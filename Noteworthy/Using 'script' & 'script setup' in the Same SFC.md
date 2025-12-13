---
alias: "<script> & <script setup>"
---

- **Module Scope Execution**: The regular `<script>` block is executed in the module scope, allowing for side effects or object creation that should only occur once.
- **Declaring Additional Options**: Some options, like `inheritAttrs` or custom plugin-enabled options, cannot be expressed in `<script setup>` and require a normal `<script>` block.
- **Named Exports**: When you need to declare named exports for the component, you can use the regular `<script>` block.
- **Compatibility**: Using both blocks allows for better integration with existing Options API codebases or external libraries written with Composition API.
- **Flexibility**: It provides developers with more control over what gets exposed to the template and what remains private.
  <template>
  <button @click="handleClick" :disabled="disabled">
  {{ label }}
  </button>
  </template>

```vue
<script lang="ts">
import { tv, type VariantProps } from "tailwind-variants";

const button = tv({
	base: "border px-3 py-1 text-base font-medium focus:outline-hidden focus-visible:ring-2 focus-visible:ring-offset-2",
	variants: {
		variant: {
			solid: "border-transparent bg-emerald-500 text-white hover:bg-emerald-600 active:bg-emerald-700",
			outline:
				"border-emerald-500 text-emerald-500 hover:bg-emerald-50 active:bg-emerald-100",
		},
	},
});

export type ButtonVariantProps = VariantProps<typeof button>;

export interface ButtonProps {
	label?: string;
	variant?: ButtonVariantProps["variant"];
}
</script>

<script lang="ts" setup>
withDefaults(defineProps<ButtonProps>(), {
	variant: "solid",
});
</script>

<template>
	<button :class="button({ variant })">
		<slot>{{ label }}</slot>
	</button>
</template>
```
