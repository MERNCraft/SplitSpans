<!-- The Selection Object -->
<section
  id="the-selection-object"
  aria-labelledby="the-selection-object"
  data-item="The Selection Object"
>
<h2><a href="#the-selection-object">The Selection Object</a></h2>

Before you can master a field of knowledge, you need to be able create clear mental images, which are not too different from the reality that you need to deal with. One good way to do this is with hands-on manipulation, where you can get immediate feedback on the effect of the changes that you make.

I'll start by introducing two leading characters in the story that will unfold: the [Selection](https://developer.mozilla.org/en-US/docs/Web/API/Selection) and the [Range](https://developer.mozilla.org/en-US/docs/Web/API/Range) object. And then I'll show you the interplay between them and their supporting cast: [range.startContainer](https://developer.mozilla.org/en-US/docs/Web/API/Range/startContainer) with its companion [range.startOffset](https://developer.mozilla.org/en-US/docs/Web/API/Range/startOffset), together with [range.endContainer](https://developer.mozilla.org/en-US/docs/Web/API/Range/endContainer) with its companion [range.endOffset](https://developer.mozilla.org/en-US/docs/Web/API/Range/endOffset).

### The Selection object

From the moment you launch a web page, the browser window has a [Selection](https://developer.mozilla.org/en-US/docs/Web/API/Selection) object, which JavaScript gives you access to. The official way to talk to it is as `window.getSelection()`, but throughout this tutorial, I will be assigning this to a variable, so you can call it `selection`, for short.

When you drag to select an image or some text in a web page, the `selection` updates internally, to reflect whatever is currently selected. When you select text, the plain text version of what you selected is available through `selection.toString()`. Indeed, if you just `console.log(selection)`, what you will see is a string of plain text (or perhaps an empty string, if no text is selected.)

This is what is showen at the top of grey feedback section 

### The Range object

Each selected section is in fact stored in a [Range](https://developer.mozilla.org/en-US/docs/Web/API/Range) object. In browsers that support multiple Range objects, there can be many separate selected areas on the page.

### Selective Vision?

However, the Selection object does not have access to everything that you see on a page. For example, `selection` has no access to:

* Pseudo-elements, like `::before` or `::after`, or any `::marker` for a list item
* The text of `<input>` elements, `<select>` elements and `<textarea>` elements 
* Any elements with a CSS styling of `user-select: none`.

The selection **can** however contain formatting tags and their content, like `This chunk with <i>italic</i> text.` **Note** that `selection.toString()` will show only plain text, even if the `selection` does in fact hold formatted text.

### See for yourself

To get an idea of what `selection` can and cannot see, click and drag to make some selections in the demo below, and watch how the text in the grey feedback zone changes.


<iframe
  id="selection-and-range"
  title="Selection and Range"
  width="480"
  height="480"
  src="https://merncraft.github.io/SelectionAndRange/">
</iframe>

Try selecting different parts of the page:

* The text at the top
* The emoji
* Both lines of text together
* The words "DIV"
* The image
* The text in italics... and some text to either side of it
* The text in the input field
* The text in the grey feedback zone that displays what the Selection object is aware of
* The entire contents of the page

Also, try simply clicking somewhere in the text without making any selection.

> You can find the full script of this demo in the _Try This at Home_ section below. I'll be quoting extracts of the script in what follows, so that you can see how the code works.

<details class="solution">
<summary>Try This at Home</summary>

To create the same demo on your own computer, you can clone the [GitHub repository](https://github.com/MERNCraft/SelectionAndRange), or follow the instructions below.

1. Greate a file called `index.html` 
2. Copy and paste the HTML shown below into it
3. Open the file in your Browser
   
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <title>getSelection</title>
  <style>
    body {
      color: #ddd;
      background-color: #111;
    }
    div#image {
      text-align: center;
      img {
        vertical-align: middle;
      }
      &::before {
        content: "::before\00a0\00a0";
      }
      &::after {
        content: "\00a0\00a0::after"
      }
    }
    input {
      margin: 1em 0;
      width: 100%;
    }
    pre {
      background-color: #333;
      min-height: 1em;
      margin-top: 0;
      white-space: pre-wrap;
    }
    pre,
    .feedback {
      -webkit-user-select: none;
      user-select: none;

      & span {
        display: inline-block;
        font-family: monospace;
        text-decoration: underline;
        width: 100%;
        text-align: center;
      }
    }
  </style>
</head>
<body>
  <p id="one">Click and drag       to make a selection 👍🏼</p>
  <p id="two">Your selection can <i>start</i> on one line and <i>end</i> on another.</p>
  <div id="image">
    <img
      src="https://picsum.photos/48/48"
      alt="random image from https://picsum.photos/48/48"
    >
  </div>
  <input type="text" placeholder="text input">
  <div class="feedback">
    <span>selection.toString()</span>
    <pre id="selection"></pre>
  </div>
  <div class="feedback">
    <span>selection.getRangeAt(0)</span>
    <pre id="range"></pre>
  </div>

  <script>
    let ii = 0
    const selectionP = document.getElementById("selection")
    const rangeP = document.getElementById("range")

    const selection = window.getSelection()

    setInterval(showSelection, 100)

    function showSelection() {
      if (!selection.rangeCount) {
        selectionP.innerText = "selection.rangeCount: 0"
        rangeP.innerText = ""
        return
      }

      const range = selection.getRangeAt(0)

      const start = range.startContainer
      const startName = start.nodeName
      let startParent = range.startContainer.parentElement
      let id = startParent.id
      startParent = startParent.tagName.toLowerCase()
                  + (id ? `#${id}` : "")

      const end = range.endContainer
      const endName = end.nodeName
      let endParent = range.endContainer.parentElement
      id = endParent.id
      endParent = endParent.tagName.toLowerCase()
                + (id ? `#${id}` : "")

      selectionP.innerText = selection

      rangeP.innerText =
`range.startContainer.nodeName:      ${startName}
range.startContainer.parentElement: ${startParent}
range.startOffset:                  ${range.startOffset}

range.endContainer.nodeName:        ${endName}
range.endContainer.parentElement:   ${endParent}
range.endOffset:                    ${range.endOffset}`
    }
  </script>
</body>
</html>
```


</details>

### Observing the Selection and its Range object

The window's Selection object is created as soon as the page loads. As you can see in the script in the Try This at Home section, you can get a pointer to it using `window.getSelection()`.

If there is something selected on the page, the Selection object will have at least one Range object. The number of Range objects currently in use is given by `selection.rangeCount`.

But as the next script extract and image show, when the page is first loaded, before the first click, nothing is selected, and the selection has no Range objects to share. Its `rangeCount` is initially zero.

```js-#71
<b>    const selection = window.getSelection()</b>

<i>    setInterval(showSelection, 100)

    function showSelection() {</i>
<b>      if (!selection.rangeCount) {
        selectionP.innerText = "selection.rangeCount: 0"</b><i>
        rangeP.innerText = ""
        return
      }</i>
```
```js-s
...
```
```js-#108
    <i>}</i>
```

![`selection.rangeCount` is 0 just after the page loaded](images/just_loaded.webp)

### Introducing the Supporting Cast...

If you double-click on the word 'Click' or click and drag to select it, you'll see in the grey feedback zone that a Range object is created. It has four properties that you will be using a lot:

* [`startContainer`](https://developer.mozilla.org/en-US/docs/Web/API/Range/startContainer)
* [`startOffset`](https://developer.mozilla.org/en-US/docs/Web/API/Range/startOffset), together with 
* [`endContainer`](https://developer.mozilla.org/en-US/docs/Web/API/Range/endContainer)
* [`endOffset`](https://developer.mozilla.org/en-US/docs/Web/API/Range/endOffset)

![Selecting the word 'Click'](images/click_selected.webp)

You might have two surprises here. First, the range has _two container_ objects: `startContainer` and `endContainer`, although you know that the word 'Click' is entirely contained in _one_ `<p>` element. Second, both the _nodeName_ of both the `startContainer` and the `endContainer` is given as `#text`, not as `<p>`. The element you created as `<p id="one">` is shown as the `range.startContainer.parentElement`.

You can see the `parentElement`'s full title and more details of the range object, if you create your own version of this demo, place a breakpoint in the Debugger just after the `range` variable is assigned a value:

![In your version of the demo, add a breakpoint after the range variable is set...](images/breakpoint.webp)

![...then observe the range object in the Debugger](images/debugging_range.webp)

### What's a #text node?

An HTML element, like a `<p>` element, can contain more than just text. It can also contain inline content elements like `<img>`, `<input>` or `<select>`, or inline formatting elements like `<i>` and `<b>`. Everything between these inline elements, and all text inside them, is treated as a _text node_. Even the gaps between HTML elements in the HTML page are considered to be text nodes. Here's an example. All the highlighted characters in the HTML code below are in text nodes, including the invisible spaces and carriage returns.

```html-#
<i><u>  </u><p><u>↵
    </u><span><u>text node in a child span</u></span><u>↵
    text node in the top-level paragraph↵
    </u><code><u>text node in a code block</u></code><u>↵</u>
<u>  </u></p></i>
```

<details class="alert" open>
<summary>White space</summary>
By default, all consecutive white space in text nodes is collapsed into a single space (` `) character, so you may not be aware of how much white space there actually is. But you will need to pay attention: soon you'll be having to count every last white space character, if you want to get your text selection tool to work well.

Notice, for example, how this line in your HTML file contains seven spaces between "drag" and "to"...

```html-#48
  <p id="one">Click and drag       to make a selection 👍🏼</p>
```

... but when this line is rendered in the page, it shows only one:

![Only one space _rendered_ between "drag" and "to"](images/one_space.webp)

</details>


### startOffset and endOffset

When the word 'Click' is selected, the `startOffset` is `0` while the `endOffset` is set to `5`.

You should be used to JavaScript counting from 0, so this should make good sense to you. Here, the `startOffset` is saying that your selection starts when there are 0 characters to the left in the `startContainer`, and the `endOffset` shows that after the 5-letter word 'Click', there are 5 characters to the left in the `endContainer`... which in this case is exactly the same as `startContainer`,.

### Where white space counts

Time for another experiment. In the demo above (or in the version you created for yourself), select from the last letter of "drag" to the first letter of "to".

![One visual space can be many HTML white space characterss](images/22-13.webp)

On the page, it looks like there is only one space between the start of the letter "g" in "drag" and the end of the letter "t" in "two". But the `range` object is looking at the source HTML, and it keeps a different score. It counts one letter "g", then seven spaces, and then one letter "t" — a difference of 9:

### When a character is more than it appears to be

Third experiment: select the emoji 👍🏼 and check what the values are for `startOffset` and `endOffset`.

![An emoji can count as several characters](images/emoji_selected.webp)

Surprised? The demo shows that `startOffset` is 41 and `endOffset` is 45. That suggest that the 👍🏼 is four characters long, but it only takes up one place.

### UTF-16 units

Computing as we know it today first started in the English-speaking world. In English, there are 26 letters (a-z), with 52 different signs for UPPERCASE and lowercase, plus ten digits (0-9), plus a few punctuations marks like `,;;.!?()/%$£#'"- —…`, and of course, the space. All these characters could be given numbers, with the highest number staying below 127. By coincidence, the number 127 in decimal is 1111111 in the binary system, and binary numbers are conveniently bundled in groups of 8 bits, so everything was perfect, and it was called ASCII. _American Standard Code for Information Interchange_.

And this was perfect, at least, for people using the same Latin alphabet as English.

But there is not just English in the world. There are many different writing scripts, like Arabic and the Indian Devanagari script, not to mention languages like Chinese which contain thousands of individual pictograms. To enable computers to handle all the thousands of different written symbols, the system of giving each symbol a number had to be extended.

One standard now is [UTF-16](https://en.wikipedia.org/wiki/UTF-16), which has variable-length blocks from 8 to 32 bits, and which provides enough numbers, to label the 1,112,064 different symbols from many different languages that are recognized today. And it turns out that UTF-16 still has a few numbers left over, and these are now being used for emojis.

So you might imagine that an emoji would take two character spaces, where English letters, numbers and punctuation take only one. But that is politically incorrect. Emojis that show humans and parts of humans can have different skin colours, so the 👍🏼 emoji takes two values: a generic thumbs-up plus a skin colour.

To cut a long story short: `startOffset` and `endOffset` do not count characters or bits or bytes. They count `UTF-16 units`, and the 👍🏼 emoji uses four UTF-16 units.

From now on, I'll just call the `units`, and you'll discover later just how important it is to think in _units_ rather than _characters_ when working in with other languages (and with emojis).

<details class="note" open>
<summary>Units and Spaces</summary>
The important point to note is that the Selection and Range objects use a different system for counting where a selection starts and finishes than the one that might seem familiar to you.

So now you know, and you can start to think about selections with this new mental image.

</details>

### When startContainer and endContainer are different

So far, the only selections we have looked at together have started and ended in the same container. How is a selection represented when it starts in one HTML element and finishes in another?

Let's look at two simple cases. For the first case, click in the line at the top and drag your mouse down, so that the selection starts in the first `<p>` element and ends in the second `<p>` element:

```html-#46
<i>    &lt;p id="one"&gt;Click and drag<u>       to make a selection 👍🏼&lt;/p&gt;
    &lt;p id="two"&gt;Your selection </u>can &lt;i&gt;start&lt;/i&gt; on one
    line and &lt;i&gt;end&lt;/i&gt; on another.&lt;/p&gt;</i>
 ```

![When startContainer and endContainer are different](images/two_lines.webp)

Note that `startOffset` gives you the number of units from the beginning of the text node in the `<p id="one">` paragraph, and that `endOffset` gives you the number of units from the beginning of the text node in the `<p id="two">` paragraph. Both of these containers are identified with the `nodeName` `#text`, but the identity of their `.parentElement` distinguishes them.

Specifically, notice that neither offset is measured from the beginning of the page. Each of them is relative to their container.

### Nested containers

Here's the second simple case to test. Click somewhere inside the word "start" (in <i>italics</i>), and drag the selection to somewhere outside the word, where the text is no longer in italics.

```html-#47
<i>    &lt;p id="two"&gt;Your selection </u>can &lt;i&gt;s<u>tart&lt;/i&gt; on one
    line</u> and &lt;i&gt;end&lt;/i&gt; on another.&lt;/p&gt;</i>
 ```

![Container.parentElements of different types](images/start_in_i_tag.webp)

Note that the `startContainer`'s `parentElement` is now reported as an `i` element, while the `endContainer`'s `parentElement` is reported as `p#two`. The `<p id="two">` paragraph has several top-level text nodes, separated by the text nodes inside the `<i>` tags, as the image below illustrates:

```html-#
&lt;p&gt;<u>text node</u>&lt;i&gt;<u>text node</u>&lt;/i&gt;<u>text node</u>&lt;i&gt;<u>text node</u>&lt;/i&gt;<u>text node</u>&lt;/p&gt;
```

In every case, `startOffset` and `endOffset` are calculated _from the beginning of the specific text node_ in which they occur. Be sure to note that the `parentElement` only helps identify where a given text node occurs, but its position and the length of its text are never used to `startOffset` and `endOffset`.

### Never say never. Safari has its own opinion.

Earlier, I wrote that you would look at two simple cases. Here's a third case, but it turns out not to be simple at all.


![A click collapses the Selection to an invisible caret](images/invisible_caret.webp)


![Everything on the page is "selected"... or is it?](images/all_selected.webp)


![Text that can't be selected by Selection](images/input_selected.webp)


![A click to the right of the input field: no text selected](images/all_deselected.webp)


![The alt text of an image as seen by Selection](images/image_selected.webp)

</section>