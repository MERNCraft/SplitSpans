<!-- Other Selection States -->
<section
  id="other-selection-states"
  aria-labelledby="other-selection-states"
  data-item="Other Selection Cases"
>
<h2><a href="#other-selection-states">Other Selection Cases</a></h2>

### A collapsed Selection

As you saw earlier, before you make your first click on a newly-opened web page, the Selection object already exists but it does not yet contain any Range objects. After the first Range object has been created, a simple click on the page will deselect all text, but the Range object will continue to exist. No selection will be visible on the page.

After a simple click, the Selection will simply set its `isCollapsed` property to `true`, the `startOffset` and `endOffset` will be set to the same numerical value, and the `startContainer` and `endContainer` will both refer to the same text node.
  
Try clicking on the demo page in different places. 

![A click collapses the Selection to an invisible caret](images/invisible_caret.webp)

![When a Selection isCollapsed
](images/isCollapsed.webp)

If you click outside any text content, the Selection will still be collapsed, but the range will not be a text node:

![A click to the right of the input field, not in a text node](images/all_deselected.webp)

<details class="note" open>
<summary>Selection.isCollapsed</summary>
When there is no visible selection in the page:

* The Selection's' `isCollapsed` property will be set to `true`
* The `startOffset` and `endOffset` will be set to the same numerical value
* The `startContainer` and `endContainer` will both refer to the same text node.

</details>


### A Selection with no Range object

It's possible to destroy the Range object, by selecting something that the Selection object can't directly see. Try typing some text into the `<input type="text">` field, and then select some of your input text.

![The Selection can't see text in `<input>` elements...](images/input_selected0.webp)

But perhaps you see something different? Perhaps you see that `selection.toString()` does indeed show the text that you selected in the input field:

![... or can it?](images/input_selected1.webp)

This depends on which browser you are using, and whether the selection that you made just before was collapsed or not.

<details class="note" open>
<summary>Selection inside an `<input type="text">` field</summary>
The result of selecting inside an `<input type="text">` field is not well-defined.

Depending on the browser and your most recent selection, selecting text inside an `<input type="text">` field can either:

* Destroy the Selection's Range object
* Provide a unexpected value for the `startContainer` and `endContainer`, and display the text selected in the `<input type="text">` field as `Selection.toString()`

</details>

### Browser differences when you Select All

WHen you user the Menu item Edit > Select All, or the corresponding keyboard shortcut, different browsers will display the selection differently on the page, and Selection.toString() may have different values.

Chrome and Edge mostly agree; Safari also highlights empty space to the left. Firefox does things differently:

* Firefox includes invisible carriage returns known only to the HTML
* Firefox does not highlight text in an `<input type="text">` field
* Firefox includes the _alt_ text of an image in Selection.string()

All browsers agree, though, **not** to include:

* The text of `::before` and `::after` and other pseudo-elements
* Elements with the CSS rule: `{ user-select: none }`

![Select All on Chrome](images/select_all_chrome.webp)

![Select All on Safari](images/select_all_safari.webp)

![Select All on Edge](images/select_all_edge.webp)

![Select All on Firefox](images/select_all_firefox.webp)

<details class="alert" open>
<summary>Test early, test often, test on all target browsers</summary>
You probably don't need to be reminded of this, but you can't trust all browsers to behave the same way, and you need to make sure that your code is working exactly the way you expect on all the browsers that you end-users are likely to use.

</details>
</section>