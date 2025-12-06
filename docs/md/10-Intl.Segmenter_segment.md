<!-- Intl.Segmenter output -->
<section
  id="intl-segmenter-segment"
  aria-labelledby="intl-segmenter-segment"
  data-item="The Segment Object"
>
  <h2><a href="#intl-segmenter-segment">The Segment Object</a></h2>

I promised that I would explain why I have been using `Array.from()`.

Here's what happens when I create a segmenter in Node.js or a browser console. (Note that I don't use `let` or `const`, which means that the variables I create are global `var`s.)

```js-#
> segmenter = new Intl.Segmenter("en", { granularity: "word" })
Segmenter [Intl.Segmenter] {}
> segmenter.resolvedOptions()
{ locale: 'en', granularity: 'word' }
```

Here's what I get when I use its `segment()` method:

```js-#
> output = segmenter.segment("Hello world!")
{}
```

The console tells me that this is an object, but it does not give me any more details. But I can use `Array.from()` to generate an array from whatever this object holds:

```js-#
> Array.from(output)
[
  {
    segment: 'Hello',
    index: 0,
    input: 'Hello world!',
    isWordLike: true
  },
  { segment: ' ',
    index: 5,
    input: 'Hello world!',
    isWordLike: false
  },
  {
    segment: 'world',
    index: 6,
    input: 'Hello world!',
    isWordLike: true
  },
  { segment: '!',
    index: 11,
    input: 'Hello world!',
    isWordLike: false     
  }
]
```

I'll discuss the structure of the objects inside the `output` in the next section. First I'll explain what kind of object `output` is, and what advantages there are for creating such an object.

### `segment()` returns an _iterable_

The output of the `new Intl.Segmenter().segment()` object is a [Segment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl/Segmenter/segment/Segments) [iterable](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_generators#iterables). This means that you can use a `for ( x of y ) { ... }` loop to iterate through it, just like you can with an array:

```js-w
> for ( item of output ) { console.log(item) }
{ segment: 'Hello', index: 0, input: 'Hello world!', isWordLike: true }
{ segment: ' ', index: 5, input: 'Hello world!', isWordLike: false }
{ segment: 'world', index: 6, input: 'Hello world!', isWordLike: true }
{ segment: '!', index: 11, input: 'Hello world!', isWordLike: false }
```

<details class="tip">
<summary>Tip: use an Iterator object</summary>

You can also retrieve its [iterator object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Iterators_and_generators#iterators), and use that to step through its items:

```javascript-w
> iterator = output[Symbol.iterator]()
Object [Segmenter String Iterator] {}
> iterator.next()
{
  value: {
    segment: 'Hello',
    index: 0,
    input: 'Hello world!',
    isWordLike: true
  },
  done: false
}
> iterator.next()
{
  value: { segment: ' ', index: 5, input: 'Hello world!', isWordLike: false },
  done: false
}
> iterator.next()
{
  value: {
    segment: 'world',
    index: 6,
    input: 'Hello world!',
    isWordLike: true
  },
  done: false
}
> iterator.next()
{
  value: { segment: '!', index: 11, input: 'Hello world!', isWordLike: false },
  done: false
}
> iterator.next()
{ value: undefined, done: true }
```

</details>

### `output.containing()`

You can use `for ... of` loops with Arrays, Strings, Maps and Sets. You can also use an Iterator object (as described above) with these data types. Each type of object provides its own special set of functions, like `Array.filter()` or `Set.has()`

One feature that is unique to the Segment iterable is the `containing()` method. This accepts an integer index value as its argument, and returns the item which contains a grapheme at that index position:

```js-w
> output.containing(7)
{ segment: 'world', index: 6, input: 'Hello world!', isWordLike: true }
```

<details class="note" open>
<summary>Segments contain a range of index values</summary>

Segment iterables are the only iterable whose items can represent an array of index values. In the current example, the word "world" starts at index 6, and continues to index 10 inclusive. This means that you can use `Segment.containing()` to find which particular grapheme, word or sentence contains a particular index value.

</details>
</section>