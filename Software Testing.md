---
alias: Testing
---

## Concepts

### Tools

- 3 classes of testing tools
    - Testing environment / test runners
        - collect test, run test code & report results
        - e.g. Jest, Vitest, Playwright
    - Test frameworks
        - define / organize individual tests.
        - e.g. Testing Library
    - Assertion libraries
        - create testable claims

- Common testing tools fall into at least 2 of the above classes.

### Stubs

- Simple replacements for functions or objects that return predefined values.
- Used when you need a dependency to exist but don't care about its behavior.

```ts
/* --- TypeScript Example --- */
// Instead of calling a real API
const getUserStub = (): User => ({
	id: "123",
	name: "Test User",
	email: "test@example.com",
});

function testUserDisplay() {
	const user = getUserStub(); // Always returns same data
	const display = formatUserName(user);
	expect(display).toBe("Test User");
}
```

```java
/* --- Java Example --- */
public interface Temperature {
    double getTemperature();
}

public class WeatherService {
    private Temperature t;

    public WeatherService(Temperature t) {
        this.t = t;
    }

    public String getWeatherDescription() {
        double t = t.getTemperature();
        if (t < 0) return "Freezing";
        if (t < 15) return "Cold";
        if (t < 25) return "Warm";
        return "Hot";
    }

    public boolean isTemperatureAboveThreshold(double threshold) {
        return t.getTemperature() > threshold;
    }
}

/* --- Test --- */
@Test
public void testWeatherDescription() {
    // Create a stub
    Temperature t = new Temperature() {
        @Override
        public double getTemperature() {
            return 20.0; // Always return 20 degrees
        }
    };

    WeatherService service = new WeatherService(t);
    assertEquals("Warm", service.getWeatherDescription());
}
```

### Mocks

- Involves creating simulated objects or functions to replace real dependencies in tests.
- Allows to test a specific piece of code without worrying about how other parts of the system work.
- Replaces real objects that code depends on with fake objects (called mocks) that simulate the behavior of the real ones.
    - These objects can be used to control what happens during the test.
        - You can make them return specific values or behave in certain ways.
- Track how they're called and let you verify interactions.

- Useful for:
    - Asserting that specific methods were called with specific arguments.
        - Isolating components for true unit testing
        - Simulating API calls and responses
        - Testing different scenarios and edge cases

- More sophisticated than stubs and can be programmed with expectations about how they should be called.

```ts
/* --- TypeScript Example --- */
const emailService = {
	send: jest.fn(),
};

function testPasswordReset() {
	const userService = new UserService(emailService);
	userService.resetPassword("user@example.com");

	// Verify the email service was called correctly
	expect(emailService.send).toHaveBeenCalledWith({
		to: "user@example.com",
		subject: "Password Reset",
		body: expect.any(String),
	});
	expect(emailService.send).toHaveBeenCalledTimes(1);
}
```

```java
/* --- Java Example --- */
@Test
public void testIsTemperatureAboveThresholdWithMock() {
    // Create a mock for Temperature
    Temperature mockTemp = mock(Temperature.class);

    // Set up the mock behavior
    when(mockTemp.getTemperature()).thenReturn(30.0);

    WeatherService service = new WeatherService(mockTemp);

    assertTrue(service.isTemperatureAboveThreshold(25.0));

    // Verify that getTemperature was called exactly once
    verify(mockTemp, times(1)).getTemperature();
}
```

### Spies

- Wrap real functions to track calls while preserving original behavior.

```ts
const logger = {
	log: (message: string) => console.log(message),
};

const logSpy = jest.spyOn(logger, "log");

processData(logger); // Uses real logger.log

expect(logSpy).toHaveBeenCalledWith("Processing started");
```

### Fixtures

- Reusable test data that provide a consistent baseline.

```ts
// fixtures/users.ts
export const mockUsers = {
	admin: {
		id: "1",
		role: "admin",
		permissions: ["read", "write", "delete"],
	},
	regular: {
		id: "2",
		role: "user",
		permissions: ["read"],
	},
};

// In tests
import { mockUsers } from "./fixtures/users";

it("should allow admin to delete", () => {
	const result = canDelete(mockUsers.admin);
	expect(result).toBe(true);
});
```

