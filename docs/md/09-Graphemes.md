<!-- Intl.Segmenter granularity -->
<!-- Graphemes -->
<section
  id="graphemes"
  aria-labelledby="graphemes"
  data-item="Graphemes"
>
  <h2><a href="#graphemes">Graphemes</a></h2>

The [second parameter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter/Segmenter#parameters) that you can use when you create a new Intl.Segmenter instance is an object, which allows you to define the _granularity_ and a _localeMatcher_.

<details class="pivot" open>
<summary>Further reading</summary>
I won't treat _localeMatcher_ here, but you can find more information about this in [the entry for Intl on the MDN web site](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl#locale_identification_and_negotiation).

</details>

### What is a `grapheme`?

A grapheme is not a letter, and it is not a Unicode code point. It is a visual representation of something meaningful.

Here are three emojis:

<p style="font-size: 5em; text-align: center">🌘🐈🐈‍⬛</p>

(You could imagine that this means: "At night, all cats are grey.")

Each emoji is a grapheme. Try this:

1. Copy the moon and cats emojis
2. Open the Console in your browser
3. Type `e = "🌘🐈🐈‍⬛"`
4. Type `s = new Intl.Segmenter()`
   (Note that, by default, Intl.Segmenter uses "grapheme" for its granularity.)
5. Type `Array.from(s.segment(e))`
   (I'll explain the `Array.from()` in a moment.)

You will probably see something like this:

![A tabby cat and a black cat become two tabby cats and a black square](images/cats.webp)

6. Now type this: `e.split("")`

Here's the output I get:
   
```node
[ '\ud83c', '\udf18', '\ud83d', '\udc08', '\ud83d', '\udc08', '‍', '⬛' ]
```

### Surrogate characters

Unicode uses `UTF-16`, which uses the numbers `0` to `2^16 - 1` (65535). That's 65536 numbers, but Chinese has over 70000 characters all of its own.

To create extra place to which to assign all the different symbols that humans currently communicate with, Unicode uses a trick [described in detail here](https://www.johndcook.com/blog/2025/03/09/unicode-surrogates/). In short, it defines a set of high-value 16-bit numbers as _surrogates_, and decides that these numbers _on their own_ have no meaning, but _when used in pairs_ take on a specific meaning.

In your browser console, try:

7. `[ '\ud83c', '\udf18' ].join("")`

Do you see a crescent moon? But `'\ud83c'` on its own, is just an echo of itself.

For fun, try: 

8. `[ '\ud83c', '\udf17' ].join("")`

Do you see the moon in its third quarter?

And a longer sequence like...

9. `[ '\ud83d', '\udc08', '‍', '⬛' ].join("")`

... can produce a cat-which-is-black in your browser page.

<details class="note" open>
<summary>Zero-Width Joiner (ZWJ)</summary>
Note that the character `'‍'` in the array above is not an empty string. It's a [zero-width joiner](https://en.wikipedia.org/wiki/Zero-width_joiner), and it's used to indicate that two separate characters should be treated as one.

When writing in Hindi, for instance, this sequence...

> [ka `क`] [virāma ्] [ZWJ] [ṣa `ष`]

... is interpreted as this single character:

> `क्‍ष`

In the black cat emoji, the zero-width joiner is used to indicate that the cat emoji and the black colour character should be combined to make a single black cat grapheme.

Here's how you can see that `'‍'` is not an _empty_ string, and that the black square character is a string like any other.

```node
> '‍'.charCodeAt(0)
8205
> '⬛'.charCodeAt(0)
11035
```

</details>

### Non-emoji multiple-character graphemes

You've seen that a black cat is defined as four different Unicode code points. The same technique is applied to create a single grapheme from separate characters in many languages. Here, for example is how the first word of a sentence in Tamil (translation: "I am going to school.") is segmented into graphemes by Intl.Segmenter:

    
![A Tamil sentence segmented into graphemes](images/tamil.webp)

At first glance, you might think that the first word (pronounced "Nāṉ") is made of three or maybe four characters, but Intl.Segmenter knows that these should be combined to form only two graphemes.

![The first word has two letters; the first letter (Nā) is in two parts](images/tamil_word.webp)

If you use the standard JavaScript `.split` String method, you will indeed see four characters...

```node
> "நான்".split("")
[ 'ந', 'ா', 'ன', '்' ]
```
... but Intl.Segmenter combines them the way a native Tamil speaker would:

```node
> Array.from(s.segment("நான்"))
[
  { segment: 'நா', index: 0, input: 'நான்' },
  { segment: 'ன்', index: 2, input: 'நான்' }
]
```

If you are not a native Tamil speaker, it's good to be able to trust Intl.Segmenter to help you understand what to do. And the advantage is that Intl.Segmenter provides this help for many many languages, including the ones that you already master, so you have one ready-made solution that you can use and re-use.

</section>