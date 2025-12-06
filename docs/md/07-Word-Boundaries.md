<!-- Word Boundaries -->
<section
  id="word-boundaries"
  aria-labelledby="word-boundaries"
  data-item="Word Boundaries"
>
  <h2><a href="#word-boundaries">Word Boundaries</a></h2>

Where does one word end and the next word begin?

Since your early childhood, you have been able to detect word boundaries in spoken speech. Try asking a two-year old to say "speedboat". (Make sure you pronounce the "d".) The chances are that the child will answer with two words, with a distinct gap between them: "speed ... boat". That's because the sequence "db" is very unusual in English words. Before children can say the words "statistical probability", their brains are busy analysing the sounds they hear according to statistical probability. And their brains tell them that "db" is very rare in the middle of a word, so it must be a word break.

But how can a web browser do this?

### Messing with white space

You only need to take a quick glance at the two sentences below to know that they are different, and that the words need to be pronounced differently, possibly with a different accent:

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

<details class="warn" open>
<summary>Work in Progress</summary>
At the time of writing, Intl.Segmenter is still not doing everything that is planned for it to do. By the time you read this, it may have improved. You might find that some of the limitations I describe here no longer cause any problems. 

</details>

Below is a demo that uses the Intl.Segmenter to parse the text of the HTML chunk just above. Here is a simplified version of the code that creates the segment objects that you see in the demo:

```javascript-#
      const source = document.getElementById("source");
      const text = source.textContent;

      const lang = "en";
      const options = { granularity: "word" };
      const segmenter = new Intl.Segmenter(lang, options);
      const segments = Array.from(segmenter.segment(text));
```

Note that you need to tell the Intl.Segmenter instance what `lang`uage the input is going to be in, and what `granularity` you want to use. For language, it makes sense to use "en" (for English).

The values for `granularity` are _grapheme_, _word_ and _sentence_. In English, using _grapheme_ will split your text into individual characters: each letter, space and punctuation mark will be treated separately. When dealing with emoji or certain other writing systems, a grapheme corresponds to a single visual item which is never divided into its constituent parts.

If you click on the checkbox in the green section at the top, a series of rules are applied to the different `<i>` and `<u>` blocks within the HTML chunk shown above. This will force the text to _appear_ differently in the browser. The HTML itself does not change.

However, the Intl.Segmenter ignores all the formatting, and just reads the raw `textContent` of the `<p id="source">` element. As a result, as you can see, it produces exactly the same `segments` whether the checkbox is checked on not. 

<iframe
  id="intl-segmenter-demo"
  title="intl-segmenter"
  width="480"
  height="800"
  src="https://merncraft.github.io/Intl-Segmenter">
</iframe>

<details class="tldr">
<summary>Try This at Home</summary>