### Snapshots

- Saved copies of your code's output that serve as a reference for future test runs.
- Instead of writing explicit assertions, you capture the output once, and subsequent tests verify nothing has changed unexpectedly.
- **Useful for**:
    - Verifying UI structure hasn't changed unintentionally.
    - Ensuring data from API responses remains consistent.
    - Generated or complex results where output is too large to write manual assertions.
- **How it works**:
    - The first time a test runs, the test runner creates a snapshot file.
    - Future test runs compare against this saved snapshot. If the output changes, the test fails.

```ts
import { render } from '@testing-library/react';
import { UserProfile } from './UserProfile';

it('renders user profile correctly', () => {
	const { container } = render(
		<UserProfile name="Alice" email="alice@example.com" />
	);

	expect(container).toMatchSnapshot();
});
```

- When behavior is intentionally changed, snapshots can be updated using testing tools, which will overwrite old snapshots with new output.
- For small, readable values, snapshots can be stored directly in test files rather than separate files.

### Environments

- Different contexts where tests run:
    - **Node environment**: Default for backend testing. Has access to Node APIs but no browser globals.
    - **`jsdom` / `happy-dom`**: Simulates browser environment in Node for frontend testing without a real browser.
        - Vitest's Browser Mode replaces `jsdom`/`happy-dom` for more reliable component tests without jumping to full E2E testing.
    - **Real browser** (E2E): Tools like Playwright or Cypress run tests in actual browsers.

### Regression Testing

- Re-running tests to ensure previously working functionality hasn't broken after code changes.
- Once tests pass, they should always pass. If refactoring breaks them, you've caught a regression.
- Add regression tests when bugs are discovered to prevent them from reoccurring. This builds coverage over time.
- **Visual Regression Testing**
    - Detects unintended visual changes by comparing screenshots before/after changes.
    - Catches CSS changes, layout breakage, font/color/spacing issues, responsive design problems, cross-browser rendering differences.
    - Functions similar to snapshot testing.
        - **Baseline**: Capture reference screenshots (known-good state)
        - **Comparison**: Capture new screenshots after changes
        - **Diff**: Compare pixel-by-pixel, highlight differences
    - Inherently unstable across different environments (i.e. devices and browsers).

```typescript
describe("Card component visuals", () => {
	it("matches baseline with minimal content", async () => {
		render(Card, { props: { title: "Title", body: "Body" } });
		expect(await screenshot()).toMatchScreenshot();
	});
});

/* --- Responsive Testing --- */
describe("Homepage visual regression", () => {
	it("looks correct on desktop", async () => {
		await page.viewport(1920, 1080);
		await page.goto("/");
		await expect(page.screenshot()).toMatchScreenshot();
	});
});
```

## Type of Testing

### Unit Testing

- Test specific low level units of functionality in isolation from other units.
    - Typically functions or classes.
- More unit tests are written compared to other types.

```jsx
import React from "react";
import { render } from "@testing-library/react";
import MyComponent from "./MyComponent";

test("renders the correct data", () => {
	const { getByText } = render(<MyComponent data="test data" />);
	expect(getByText("test data")).toBeInTheDocument();
});

test("renders the default data when no prop is provided", () => {
	const { getByText } = render(<MyComponent />);
	expect(getByText("Default Text")).toBeInTheDocument();
});
```

### Integration Testing

- Ensure the individual pieces of the application work well together.
- Focuses on catching flaws in interactions between integrated units or components.
- Strikes a balance between unit and end-to-end tests, providing confidence in how components work together.
- More integration tests are written compared to E2E tests.

```jsx
// ParentComponent.js
function ParentComponent({ data }) {
	return <ChildComponent data={data} />;
}

// ParentComponent.test.js
import React from "react";
import { render } from "@testing-library/react";
import ParentComponent from "./ParentComponent";

test("renders the correct data from the child component", () => {
	const { getByText } = render(<ParentComponent data="test data" />);
	expect(getByText("test data")).toBeInTheDocument();
});

test("renders the default data from the child component when no prop is provided", () => {
	const { getByText } = render(<ParentComponent />);
	expect(getByText("Default Child Data")).toBeInTheDocument();
});
```

