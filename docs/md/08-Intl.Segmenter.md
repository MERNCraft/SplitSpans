<!-- Intl.Segmenter -->
<section
  id="intl-segmenter-language"
  aria-labelledby="intl-segmenter-language"
  data-item="Intl.Segmenter language"
>
  <h2><a href="#intl-segmenter">Intl.Segmenter: language</a></h2>
  
You've just seen that Intl.Segmenter can detect where words begin and end in English. _But_, you say, _that's easy. There are breaks between words when there is a space, or a punctuation mark._

"Ah!" I answer. "You're 100% sure about that? Where are the word-endings in the phrase 'the children's dogs' toys'?"

And what about languages like Japanese, Korean and Thai, where spaces are not used between words? Or languages like Arabic, which use different letter forms for the same letters if it appears at the start, in the middle, or at the end of a word?

### More

If you click on the word `> More` in the demo you've just seen, you can explore examples in 12 different languages. Most of the sentences in the demo are taken from the Wikipedia article about the language or the country in the given language. Here's how a sentence in Japanese is segmented into words:

![Splitting text into words in Japanese](images/japanese.webp)

You've already seen that that you need to tell the Intl.Segmenter what language you want to segment, as well as what _granularity_ you want. Something like this:

```js-#
const lang = "en";
const options = { granularity: "word" };
const segmenter = new Intl.Segmenter(lang, options);
```

You may be surprised (I certainly was), that choosing `"en"` for segmenting Japanese text seems to work rather well. Indeed, the results do not look very different from what you get if you choose "ja" as the language code for segmenting Japanese text:

![Using "ja" to split Japanese text into words](images/japanese_with_ja.webp)

### Identical results across language settings

Indeed, just to make sure that I wasn't missing something subtle, I added a test in my demo, to segment the text of any chosen language using a segmenter set to use _every other language_, and found that the results in all cases are identical. Check the Compare Segmentation Across All Languages checkbox to run this test for whichever language you choose.

![Using "ja" to split Japanese text into words](images/identical.webp)

The segmentation results for all the languages in this demo are
always identical, regardless of the language that the Intl.Segmenter instance is told to expect. Click below to see the test that I wrote.

<details class="tldr">
<summary>Testing segmentation with Segmenters in all languages</summary>

You can find the full text of the HTML page at [GitHub repository](https://github.com/MERNCraft/Intl-Segmenter).

```js-#318
      function checkAllLanguageSettings(text) {
        // Segment the current text using all values of lang
        const sets = langs.map( lang => {
          const options = { granularity: granularity.value }
          const segmenter = new Intl.Segmenter(lang, options)
          const segments = Array.from(segmenter.segment(text))
          segments.lang = lang

          return segments
        })

        // Pair each segmentation with one from a different lang
        const pairs = sets.reduce(( pairs, original, index) => {
          const compare =  (index)
            ? sets[index - 1]
            : sets[sets.length - 1]

            pairs.push([ original, compare ])
          return pairs
        }, [])

        // If every pair contains identical segmentations, then
        // all the segmentations are identical.
        const same = pairs.every(([ original, compare ]) => (
          JSON.stringify(original) === JSON.stringify(compare)
        ))

        identical.innerHTML = `Segmentation results with all language settings are: <b>${same ? "identical" : "different"}</b>`
      }
  ```

</details>

<details class="note" open>
<summary>More to come?</summary>
The [MDN documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter/Segmenter#parameters) indicates that this parameter is _optional_.

Perhaps this is a sign that Intl.Segmenter is able to recognize what segmentation rules to apply based on the character codes usend in the text being segmented... in which case it is already doing an excellent job, and no language setting is unnecessary.

Perhaps the language setting has been included now to allow for more fine-tuned segmentation in the future. In this case, it would make make sense to **provide the correct language setting**, so that your code can profit from better segmentation in future browsers.

</details>

</section>