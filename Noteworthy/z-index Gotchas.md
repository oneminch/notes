- For the `z-index` property to work on an element, the element's `position` value needs to be set to anything other than `static`.

## `z-index` & Pseudo-elements

- Setting the value of `z-index` to a negative value can be used as a workaround when using the property with pseudo-elements like `::before`.

```css
/* ⛔ Doesn't work with both positive values */
a {
	position: relative;
	z-index: 2;
}

a::before {
	position: absolute;
	z-index: 1;
}

/* ✅ Works with one negative value */
a {
	position: relative;
	z-index: 0;
}

a::before {
	position: absolute;
	z-index: -1;
}
```