### End to End (E2E) Testing

- Ensure application works well from the user's perspective.
- Ensures proper communication with other systems, interfaces, and databases.
- Takes more time and can be expensive in terms of efficiency, time, and money.
- Less E2E tests are written compared to other types.

```jsx
// App.jsx
function App() {
	const [count, setCount] = useState(0);

	return (
		<div>
			<h1>Counter App</h1>
			<p data-testid="count">Count: {count}</p>
			<button onClick={() => setCount(count + 1)}>Increment</button>
		</div>
	);
}

export default App;
```

```js
// e2e.test.js
import { test, expect } from "@playwright/test";

test("counter increments when the button is clicked", async ({ page }) => {
	// Navigate to the app
	await page.goto("http://localhost:3000");

	// Check the initial count
	const countElement = page.locator('[data-testid="count"]');
	await expect(countElement).toHaveText("Count: 0");

	// Click the increment button
	await page.click("text=Increment");

	// Check if the count has been incremented
	await expect(countElement).toHaveText("Count: 1");

	// Click the increment button again
	await page.click("text=Increment");

	// Check if the count has been incremented again
	await expect(countElement).toHaveText("Count: 2");
});
```

## Patterns

> [!quote]- The Testing Pyramid
> ![The Testing Pyramid](assets/images/testing.testing-pyramid.png)
> **Source**: Google Testing Blog

> [!quote]- The Testing Trophy
>
> ##### "Write tests. Not too many. Mostly integration."
>
> ![The Testing Trophy|350](assets/images/testing.testing-trophy.jpg)
> **Source**: Kent C. Dodds

### Test-Driven Development (TDD)

- The goal of TDD is code quality.

- **Advantages**
    - Clarified thinking / code design
    - Better communication between developers
    - Better structure / organization of production code
- **Disadvantages**
    - Takes longer initially
    - Bad tests create a fall sense of security

![Red -> Green -> Refactor](assets/images/testing.red-green-refactor.png)

- Good tests should be:
    - ==R==eadable
    - ==I==solated - test run independent of one another
    - ==T==horough - cover all edge cases
    - ==E==xplicit

### Arrange-Act-Assert (AAA)

- A structured approach to writing test cases that ensures clarity and separation of concerns.
- Widely used in unit testing and follows three distinct phases:
    - **Arrange**: Set up the necessary conditions and inputs for the test.
    - **Act**: Execute the functionality being tested.
    - **Assert**: Verify that the outcome is as expected.

```jsx
import { render, screen, fireEvent } from "@testing-library/react";
import Counter from "./Counter";

test("increments counter on button click", () => {
	// Arrange
	render(<Counter />);

	// Act
	fireEvent.click(screen.getByText("Increment"));

	// Assert
	expect(screen.getByText("Counter: 1")).toBeInTheDocument();
});
```

## Example

### Component Testing (Nuxt)

```vue
<!-- app/components/TaskItem.vue -->
<template>
	<div class="task-item" :data-status="task.status">
		<div class="task-header">
			<h3>{{ task.title }}</h3>
			<span class="priority" :data-priority="task.priority">
				{{ task.priority }}
			</span>
		</div>
		<p class="description">{{ task.description }}</p>
		<div class="task-footer">
			<span v-if="task.dueDate" class="due-date">
				Due: {{ formatDate(task.dueDate) }}
			</span>
			<div class="actions">
				<button @click="$emit('edit', task.id)" class="btn-edit">
					Edit
				</button>
				<button @click="handleDelete" class="btn-delete">Delete</button>
			</div>
		</div>
	</div>
</template>

<script setup lang="ts">
const props = defineProps<{
	task: Task;
}>();

const emit = defineEmits<{
	edit: [id: string];
	delete: [id: string];
}>();

function formatDate(date: string) {
	return new Date(date).toLocaleDateString();
}

function handleDelete() {
	if (confirm(`Delete task "${props.task.title}"?`)) {
		emit("delete", props.task.id);
	}
}
</script>
```

