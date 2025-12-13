## Consistent Border Radius

- Setting the same `border-radius` to a parent and child element doesn't lead to the desired effect around the corners.

![Border Radius](snippets.border-radius.png)

## Centering in CSS

### Horizontally + Vertically

```css
/* Using 'position' (1) */
.parent {
	position: relative;
}

.child {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
}

/* Using 'position' (2) */
.parent {
	position: relative;
}

.child {
	position: absolute;
	top: 0;
	left: 0;
	bottom: 0;
	right: 0;
	margin: auto;
}

/* Using 'flexbox' (1) */

.parent {
	display: flex;
	justify-content: center;
	align-items: center;
}

/* Using 'flexbox' (2) */

.parent {
	display: flex;
	justify-content: center;
}

.child {
	align-self: center;
}

/* Using 'flexbox' (3) */

.parent {
	display: flex;
}

.child {
	margin: auto;
}
```

### Horizontally

```css
/* Using 'position' (1) */
.parent {
	position: relative;
}

.child {
	position: absolute;
	top: 50%;
	transform: translateY(-50%);
}

/* Using 'position' (2) */
.parent {
	position: relative;
}

.child {
	position: absolute;
	left: 0;
	right: 0;
	margin: auto;
}
```

### Vertically

```css
/* Using 'position' (1) */
.parent {
	position: relative;
}

.child {
	position: absolute;
	left: 50%;
	transform: translateX(-50%);
}

/* Using 'position' (2) */
.parent {
	position: relative;
}

.child {
	position: absolute;
	top: 0;
	bottom: 0;
	margin: auto;
}
```

## Resizing `absolute` elements

- An `absolute`ly positioned element can be resized / stretched to fully cover its container.

```html
<div></div>
```

```css
div {
	position: absolute;
	background-color: gold;
	top: 0;
	right: 0;
	bottom: 0;
	left: 0;
	/* OR shorthand prop `inset: 0 0 0 0;` */
	margin: 0;
}
```
