- The CSS box model is a fundamental concept that defines how elements are sized and laid out on a web page.
- It consists of four components:
    - content
    - padding
    - border
    - margin.
- The `box-sizing` property determines how the total width and height of an element are calculated, including its content, padding, and border.
- By default, the `box-sizing` property is set to `content-box`, which means the `width` and `height` properties only apply to the content area of an element.
- The padding and border are added to the specified width and height, making the rendered element larger than the specified dimensions.
- Alternatively, setting `box-sizing: border-box` includes the padding and border within the specified `width` and `height`, which makes it easier to size elements consistently, as the total rendered size matches the specified dimensions.
- It's recommended to set `box-sizing: border-box` as the default for all elements, as it provides a more intuitive and consistent sizing behavior.

```css
* {
	box-sizing: border-box;
}
```