```ts
// test/nuxt/components/TaskItem.nuxt.test.ts
import { describe, it, expect, vi } from "vitest";
import { mountSuspended } from "@nuxt/test-utils/runtime";
import TaskItem from "~/components/TaskItem.vue";
import { mockTasks } from "../../fixtures/tasks";

describe("TaskItem", () => {
	it("renders task information correctly", async () => {
		const task = mockTasks[0];
		const wrapper = await mountSuspended(TaskItem, {
			props: { task },
		});

		// Check title and description are displayed
		expect(wrapper.find("h3").text()).toBe("Write unit tests");
		expect(wrapper.find(".description").text()).toBe("Add tests");

		// Check priority badge
		expect(wrapper.find(".priority").text()).toBe("high");
		expect(wrapper.find(".priority").attributes("data-priority")).toBe(
			"high",
		);

		// Check status data attribute
		expect(wrapper.attributes("data-status")).toBe("in-progress");
	});

	it("formats and displays due date when present", async () => {
		const task = mockTasks[0];
		const wrapper = await mountSuspended(TaskItem, {
			props: { task },
		});

		const dueDate = wrapper.find(".due-date");
		expect(dueDate.exists()).toBe(true);
		expect(dueDate.text()).toContain("Due:");
	});

	it("hides due date when not set", async () => {
		const task = mockTasks[1]; // No due date
		const wrapper = await mountSuspended(TaskItem, {
			props: { task },
		});

		expect(wrapper.find(".due-date").exists()).toBe(false);
	});

	it("emits edit event when edit button clicked", async () => {
		const task = mockTasks[0];
		const wrapper = await mountSuspended(TaskItem, {
			props: { task },
		});

		await wrapper.find(".btn-edit").trigger("click");

		expect(wrapper.emitted("edit")).toBeTruthy();
		expect(wrapper.emitted("edit")?.[0]).toEqual(["1"]);
	});

	it("shows confirmation and emits delete event", async () => {
		const task = mockTasks[0];

		// Mock window.confirm to return true
		const confirmSpy = vi.spyOn(window, "confirm").mockReturnValue(true);

		const wrapper = await mountSuspended(TaskItem, {
			props: { task },
		});

		await wrapper.find(".btn-delete").trigger("click");

		// Verify confirm was called with correct message
		expect(confirmSpy).toHaveBeenCalledWith(
			'Delete task "Write unit tests"?',
		);

		// Verify delete event was emitted
		expect(wrapper.emitted("delete")).toBeTruthy();
		expect(wrapper.emitted("delete")?.[0]).toEqual(["1"]);

		confirmSpy.mockRestore();
	});

	it("does not emit delete when user cancels confirmation", async () => {
		const task = mockTasks[0];
		const confirmSpy = vi.spyOn(window, "confirm").mockReturnValue(false);

		const wrapper = await mountSuspended(TaskItem, {
			props: { task },
		});

		await wrapper.find(".btn-delete").trigger("click");

		expect(wrapper.emitted("delete")).toBeFalsy();

		confirmSpy.mockRestore();
	});
});
```

### Composable Testing (Nuxt)

```ts
// app/composables/useTasks.ts
export function useTasks() {
	const tasks = ref<Task[]>([]);
	const loading = ref(false);
	const error = ref<string | null>(null);

	async function fetchTasks() {
		loading.value = true;
		error.value = null;

		try {
			const data = await $fetch<Task[]>("/api/tasks");
			tasks.value = data;
		} catch (e) {
			error.value = "Failed to fetch tasks";
			console.error(e);
		} finally {
			loading.value = false;
		}
	}

	async function createTask(input: CreateTaskInput) {
		try {
			const newTask = await $fetch<Task>("/api/tasks", {
				method: "POST",
				body: input,
			});
			tasks.value.push(newTask);
			return newTask;
		} catch (e) {
			throw new Error("Failed to create task");
		}
	}

	async function deleteTask(id: string) {
		await $fetch(`/api/tasks/${id}`, { method: "DELETE" });
		tasks.value = tasks.value.filter((t) => t.id !== id);
	}

	return {
		tasks: readonly(tasks),
		loading: readonly(loading),
		error: readonly(error),
		fetchTasks,
		createTask,
		deleteTask,
	};
}
```

