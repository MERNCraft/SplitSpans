<!-- Word Boundaries -->
<section
  id="word-boundaries"
  aria-labelledby="word-boundaries"
  data-item="Word Boundaries"
>
  <h2><a href="#word-boundaries">Word Boundaries</a></h2>

Where does one word end and the next word begin? Since your early childhood, you have been able to detect word boundaries in spoken speech. Try asking a two-year old to say "speedboat". (Make sure you pronounce the "d".) The chances are that the child will answer with two words with a distinct gap between them: "speed ... boat". That's because the sequence "db" is very unusual in English words. Before children can say the words "statistical probability", their brains are busy analysing the sounds they hear according to statistical probability.

You need to take a quick glance at these two sentences to know that they are different, and that the words need to be pronounced differently, possibly with a different accent:

> The irregular program mess ends now.
> 
> Their regular programmes send snow.

But both sentences use the exact same letters in the exact same order, and only the position of the spaces change. Your brain can immediately see the differences, and may not even notice the similarity until it is pointed out to you.

In this project, which aims to to ensure that every selection starts and ends with a whole word, you'll somehow need to explain to your browser what a whole word is, even when there are HTML tags to confuse it.

Does the following HTML seem clear to you, now, even when you know what you are looking for?

```html-w
&lt;p id="source"&gt;&lt;i class="after"&gt;The&lt;u&gt; &lt;/u&gt;ir&lt;/i&gt;&lt;b&gt;regular program&lt;u&gt; &lt;/u&gt;mes&lt;/b&gt;&lt;i class="before after"&gt;s&lt;u class="space"&gt; &lt;/u&gt;end&lt;/i&gt;s&lt;u class="space"&gt; &lt;/u&gt;now.&lt;/p&gt;
```

### Enter `Intl.Segmenter`

A new API was [recently](https://caniuse.com/?search=Intl.Segmenter) added to JavaScript as a global object in all major browsers: [Intl.Segmenter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter).

Below is a demo that uses the Intl.Segmenter to parse the text of the HTML chunk just above. Here is the code that creates the segment objects that you see in the demo:

```javascript-#77
      const source = document.getElementById("source");
```
```js-s
...
```
```javascript-#84
        const text = source.textContent;

        const lang = "en";
        const options = { granularity: "word" };
        const segmenter = new Intl.Segmenter(lang, options);
        const words = Array.from(segmenter.segment(text));
```

Note that you need to tell the Intl.Segmenter instance what `lang`uage the input is going to be in, and what `granularity` you want to use.

If you click on the checkbox in the green section at the top, a series of rules are applied to the different `<i>` and `<u>` blocks within that HTML chunk, in order to force the text to _appear_ differently in the browser. The HTML itself does not change.

However, the Intl.Segmenter cuts through the formatting noise, and just reads the raw `textContent` of the `<p id="source">` element. It ignores any CSS formatting that may or may not be applied. As a result, as you can see, it produces exactly the same `segments` whether the checkbox is checked on not. 

<iframe
  id="intl-segmenter"
  title="intl-segmenter"
  width="480"
  height="480"
  src="https://merncraft.github.io/Intl-Segmenter">
</iframe>

<details class="tldr">
<summary>Try This at Home</summary>

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Word Boundaries</title>
    <style>
      body {
        display: flex;
        flex-direction: column;
        margin: 0;
        height: 100vh;
        color: #ddd;
        background-color: #111;

        label {
          padding: 1em;
          background-color: #060;
        }

        #source {
          font-size: 1.5em;
          color: #0c0;
        }

        u {
          text-decoration: none;
        }

        &:has(input:checked) {
          & #source {
            color: #c00;

            /* use non-breaking spaces to force letters apart */
            i.after {
              &::after {
                content: "\00A0";
                display: inline-block;
              }
            }
            i.before::before {
              content: "\00A0";
              display: inline-block;
            }
            u {
              font-size: 0;
            }
          }
        }

        #segments {
          flex: 1;
          overflow-y: auto;
        }

        pre {
          white-space: pre-wrap;
          
          &::first-line {
            font-size: 1.5em;
          }
        }
      }
    </style>
  </head>
  <body>
    <label>
      <span>Use confusing CSS to change the spacing: </span>
      <input type="checkbox" id="checkbox">
    </label>
    <p id="source">&lt;i class="after"&gt;The&lt;u&gt; &lt;/u&gt;ir&lt;/i&gt;&lt;b&gt;regular program&lt;u&gt; &lt;/u&gt;mes&lt;/b&gt;&lt;i class="before after"&gt;s&lt;u class="space"&gt; &lt;/u&gt;end&lt;/i&gt;s&lt;u class="space"&gt; &lt;/u&gt;now.&lt;/p&gt;
    <pre id="words"></pre>
    <pre id="segments"></pre>

    <script>
      const checkbox = document.getElementById("checkbox");
      const source = document.getElementById("source");
      const wordsPre = document.getElementById("words");
      const segmentsPre = document.getElementById("segments");

      checkbox.addEventListener("change", segmentText)

      function segmentText() {
        const text = source.textContent;

        const lang = "en";
        const options = { granularity: "word" };
        const segmenter = new Intl.Segmenter(lang, options);
        const words = Array.from(segmenter.segment(text));
        const justWords = words
          .filter((item) => item.isWordLike)
          .map((item) => item.segment);

        wordsPre.textContent = `words:
${JSON.stringify(justWords, null, "  ")}`;
        segmentsPre.textContent = `segments:
${JSON.stringify(words, null, "  ")}`;
      }

      segmentText()
    </script>
  </body>
</html>
```

</details>

First, I'll show you how to create a mental image of what Intl.Segmenter does, and then you can practise getting it to work together with the Selection, Range and container objects that you have already seen.

</section>