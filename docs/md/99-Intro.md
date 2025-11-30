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

I recently needed to find a simple way to select text on a web page, ensuring that selections start and end with a whole word, and this regardless of language or writing system, or how the HTML text was formatted.

> ❌ This is wh<span style="background-color: #960">at I wanted </span>to avoid.
> 
> ✅ This is <span style="background-color: #960">what I wanted</span> to achieve.

In European languages, spaces are used to separate words. But this is not true for all Asian languages. In Thai, for instance, consonants and vowels are written in a continuous stream, with spaces used to indicate the end of a sentence. My knowledge of Thai is limited. I needed help to find where words in Thai begin and end.

> ❌ นี่คือสิ่งที่ฉันไม่อยาก<span style="background-color:#960">ใหเ กิดขึ้</span>น
> 
> ✅ นี่คือสิ่งที่ฉันไม่อยาก<span style="background-color:#960">ให้เกิดขึ้น</span>


Enter [Intl.Segmenter](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter), a relatively new JavaScript feature that is now widely available in browsers everywhere.

To understand how selection works in a browser and what Intl.Segmenter does, what better way is there than to write a tutorial (for you, and for my future self) about it.

### Why might you want to read this?

If you are building a tool involving text selection, editing, annotation, subtitles, or multilingual text handling, this tutorial might be helpful for you, for tasks like:
like:

* Snapping selections to whole words
* Handling selection that begins in whitespace
* Distinguishing punctuation vs typographic spaces
* Extracting the right boundaries for DOM splitting
* Supporting multilingual text in the browser

Use cases might include:

* A collaborative writing environment like a Google Docs clone
* An ebook reader, where user can look up words in a dictionary
* An annotation tool for speeches, lyrics or poetry
* An assisted reading tool
* A language learning app

Does this sound like something you are doing? If so, read on...


<details class="tldr">
<summary>TL;DR: Why do I need to do this?</summary>

I teach English as a foreign language to adults. I have friends who teach their own native languages, to adults and to children. A big question that we have is: "How to make learners want to spend time practising their new language?" One way is to get them to watch films in their target language. Or perhaps better yet (and free): YouTube videos on subjects that interest them.

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

 So, before making my "Learn a language through YouTube", I had to make myself a tool that would allow me to:

* Paste a YouTube URL
* Automatically download the transcript
* Automatically download the audio file, and generate a waveform graphic from it, so I could easily see where sounds began and ended
* Edit the text of the auto-generated transcript
* Divide the transcript into natural segments for subtitles
* Set the start and end times of each subtitle precisely
* Allow a translator to follow the video and the original subtitles while making a translation
* Provide the translator with some flexibility about subtitle timing, as different languages give the same information in a different order.
* Export the subtitles in the standard SRT format, to be used by my "Learn a language through YouTube" app.

Hence the need to split, merge and edit the text of HTML spans, as described in this tutorial.

You can find the Subtitle Optimizer tool [here](), and the "Learn a language through YouTube" app [here](https://stv.jazyx.com).

> ### Fun Fact
> One advantage of watching YouTube videos embedded in third-party site is that **the videos are never interrupted by ads**.
> 
> And yes, I have contacted the content creators who videos I link to, to make sure that they are happy that their work is used in this educational manner, even if it means that they don't earn any ad revenue from it.

</details>

</section>
