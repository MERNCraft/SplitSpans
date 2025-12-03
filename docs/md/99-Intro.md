---
title: Selecting Whole Words
subtitle: with window.getSelection() and Intl.Segmenter
month: November 2025
organization: MERNCraft
repo: SplitSpans
---
<section
  id="intro"
  aria-labelledby="intro"
  data-item="Introduction"
>
  <h2><a href="#intro">Introduction</a></h2>

<iframe
  id="demo"
  title="demo"
  width="600"
  height="300"
  src="https://merncraft.github.io/SplitSpansDemo/">
</iframe>


### What's in Store for You

####  Part 1: Selection
1. window.getSelection()
*  What it returns (a Selection object)
*  Multiple ranges? (selection.rangeCount)
*  Basic usage examples (toString(), getRangeAt(0))
  
2. The Range object
*  Properties: startContainer, startOffset, endContainer, endOffset, commonAncestorContainer
*  Methods: selectNode, selectNodeContents, setStart, setEnd, cloneRange
*  Why you often want a Range instead of just the selection string

3. Text nodes vs element nodes
*  What a text node is (nodeType === 3)
*  How inline elements affect selections (`<b>`, `<i>`, `<span>`)
*  Why startContainer can be a text node or an element (Safari quirk)
*  Using childNodes to inspect the DOM structure

4. Selection offsets
*  Offsets relative to the container (text node vs element)
*  Why startOffset and endOffset may not correspond to visible characters exactly (whitespace, hidden nodes, zero-length nodes)
*  Common mistakes when computing offsets directly
   - Treating offsets as global visible positions
   - Assuming containers are always text nodes
   - Forgetting that whitespace rules affect selectable characters
   - Not accounting for invisible/empty text nodes
   - Treating endOffset as inclusive
   - Misunderstanding UTF-16 vs grapheme clusters
   - Using textContent.length as if spans were one node
   - Misinterpreting backwards selections
   - Mutating the DOM before preserving Range info

5. Browser differences
*  Safari quirks:
*  startContainer may be in a previous element’s text node
*  Selections may “snap” across inline boundaries
*  Chrome/Firefox generally behave consistently
*  Implications for algorithms that rely on DOM offsets

6. Clamping / normalizing selections
*  Concept: adjusting startContainer and endContainer to the “first/last selectable text node” in the intended range
*  Why this is needed before doing any text manipulation or word-boundary expansion
*  Example: “Snap selection start to 0 offset of first text node inside element”

7. Traversing selected text
*  Why you need to iterate all text nodes between startContainer and endContainer
*  Introduce TreeWalker or NodeIterator for walking the DOM
*  Differences between childNodes iteration vs TreeWalker (nested inline elements)

9. Handling whitespace
white-space CSS affects what is visually selectable
*  pre-wrap vs normal vs nowrap
*  How this affects offset calculations
*  Note: pseudo-elements (::before, ::after) are ignored because they aren’t selectable

9. Edge cases and best practices
*  Empty elements or elements with no text nodes
*  Nested inline elements (<b><i>text</i></b>)
*  Collapsed nodes or nodes with only whitespace
*  Using Range.compareBoundaryPoints to detect containment

10. Topics to explore further
*   Using range.getClientRects() for visual selection bounds
*   Multi-range selections (rare, but good to mention)
*   How selection APIs interact with contenteditable areas
*   Selection normalization strategies from popular editors (ProseMirror, Slate)

#### Part 2: Detecting word boundaries
1. How browser understand word boundaries
2. Intl.Segmenter basics
3. Mapping segmenter offsets back to the DOM
4. Working no-break characters
5. Language-specific rules for punctuation
6. Boundaries around inline formatting
7. Identifying blocks that should not be broken
8. Words that span DOM nodes, for whatever reason
9. Special case: Speaker change em-dash for subtitles

#### Part 3: Splitting and merging elements
1. How Range mutations work
2. Splitting text nodes and inline structures
3. Extracting content with extractContents()
4. Safe merging: style checks, tagName checks
5. Wrapping selection in spans
6. Avoiding nested span hell
7. Removing/redesigning wrappers
8. Adding a class to new spans

