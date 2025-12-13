- Destructuring extracts primitive values, which breaks the reactive connection.
- When destructuring, the _value_ is extracted at that moment, not the **reactive reference**.
- Accessing or assigning to a destructured variable is non-reactive because it no longer triggers the `get` / `set` proxy traps on the source object.

```javascript
const user = reactive({
	name: "Alice",
	age: 25,
});

// This breaks reactivity
const { name, age } = user;

console.log(name); // 'Alice' - just a regular string now
name = "Bob"; // Only changes local variable
console.log(user.name); // Still 'Alice' - no connection!
```

- **`toRefs()`**
    - Converts all properties to refs, maintaining reactivity.

```javascript
const user = reactive({
	name: "Alice",
	age: 25,
	email: "alice@example.com",
});

// ✅ This preserves reactivity
const { name, age, email } = toRefs(user);

// Now these are refs connected to the original object
console.log(name.value); // 'Alice'
name.value = "Bob";
console.log(user.name); // 'Bob' - still connected!
```

- **`toRef()`**
    - For destructuring a single property.

```javascript
import { reactive, toRef } from "vue";

const user = reactive({
	name: "Alice",
	age: 25,
	email: "alice@example.com",
});

const name = toRef(user, "name");
const age = toRef(user, "age");

name.value = "Bob";
console.log(user.name); // 'Bob'
```

- Destructuring props also loses reactivity.

```js
const props = defineProps(["name", "age"]);
const { name, age } = props; // Loses reactivity!

const props = defineProps(["name", "age"]);
const { name, age } = toRefs(props); // ✅ Do this instead
```

## Quick Reference

```javascript
// ❌ BREAKS REACTIVITY
const { x } = reactive({ x: 1 });
const { x } = props;

// ✅ PRESERVES REACTIVITY
const { x } = toRefs(reactive({ x: 1 }));
const { x } = toRefs(props);
const x = toRef(reactive({ x: 1 }), "x");

// ✅ ALREADY SAFE (returns refs)
const { data, error } = useFetch();
const { count } = useCounter();
const { updateUser } = store; // Functions don't need toRefs

// ✅ DON'T NEED TO DESTRUCTURE
state.x = 2; // Just use directly
props.name; // Just use directly
```
