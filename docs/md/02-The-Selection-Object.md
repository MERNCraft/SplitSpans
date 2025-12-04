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

<details class="tip" open>
<summary>Keep the demo accessible for testing</summary>
You might like to open this page in a second browser window, so that you can keep this demo available side-by-side for testing as you scroll down in this window to read the rest of the tutorial.

Alternatively, you can create your own version of the demo and open it in a separate window.

You can find the full script of this demo in the _Try This at Home_ section below. I'll be quoting extracts of the script in what follows, so that you can see how the code works.

</details>

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

You should be used to JavaScript counting from 0, so this should make good sense to you. Here, the `startOffset` is saying that your selection starts when there are 0 characters to the left in the `startContainer`, and the `endOffset` shows that after the 5-letter word 'Click', there are 5 characters <i>to the left</i> in the `endContainer`... which in this case is exactly the same as `startContainer`.

<details class="note" open>
<summary>`endOffset` is exclusive</summary>
An `endOffset` of `5` tells you that `"Click and drag..."[5]` (the character at index 5) is **not** included ind the selection. `"Click and drag..."[5]` refers to the _space_ after the end of the word `Click`, and not the "k", which is at index `4`.

`endOffset` in _exclusive_. It always refers to the first character that is **not** included in the selection.

</details>

</section>