<!-- Browser Differences -->
<section
  id="browser-differences"
  aria-labelledby="browser-differences"
  data-item="Browser Differences"
>
<h2><a href="#browser-differences">Edge Cases and Browser Differences</a></h2>


Earlier, I wrote that you would look at two simple cases. Here's a third case, but it turns out not to be simple at all. The results you get will depend on which browser you are using, and even how you make the selection.

Select the words "_start_ on one line and _end_", making sure that the selection begins with the whole word "_start_" and ends with the whole word "_end_".

But try different ways of making this selection:

* Drag from the beginning of the word "_start_" to the end of the word "_end_"
* Double click on the word "_start_" then press Shift and click after the word "_end_"
* Select from the beginning of the word "_start_" to somewhere in the middle of the word "_end_", then use Shift+Right Arrow to extend the selection to the end of the word "_end_"
* Select from the end of the word "_end_" to somewhere in the middle of the word "_start_", then use Shift+Left Arrow to extend the selection to the end of the word "_start_"
* ...

Try doing this in different browsers. Here are some of the ways the `range` values may be set:

---

```html-#
... can &lt;i&gt;<u>start&lt;/i&gt; on one line and &lt;i&gt;end</u>&lt;/i&gt; on...
```

![_i 0_ to _i 3_ on Chrome](images/chrome.webp)

---

```html-#
... can <u>&lt;i&gt;start&lt;/i&gt; on one line and &lt;i&gt;end</u>&lt;/i&gt; on...
```

![_p#two 19_ to _i 3_ on Safari](images/safari.webp)

---

```html-#
... can &lt;i&gt;<u>start&lt;/i&gt; on one line and &lt;i&gt;end&lt;/i&gt;</u> on...
```

![_i 0_ to _p#two 0_ on Firefox](images/firefox.webp)

---

Selections that look exactly the same on the screen may be described differently by the `range` object, when the selection boundary is at the boundary between two containers. All of these descriptions are correct, but they all have slightly different meanings.

And try this: double-click on the 👍🏼 emoji at the end of `<p id="one">`, then press Shift+Right Arrow.

![Double click to select the emoji...](images/double_click_emoji.webp)

![... then press Shift+Right arrow to extend the selection](images/Shift_Right_Arrow.webp)

The visual appearance of the selection will not change, but the `range` object will now see that you have extended the selection to include the invisible carriage return and white space characters between the two `<p>` elements.

```html-#48
  &lt;p id="one"&gt;Click and drag       to make a selection 👍🏼&lt;/p&gt;<u>↵  
  </u>&lt;p id="two"&gt;Your selection can &lt;i&gt;start&lt;/i&gt; on one line and
```

### Need to Normalize

The point to note here is that you cannot trust the Selection data blindly. You will need to _normalize_ a selection to a specific, known state, before you start to work with it. Precisely how you normalize it will depend on how you plan to work with it.

For example, suppose you have text like this:

> Compare _short_-term and _long_-term planning

And suppose one of your end-users makes a partial selection, like this:

```html-#
Compare &lt;i&gt;short&lt;i&gt;<u>-term and &lt;i&gt;long</u>&lt;i&gt;-term planning
```

If your app is designed to ensure that whole words are selected, it will need to change the initial selection to this:

```html-#
Compare <u>&lt;i&gt;short&lt;i&gt;-term and &lt;i&gt;long&lt;i&gt;-term</u> planning
```

In this case, you will want your app to normalize the start and the end of the selection in different ways:

* You want to normalize the start of the selection to begin at the end of the first &lt;i&gt; tag, so that your app can extend the start backwards, to add "_short_" to the word "-term"
* You want to normalzie the end of the selection to the beginning of the text node following the closing &lt;/i&gt; tag, so that your app can extend the end forwards, to add "term" to the word "_long_".

In other words, here's how your app would need to normalize the user's selection before using it to extend to include whole words:

```html-#
Compare &lt;i&gt;short<u>&lt;i&gt;-term and &lt;i&gt;long&lt;i&gt;</u>-term planning
```

Other cases might require different normalization techniques.

<details class="note" open>
<summary>The lesson to learn here</summary>
Sometimes you will need to **adjust the  raw selection data** that the browser gives you, so that you can use the selection the way you want to use it.

</details>

</section>