```ts
// test/nuxt/composables/useTasks.nuxt.test.ts
import { describe, it, expect, vi, beforeEach } from "vitest";
import { mockNuxtImport } from "@nuxt/test-utils/runtime";
import { mockTasks } from "../../fixtures/tasks";

// Mock $fetch before any imports
const $fetchMock = vi.fn();
mockNuxtImport("$fetch", () => $fetchMock);

// Now import composable (after mock is set up)
import { useTasks } from "~/composables/useTasks";

describe("useTasks", () => {
	beforeEach(() => {
		// Reset mock between tests
		$fetchMock.mockReset();
	});

	it("fetches tasks successfully", async () => {
		// Setup: Mock returns our fixture data
		$fetchMock.mockResolvedValue(mockTasks);

		const { tasks, loading, error, fetchTasks } = useTasks();

		// Initially empty
		expect(tasks.value).toEqual([]);
		expect(loading.value).toBe(false);

		// Act: Fetch tasks
		await fetchTasks();

		// Assert: State updated correctly
		expect(loading.value).toBe(false);
		expect(error.value).toBeNull();
		expect(tasks.value).toEqual(mockTasks);

		// Verify API was called correctly
		expect($fetchMock).toHaveBeenCalledWith("/api/tasks");
		expect($fetchMock).toHaveBeenCalledTimes(1);
	});

	it("handles fetch errors", async () => {
		// Setup: Mock rejects with error
		$fetchMock.mockRejectedValue(new Error("Network error"));

		const { tasks, loading, error, fetchTasks } = useTasks();

		await fetchTasks();

		expect(loading.value).toBe(false);
		expect(error.value).toBe("Failed to fetch tasks");
		expect(tasks.value).toEqual([]);
	});

	it("creates a new task", async () => {
		const newTask = {
			id: "4",
			title: "New task",
			description: "Description",
			status: "todo" as const,
			priority: "low" as const,
			dueDate: null,
			createdAt: new Date().toISOString(),
			updatedAt: new Date().toISOString(),
		};

		$fetchMock.mockResolvedValue(newTask);

		const { tasks, createTask } = useTasks();

		const result = await createTask({
			title: "New task",
			description: "Description",
		});

		expect(result).toEqual(newTask);
		expect(tasks.value).toContain(newTask);

		// Verify POST request
		expect($fetchMock).toHaveBeenCalledWith("/api/tasks", {
			method: "POST",
			body: {
				title: "New task",
				description: "Description",
			},
		});
	});

	it("deletes a task", async () => {
		// Start with tasks
		$fetchMock.mockResolvedValueOnce(mockTasks);
		const { tasks, fetchTasks, deleteTask } = useTasks();
		await fetchTasks();

		// Setup delete to succeed
		$fetchMock.mockResolvedValueOnce(undefined);

		await deleteTask("1");

		// Task removed from state
		expect(tasks.value.find((t) => t.id === "1")).toBeUndefined();
		expect(tasks.value).toHaveLength(2);

		// Verify DELETE request
		expect($fetchMock).toHaveBeenCalledWith("/api/tasks/1", {
			method: "DELETE",
		});
	});

	it("throws error when create fails", async () => {
		$fetchMock.mockRejectedValue(new Error("Server error"));

		const { createTask } = useTasks();

		await expect(
			createTask({
				title: "Test",
				description: "Test",
			}),
		).rejects.toThrow("Failed to create task");
	});
});
```

### API Endpoint Testing (Nuxt)

```ts
// app/server/api/tasks/index.get.ts
import { getTasksFromDB } from "~/server/database";

export default defineEventHandler(async (event) => {
	try {
		const tasks = await getTasksFromDB();
		return tasks;
	} catch (error) {
		throw createError({
			statusCode: 500,
			message: "Failed to fetch tasks",
		});
	}
});
```

```ts
/* --- Critical E2E API Integration Tests (Real Server) --- */

// test/e2e/api/tasks.test.ts
import { describe, it, expect } from "vitest";
import { $fetch, setup } from "@nuxt/test-utils/e2e";
import { mockTasks } from "../../fixtures/tasks";

describe("Tasks API E2E", async () => {
	await setup({
		server: true,
	});

	describe("GET /api/tasks", () => {
		it("returns all tasks", async () => {
			const tasks = await $fetch("/api/tasks");

			expect(Array.isArray(tasks)).toBe(true);
			// Test against real or seeded database
		});
	});

	describe("POST /api/tasks", () => {
		it("creates a new task", async () => {
			const result = await $fetch("/api/tasks", {
				method: "POST",
				body: {
					title: "New task",
					description: "Test description",
				},
			});

			expect(result).toMatchObject({
				title: "New task",
				description: "Test description",
				status: "todo",
			});
			expect(result.id).toBeDefined();
		});
	});
});
```

