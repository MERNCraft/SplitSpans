<!-- The Selection Object -->
<section
  id="the-selection-object"
  aria-labelledby="the-selection-object"
  data-item="The Selection Object"
>
<h2><a href="#the-selection-object">The Selection Object</a></h2>

I'll start by introducing two leading characters in the story that will unfold: the Selection and the Range object.

### The Selection object

From the moment you launch a web page, the browser window has a [Selection](https://developer.mozilla.org/en-US/docs/Web/API/Selection) object, which JavaScript gives you access to. The official way to talk to it is as `window.getSelection()`, but throughout this tutorial, I will be assigning this to a variable, so you can call it `selection`, for short.

When you drag to select an image or some text in a web page, the `selection` updates internally, to reflect whatever is currently selected. When you select text, the plain text version of what you selected is available through `selection.toString()`. Indeed, if you just `console.log(selection)`, what you will see is a string of plain text (or perhaps an empty string, if no text is selected.)

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
  height="360"
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

You can find the full 


<details class="solution">
<summary>Try this at home</summary>

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
    pre {
      background-color: #ccc;
      min-height: 1em;
    }
    div::before {
      content: "DIV >>>"
    }
    div::after {
      content: "<<< DIV"
    }
    #feedback {
      -webkit-user-select: none;
      user-select: none;
    }
  </style>
</head>
<body>
  <p id="one">Click and drag       to make a selection 👍🏼</p>
  <p id="two">Your selection can <i>start</i> on one line and <i>end</i> on another.</p>
  <div>
    <img src="https://picsum.photos/32/32" alt="picsum image">
  </div>
  <input type="text" placeholder="text input">
  <pre id="feedback"></pre>

  <script>
    const feedback = document.getElementById("feedback")
    document.body.addEventListener("mousemove", showSelection)
    document.body.addEventListener("mouseup", showSelection)

    function showSelection() {
      const selection = window.getSelection()
      if (!selection.rangeCount) {
        feedback.innerText = "selection.rangeCount: 0"
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
      
      feedback.innerText = `'${selection}'
range.startContainer.nodeName:      ${startName}
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

The window's Selection object is created as soon as the page loads. As you can see in the script of your HTML page, you can get a pointer to it using `window.getSelection()`.

If there is something selected on the page, the Selection object will have at least one Range object. But as the next image shows, when the page is first loaded, before the first click, nothing is selected, and the selection has no Range objects to share. Its `rangeCount` is initially zero.

```js-#37
const selection = window.getSelection()
if (!selection.rangeCount) {
  feedback.innerText = "selection.rangeCount: 0"
  return
}
```

![Page just loaded](images/just_loaded.webp)

### Best Supporting Roles...

If you double-click on the word 'Click' or click and drag to select it, you'll see in the grey feedback zone that a Range object is created. It has four properties that you will be using a lot:

* `startContainer`
* `startOffset`
* `endContainer`
* `endOffset`

![Selecting the word 'Click'](images/click_selected.webp)

You might have two surprises here. First, the range mentions _two container_ objects: `startContainer` and `endContainer`, although you know that the word 'Click' is entirely contained in one `<p>` element. Second, both the _nodeName_ of both the `startContainer` and the `endContainer` is given as `#text`, not as `<p>`. The element you created as `<p id="one">` is shown as the `range.startContainer.parentElement`.

You can see the `parentElement`'s full title and more details of the range object, if you place a breakpoint in the Debugger:

![Add a breakpoint after the range variable is set...](images/breakpoint.webp)

![...then observe the range object in the Debugger](images/debugging_range.webp)

### What's a #text node?

An HTML element, like a `<p>` element, can contain more than just text. It can also contain inline content elements like `<img>`, `<input>` or `<select>`, or inline formatting elements like `<i>` and `<b>`. Everything between these inline elements, and all text inside them, is treated as a `text node`. Even the gaps between HTML elements in the HTML page are considered to be text nodes. Here's an example. All the highlighted characters in the HTML code below are in text nodes, including the invisible spaces and carriage returns.

```html-#
<i><p><u>↵
  </u><span><u>text node in a child span</u></span><u>↵
  text node in the top-level paragraph↵</u>
</p></i>
```

<details class="alert" open>
<summary>White space</summary>
By default, all consecutive white space in text nodes is collapsed into a single space (` `) character, so you may not be aware of how much white space there actually is. But you'll need to pay attention: soon you'll be having to count every last white space character, if you want to get your text selection tool to work well.

Notice, for example, how this line in your HTML file contains many spaces between "drag" and "to"...

```html-#72
<p id="one">Click and drag       to make a selection 👍🏼</p>
```

... but this line in the rendered page shows only one:<br><br>


![Only one space rendered between "drag" and "to"](images/one_space.webp)

</details>


### startOffset and endOffset

When the word 'Click' is selected, the `startOffset` is `0` while the `endOffset` is set to `5`.

You should be used to JavaScript counting from 0. Here, it is saying that your selection starts when there are 0 characters to the left, and it ends after the 5-letter word 'Click', when there are 5 characters to the left.

On the page, the distance between the start of the letter "g" in "drag" and the end of the letter "t" in "two", looks like it should give an offset difference of `3`, but the `range` object is looking at the source HTML, and it keeps a different score:

![One visual space can be many HTML white space characterss](images/22-13.webp)


![An emoji can count as several characters](images/emoji_selected.webp)



![The alt text of an image as seen by Selection](images/image_selected.webp)



![When startContainer and endContainer are different](images/two_lines.webp)


![Container.parentElements of different types](images/start_in_i_tag.webp)


![Text that can't be selected by Selection](images/input_selected.webp)


![Everything on the page is "selected"... or is it?](images/all_selected.webp)


![A click to the right of the input field: no text selected](images/all_deselected.webp)


![A click collapses the Selection to an invisible caret](images/invisible_caret.webp)

</section>