#### Mini Projects
* Project 1: Basic selection
  Single text node, no formatting
  Goal: get selected text and offsets

* Project 2: Inline formatting
  `<b>` or `<i>` inside text
  Goal: understand startContainer and endContainer with nested tags

* Project 3: Multi-span selection
  Two adjacent `<span>` elements
  Goal: see how selection crosses elements, offsets are computed

* Project 4: Whitespace handling
  Show white-space: pre-wrap vs normal
  Goal: see which characters are actually selectable

Project 5: Safari quirk
Cross-span startContainer issue
Goal: understand why clamping/normalization is needed

* Project 6: Introduction to TreeWalker
  Walk selected text nodes, accumulate text
  Goal: show how to reliably collect selected text

* Project 7: Selection clamping
  Normalize selection start/end to first/last text node
  Goal: ready for word-boundary expansion in Part 2

---

* Whole-word expansion
* Splitting text out of existing spans
* Splitting `<b>`, `<i>`, etc. at selection boundaries
* Creating a new span exactly around the selection
* Merging neighboring spans when appropriate
* Normalizing Safari’s misaligned selection containers
* DOM text walking
* Range manipulation

<details class="tldr">
<summary>TL;DR: My personal use case</summary>

I teach English as a foreign language to adults. I have friends who teach their own native languages, to adults and to children. A big question that we have is: "How to make learners want to spend time practising their new language?" One way is to get them to watch films in their target language. Or perhaps better yet (and free): get them to watch short YouTube videos on subjects that interest them.

### YouTube auto-generated transcripts
 Many YouTube videos provide subtitles, and an interactive transcript. You can show the subtitles by clicking on the `Subtitles/closed caption` button in the YouTube toolbar, and you can show the transcript by ...

 * Clicking on the `more...` button in the summary
 * Scrolling to near the end to find the `Show Transcript` button
 * Making your web page wide enough to see the interactive transcript to the side of your video.

 But the YouTube website is design for YouTube to optimize revenue. Our language students don't need all the ads and links and extra content. They really just want a good video to watch, and help with understanding what is being said.

 Auto-generated subtitles can be wildly wrong and confusing. Even when they are accurate, the timing of the subtitles can be awkward and imprecise. A subtitle might contain speech both before and after a scene change, and it might be too long to be comfortable. The transcript is broken up into timed chunks that might not correspond to actual sentences.

### Doing it better

 I wanted to create something more like the system provided by [ted.com](https://www.ted.com/talks/joshua_foer_feats_of_memory_anyone_can_do/transcript), which:
 
 * Shows the transcript in natural paragraphs
 * Uses a separate span for each natural break in the speech
 * Provides transcripts created by humans and translated by humans into many different languages.

 I also wanted to add a feature that allows language learners to play a particular phrase or sentence in a loop, perhaps a little more slowly, so that they have the chance to hear how the speaker actually pronounces the words.

 A subtitle might say: "It's going to be good!" but the speaker might make a sound like "Sgonnbegooh!" It can take a learner a little time to get used to the shortcuts native speakers make, that other native speakers can easily understand.

 So, before releasing my "Learn a language through YouTube" app, I had to make myself a tool that would allow me to:

* Paste a YouTube URL
* Automatically download the transcript
* Automatically download the audio file, and generate a waveform graphic from it, so I could easily see where sounds began and ended
* Edit the text of the auto-generated transcript
* Divide the transcript into natural segments for subtitles
* Set the start and end times of each subtitle precisely
* Allow a translator to follow the video and the original subtitles while making a translation
* Provide the translator with some flexibility about subtitle timing, as different languages give the same information in a different order.
* Export the subtitles in the standard SRT format, to be used by my "Learn a language through YouTube" app.

Hence the need to split and merge the text of HTML spans, as described in this tutorial.

You can find the Subtitle Optimizer tool [here](), and the "Learn a language through YouTube" app [here](https://stv.jazyx.com).

> ### Fun Fact
> One advantage of watching YouTube videos embedded in third-party site is that **the videos are never interrupted by ads**.
> 
> And yes, I contact the content creators whose videos I link to, to make sure that they are happy that their work is used in this educational manner, even if it means that they don't earn any ad revenue from it.

</details>

</section>
