- Due the nature of emojis, `String.prototype.length` in [[JavaScript]] doesn't return the expected result if a string contains an emoji.

```js
const emojis = ["👨🏽‍💻", "👪", "🎅🏿"];

emojis.forEach((emoji) => {
	console.log(emoji, emoji.length);
});

// Output:
// 👨🏽‍💻 7
// 👪 2
// 🎅🏿 4
```

- In such cases, the `Intl.Segmenter` API can be used to get the expected string length.

```js
emojis.forEach((emoji) => {
	console.log(emoji, Array.from(new Intl.Segmenter().segment(emoji)).length);
});

// Output:
// 👨🏽‍💻 1
// 👪 1
// 🎅🏿 1
```
