- A common way of scoping styles to a component.

- A CSS Module is a CSS file which declares styles that are scoped by default.
- Tools like `create-react-app` and `vite` support CSS Modules out of the box.
    - They basically attach a unique identifier to each component and list the styles with the unique ID as a selector.

```css
/* Button.module.css */
.btn {
	background: "red";
}

.btn-clicked {
	background: "crimson";
}
```

```jsx
import classes from "./Button.module.css";

// Vanilla JS
document.querySelector("button").className = classes.btn;

// React
const Button = () => {
    ...
    return (
        <button className={
            `${styles.btn} ${isClicked && styles["btn-clicked"] }`}>
            Submit
        </button>
    );
}
```
