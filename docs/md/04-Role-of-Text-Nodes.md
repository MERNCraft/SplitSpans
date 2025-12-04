<!-- Different Containers -->
<!-- Role of Text Nodes -->
<section
  id="role-of-text-nodes"
  aria-labelledby="role-of-text-nodes"
  data-item="The Role of Text Nodes"
>
  <h2><a href="#role-of-text-nodes">The Role of Text Nodes</a></h2>
  
So far, the only selections we have looked at together have started and ended in the same container. How is a selection represented when it starts in one HTML element and finishes in another?

Let's start with two simple cases. For the first case, click in the line at the top and drag your mouse down, so that the selection starts in the first `<p>` element and ends in the second `<p>` element:

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

<details class="note" open>
<summary>Respect for #text nodes</summary>
In every case, `startOffset` and `endOffset` are calculated _from the beginning of the specific text node_ in which they occur.

Be sure to note that the `parentElement` only helps identify where a given text node occurs, but its position and the length of its text are never used to `startOffset` and `endOffset`.

</details>
</section>