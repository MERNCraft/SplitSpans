<!-- Counting White Space -->
<section
  id="counting-units"
  aria-labelledby="counting-units"
  data-item="Counting Units"
>
  <h2><a href="#counting-units">Counting Units</a></h2>
  

Time for another experiment. In the demo you used earlier (or in the version you created for yourself), select from the last letter of "drag" to the first letter of "to".

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

One standard now is [UTF-16](https://en.wikipedia.org/wiki/UTF-16), which has variable-length blocks from 8 to 32 bits, and which provides enough numbers to label the 1,112,064 different symbols from many different languages that are recognized today. And it turns out that UTF-16 still has a few numbers left over, and these are now being used for emojis.

So you might imagine that an emoji would take two character spaces, where English letters, numbers and punctuation take only one. But that is politically incorrect. Emojis that show humans and parts of humans can have different skin colours, so the 👍🏼 emoji takes two values: a generic thumbs-up plus a skin colour.

To cut a long story short: `startOffset` and `endOffset` do not count characters or bits or bytes. They count `UTF-16 units`, and the 👍🏼 emoji uses four UTF-16 units.

From now on, I'll just call them `units`, and you'll discover later just how important it is to think in _units_ rather than _characters_ when working in with other languages (and with emojis).

<details class="note" open>
<summary>Units and Spaces</summary>
The important point to note is that the Selection and Range objects use a different system for counting where a selection starts and finishes than the one that might seem familiar to you.

So now you know, and you can start to think about selections with this new mental image.

</details>
</section>