```ts
/* --- Component Tests with API Layer (Runtime Mocks) --- */

// test/nuxt/components/tasks.nuxt.test.ts
import { describe, it, expect } from "vitest";
import { renderSuspended, registerEndpoint } from "@nuxt/test-utils/runtime";
import { screen } from "@testing-library/vue";
import TaskList from "~/components/TaskList.vue";
import { mockTasks } from "../../fixtures/tasks";

describe("TaskList with API", () => {
	it("fetches and displays tasks from API", async () => {
		// Mock the API endpoint the component calls
		registerEndpoint("/api/tasks", () => mockTasks);

		await renderSuspended(TaskList);

		// Component makes real request to mocked endpoint
		await screen.findByText("Write unit tests");
		expect(screen.getByText("Review pull requests")).toBeInTheDocument();
	});

	it("handles API errors", async () => {
		registerEndpoint("/api/tasks", () => {
			throw createError({
				statusCode: 500,
				message: "Server error",
			});
		});

		await renderSuspended(TaskList);

		await expect(screen.findByRole("alert")).resolves.toHaveTextContent(
			/failed/i,
		);
	});
});
```

### E2E Testing

```ts
import { expect, test } from "@nuxt/test-utils/playwright";

test("test", async ({ page, goto }) => {
	await goto("/", { waitUntil: "hydration" });
	await expect(page.getByRole("heading")).toHaveText("Welcome!");
});
```

## Best Practices

- Focus on testing behavior, not implementation details.
- When choosing what to use:
    - **Real implementation**: Prefer when fast and reliable
    - **Fake**: Lightweight working implementation (e.g., in-memory database)
    - **Stub**: When you just need data returned
    - **Mock**: When you need to verify behavior/interactions
    - **Spy**: When you need both real behavior and tracking
- **What to mock**:
    - ✅ DO mock:
        - External APIs ($fetch)
        - Browser APIs (localStorage, confirm)
        - Date/time (for consistent tests)
        - Random values
        - Third-party services
    - ❌ DON'T mock:
        - Framework reactivity
        - Own components (in integration tests)
        - Simple utility functions
        - Framework internals
- Each test should be independent and not rely on others running first.
    - Use hooks / setup functions like `beforeEach` to reset state rather than relying on test execution order.
- Test titles should tell a story.

```ts
it("emits delete event after user confirms", () => {});
```

- When working with visual regression tests,
    - wait for animations or disable them for visual tests.
    - testing specific elements.
    - make sure to handle dynamic values that change each run.
    - set consistent viewport sizes.
- When working with snapshot tests,
    - review changes carefully after updating snapshots.
    - use for stable output that doesn't change frequently.
    - prefer semantic assertions when possible.
    - keep snapshots smaller and focused by testing specific parts.
    - make sure to handle dynamic values that change each run.

```ts
it("creates user with timestamp", () => {
	const user = createUser("Alice");

	expect(user).toMatchSnapshot({
		id: expect.any(String), // Verified by type only
		createdAt: expect.any(Date), // Verified by type only
	});
});
```

- To mitigate [[flaky tests]]:
    - Ensure tests are deterministic and not reliant on external factors.
    - Use proper setup and teardown methods to isolate test environments.
    - Regularly review and refactor tests to improve reliability.
    - Avoid using random values unless necessary, and control randomness where possible (e.g. via mocking).

```ts
// Bad: Random data
expect({ id: Math.random() }).toMatchSnapshot();

// Good: Mock randomness
jest.spyOn(Math, "random").mockReturnValue(0.5);
expect({ id: Math.random() }).toMatchSnapshot();
```

---

## Further

### Reads 📄

- [Write tests. Not too many. Mostly integration. (Kent C. Dodds)](https://kentcdodds.com/blog/write-tests)