To create the same demo on your own computer, you can clone the [GitHub repository](https://github.com/MERNCraft/Intl-Segmenter), or follow the instructions below.

1. Greate a file called `Intl_Segmenter.html` 
2. Copy and paste the HTML shown below into it
3. Open the file in your Browser

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

        & div#settings {
          background-color: #060;
          padding: 0 1em 1em;

          #use-css {
            opacity: 0.5;
            pointer-events: none;

            &.active {
              opacity: 1;
              pointer-events: all;
            }
          }

          & > label,
          & details > label {
            display: block;
            width: 100%;
            text-align: center;
            margin-top: 1em;
          }

          & details {
            & div {
              text-align: center;
              margin-top: 1em;

              &.language {
                display: flex;
                justify-content: center;
                align-items: center;

                input[type=radio] {
                  position: absolute;
                  left: -999vw;

                  &:checked ~ img {
                    opacity: 1;
                    border-color: white;
                    box-shadow: 0 0 10px white;
                  }
                }
                & label img {
                  width: 1.5em;
                  margin-left: 0.5em;
                  opacity: 0.75;
                  border: 1px solid transparent;
                  border-radius: 1em;
                }
              }
            }
          }
        }


        & p[lang] {
          font-size: 1.2em;
          color: #0c0;
          text-align: center;
          display: none;
        }

        u {
          text-decoration: none;
        }

        &:has(#checkbox:checked) {
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

        #identical {
          color: #0c0;
          text-align: center;

          &:empty {
            display: none;
          }
        }
      }

      .selected {
        display: block!important
      }
    </style>
  </head>

  <body>
    <div id="settings">
      <label id="use-css">
        <span>Use CSS to change the spacing:</span>
        <input type="checkbox" id="checkbox">
      </label>

      <details id="details">
        <summary>More</summary>

        <div class="language">
          <span>Text in:</span>
          <label>
            <input type="radio" name="language" id="en" checked>
            <img src="images/en-GB.png" checked alt="English" title="English">
          </label>
          <label>
            <input type="radio" name="language" id="am" >
            <img src="images/am.png" alt="አማርኛ" title="አማርኛ Amharic">
          </label>
          <label>
            <input type="radio" name="language" id="ar">
            <img src="images/ar.png" alt="عربي" title="عربي Arabic">
          </label>
          <label>
            <input type="radio" name="language" id="bn">
            <img src="images/bn.png" alt="বাংলা" title="বাংলা Bengali">
          </label>
          <label>
            <input type="radio" name="language" id="fr">
            <img src="images/fr-FR.png" alt="Français" title="Français French">
          </label>
          <label>
            <input type="radio" name="language" id="hi">
            <img src="images/hi.png" alt="हिंदी" title="हिंदी Hindi">
          </label>
          <label>
            <input type="radio" name="language" id="iu">
            <img src="images/iu.png" alt="ᐃᓄᒃᑎᑐᑦ" title="ᐃᓄᒃᑎᑐᑦ Inuktut">
          </label>
          <label>
            <input type="radio" name="language" id="ja">
            <img src="images/ja.png" alt="日本語" title="日本語 Japanese">
          </label>
          <label>
            <input type="radio" name="language" id="ko">
            <img src="images/ko.png" alt="한국인" title="한국인 Korean">
          </label>
          <label>
            <input type="radio" name="language" id="ru">
            <img src="images/ru.png" alt="Русский" title="Русский Russian">
          </label>
          <label>
            <input type="radio" name="language" id="ta">
            <img src="images/ta.png" alt="தமிழ்" title="தமிழ் Tamil">
          </label>
          <label>
            <input type="radio" name="language" id="th">
            <img src="images/th.png" alt="ไทย" title="ไทย Thai">
          </label>
        </div>

        <div class="options">
          new Intl.Segmenter ({
          <label>
            <span>lang:</span>
            <select id="lang">
              <option value="en" selected>en</option>
              <option value="am">am</option>
              <option value="ar">ar</option>
              <option value="bn">bn</option>
              <option value="fr">fr</option>
              <option value="hi">hi</option>
              <option value="ja">ja</option>
              <option value="ko">ko</option>
              <option value="iu">iu</option>
              <option value="ru">ru</option>
              <option value="ta">ta</option>
              <option value="th">th</option>
            </select>
          </label>
          ,
          <label>
            <span>granularity:</span>
            <select id="granularity">
              <option value="grapheme">grapheme</option>
              <option value="word" selected>word</option>
              <option value="sentence">sentence</option>
            </select>
          </label>
          })
        </div>

        <label>
          <span>Compare segmentation across all languages</span>
          <input type="checkbox" id="check-all">
        </label>
      </details>
    </div>
    <p id="source" lang="en" class="selected"><i class="after">The<u> </u>ir</i><b>regular program<u> </u>mes</b><i class="before after">s<u class="space"> </u>end</i>s<u class="space"> </u>now.</p>
    <p lang="am">አማርኛ ፡ የኢትዮጵያ ፡ መደበኛ ፡ ቋንቋ ፡ ነው ። ሴማዊ ፡ ቋንቋዎች ፡ ውስጥ ፡ የሚመደብ ፡ ሲሆን ፡ ካረቢኛ ፡ ቀጥሎ ፡ ሁለተኛ ፡ ብዙ ፡ ተናጋሪዎች ፡</p>
    <p lang="ar">العربية لغةٌ رسمية في كل دول الوطن العربي</p>
    <p lang="bn">বাংলা সার্বভৌম ভাষাভিত্তিক জাতিরাষ্ট্র বাংলাদেশের একমাত্র রাষ্ট্রভাষা তথা সরকারি ভাষা।</p>
    <p lang="fr">Les premières occurrences du mot « France » en langue française se rencontrent au XIe siècle.</p>
    <p lang="hi">हिन्दी संवैधानिक रूप से भारत की राजभाषा और भारत की सबसे अधिक बोली और समझी जाने वाली भाषा है।</p>
    <p lang="ja">日本において、国号を直接かつ明確に規定した法令は存在しない。</p>
    <p lang="ko">한국어(韓國語) 또는 조선어(朝鮮語)는 대한민국과 조선민주주의인민공화국의 공용어이다. 둘은 표기나 문법, 동사 어미, 표현에서 약간의 차이가 있다.</p>
    <p lang="iu">ᐃᓅᔪᓕᒫᑦ ᐊᓂᖅᑎᕆᔪᓕᒫᑦ ᐃᓅᓚᐅᕐᒪᑕ ᐃᓱᒪᕐᓱᕐᓚᑎᒃ ᐊᒻᒪᓗ ᐊᔾᔨᐅᖃᑎᒦᒃᓗᑎᒃ ᓂᕐᓱᐊᖑᓂᒃᑯᑦ ᐊᒻᒪᓗ ᐱᔪᓐᓀᑎᑎᒍᑦ.</p>
    <p lang="ru">Россия — многонациональное государство с широким этнокультурным многообразием.</p>
    <p lang="ta">நான் பள்ளிக்குச் சென்று வருகிறேன்.</p>
    <p lang="th">ประเทศไทยมีประชากรเกือบ 66 ล้านคน พื้นที่ประมาณ 513,115 ตารางกิโลเมตร</p>

    <pre id="words"></pre>
    <p id="identical"></p>
    <pre id="segments"></pre>


    <script>
      const useCSS = document.getElementById("use-css")
      const checkbox = document.getElementById("checkbox")
      const details = document.getElementById("details")
      const triggers = Array.from(
        document.querySelectorAll("[name=language]")
      )
      const checkAll = document.getElementById("check-all")
      const select = document.getElementById("lang")
      const granularity = document.getElementById("granularity")
      triggers.push(checkAll)
      triggers.push(select)
      triggers.push(granularity)

      const wordsPre = document.getElementById("words")
      const identical = document.getElementById("identical")
      const segmentsPre = document.getElementById("segments")
      const phrases = Array.from(
        document.querySelectorAll("p[lang]")
      )
      const langs = phrases.map(
        phrase => phrase.getAttribute("lang")
      )

      let languageId

      checkbox.addEventListener("change", segmentText)
      details.addEventListener("toggle", toggleDetails)
      triggers.forEach( trigger => {
        trigger.addEventListener("change", setLanguage)
      })


      function segmentText() {
        const selector = `p[lang=${languageId}]`
        const source = document.querySelector(selector)
        const text = source.textContent

        const lang = select.value
        const options = { granularity: granularity.value }
        const segmenter = new Intl.Segmenter(lang, options)
        const segments = Array.from(segmenter.segment(text))

        let items = segments
          .filter((item) => item.isWordLike)
          .map((item) => item.segment)

          if (items.length) {
          items = `words:
${JSON.stringify(items, null, "  ")}`
        } else {
          const count = segments.length
          const s = count === 1 ? "" : "s"
          items = `${segments.length} ${granularity.value}${s} found`
        }

        wordsPre.textContent = items
        segmentsPre.textContent = `segments:
${JSON.stringify(segments, null, "  ")}`

        if (checkAll.checked) {
          checkAllLanguageSettings(text)

        } else {
          identical.textContent = ""
        }
      }


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


      function toggleDetails({ target }) {
        if (!target.open) {
          // When details or closed, stop checking all languages...
          checkAll.checked = false
          // ... check only English...
          select.value = "en"
          // ... segment into words...
          granularity.value = "word"
          // ... and select the English text...
          target = triggers[0]
          target.checked = true
          // ... just as it was after the page first loaded
          setLanguage({ target })
        }
      }


      function setLanguage({ target }) {
        const { id, tagName } = target
        if ( id !== "check-all"
          && id !== "lang"
          && id !== "granularity"
           ) {
          languageId = id
        }
        phrases.forEach( phrase => {
          const action = (phrase.getAttribute("lang") === languageId)
            ? "add"
            : "remove"
          phrase.classList[action]("selected")
        })

        const action = (id === "en")
          ? "add"
          : "remove"
          useCSS.classList[action]("active")

        segmentText()
      }


      const target = document.querySelector("[type=radio]:checked")
      setLanguage({ target })

    </script>
  </body>
</html>
```

</details>

As before, you might like to open this page in a different browser window, so that you can interact with this demo as you continue reading this tutorial.

</section>