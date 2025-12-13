---
aliases:
    - UI
    - UX
---

## Principles of Design

> [!quote]- Design Principles (Graphic)
> ![](assets/images/design.principles.png)
> **Source**: VistaPrint

### Visual Hierarchy

- Concerns with arranging elements to show their order of importance, which guides the user's attention to the most important elements first.
- **Key Techniques**:
    - _Size_ - Larger elements draw more attention.
    - _Color_ - Brighter or contrasting colors stand out.
    - _Placement_ - Elements at the top are typically seen as more important.
    - _White Space_ - More space around an element emphasizes it.
- **Tips**:
    - Make the main heading the largest text on the page.
    - Use a bright, contrasting color for important buttons.
    - Place crucial information or calls-to-action near the top of the page.
    - Utilize white space to create distinct sections.

### Balance

- Refers to the distribution of visual weight on a web page.
- It can be:
    - _Symmetrical_ - Evenly distributed elements
    - _Asymmetrical_ - Uneven but still visually balanced layout
    - _Radial_ - Elements arranged around a central point

### Contrast

- Uses differences in color, size, or shape to make elements stand out.
- It's crucial for:
    - Making text readable against backgrounds
    - Highlighting important elements like calls-to-action
    - Creating visual interest

### Alignment

- Involves arranging elements to create order and organization.
- Good alignment:
    - Improves readability
    - Creates a clean, professional look
    - Helps guide users through the content

### Proximity

- Groups related items together, which:
    - Helps users understand relationships between elements
    - Improves information processing
    - Creates a more organized layout

### Repetition

- Involves reusing design elements throughout a website to:
    - Create consistency and unity
    - Reinforce branding
    - Make the site easier to navigate and understand

### White Space

- aka negative space.
- The empty space between and around elements of a design or page layout.
- Improves readability and comprehension.
- Creates visual hierarchy and guides the user's eye.
- Creates focus on important elements
- Gives design a clean, modern look.
- In a web application, it can be achieved using padding and margin properties.

### Consistency in Design

- Creates a cohesive look and feel, making applications more intuitive and user-friendly.
- **Key Areas**:
    - _Typography_ - Use a consistent set of fonts and sizes.
    - _Color scheme_ - Stick to a chosen color palette.
    - _UI elements_ - Buttons, forms, and other elements should have a consistent style.
    - _Spacing_ - Use consistent padding and margins throughout.

## UX

### Information Architecture

- Information architecture (IA) is crucial for organizing and structuring content in a way that's intuitive for users.
- **Key Aspects**:
    - Creating logical navigation hierarchies
    - Designing effective menu structures
    - Organizing content into categories and subcategories
    - Developing clear labeling systems

```html
<!-- Example: Navigation -->
<nav>
	<ul>
		<li><a href="#home">Home</a></li>
		<li>
			<a href="#products">Products</a>
			<ul>
				<li><a href="#category1">Category 1</a></li>
				<li><a href="#category2">Category 2</a></li>
			</ul>
		</li>
		<li><a href="#about">About</a></li>
		<li><a href="#contact">Contact</a></li>
	</ul>
</nav>
```

### Interaction Design

- Focuses on creating engaging interfaces with meaningful and intuitive interactions.
- **Key Principles**:
    - Providing clear feedback for user actions
    - Designing intuitive gestures and controls
    - Creating smooth transitions and animations
    - Ensuring consistency in interactive elements

```css
.button {
	transition: background-color 0.3s ease;
}

.button:hover {
	background-color: #0056b3;
}

.button:active {
	transform: scale(0.98);
}
```

#### Affordances and Signifiers

- Affordances are the possible actions users can take with an object.
- Signifiers are the visual cues that indicate these possibilities.
- **Tips**:
    - Use familiar UI elements that suggest their functionality.
    - Implement hover effects to indicate interactivity.
    - Utilize skeuomorphic design elements when appropriate.

### User Flow and Task Analysis

- Understanding how users move through your application to complete tasks is essential.
- This involves:
    - Mapping out common user journeys
    - Identifying and eliminating pain points in the flow
    - Optimizing steps required to complete tasks
    - Conducting task analysis to understand user goals and behaviors

## UI

### Typography

- Impacts readability and user experience.
- **Types**
    - _Serif_ - Fonts with small lines at the ends of characters (e.g., Times New Roman)
    - _Sans-serif_ - Fonts without these lines (e.g., Arial)
    - _Display_ - Decorative fonts for headlines
    - _Monospace_ - Each character occupies the same width (often used for code)
- **Font Pairing**
    - Combine fonts that complement each other.
    - A common approach is to use:
        - Sans-serif for headings
        - Serif for body text

### Color Theory

- The 60-30-10 Rule creates a balanced and visually appealing color scheme.
- It suggests using colors in the following proportion:
    - 60% dominant color (usually a neutral or less intense color)
    - 30% secondary color
    - 10% accent color

```css
/* Example */
body {
	background-color: #f0f0f0; /* Light gray - 60% */
}

header,
footer {
	background-color: #3498db; /* Blue - 30% */
}

.cta-button {
	background-color: #e74c3c; /* Red - 10% */
}
```

### Animations and Microinteractions

- Subtle animations can greatly enhance UX by providing feedback and guiding attention.
- Microinteractions are small, subtle animations or feedback mechanisms that can greatly enhance the user experience.
- It's important to:
    - Use animation purposefully
    - Keep animations short and smooth
    - Ensure animations don't interfere with usability
- **Examples**:
    - Hover effects on interactive elements
    - Transitions for smoother state changes
    - Loading animations to indicate process status
    - Form validation feedback
    - Subtle animations for state changes

```css
@keyframes pulse {
	0% {
		transform: scale(1);
	}
	50% {
		transform: scale(1.05);
	}
	100% {
		transform: scale(1);
	}
}

.notification-icon {
	animation: pulse 2s infinite;
}
```

### Accessibility

> [!cite]- Accessibility
> ![[Accessibility]]

- **Key Considerations**:
    - Color contrast
    - Font size and readability
    - Keyboard navigation

### Responsive Design

> [!cite]- Responsive Design
> ![[Responsive Web Design]]

## Psychology of Design

### Gestalt Principles

- Explain how humans perceive and organize visual information.
- **Key Principles**:
    - _Proximity_ - Elements close to each other are perceived as related.
    - _Similarity_ - Similar elements are perceived as part of the same group.
        - Group related items together in an interface.
        - Use similar styles for elements that serve similar functions.
    - _Closure_ - The mind tends to see complete figures even if they're incomplete.
    - _Continuity_ - The eye follows smooth paths rather than abrupt changes in direction.

### Cognitive Load Theory

- Suggests that our working memory has a limited capacity for processing information.
- **Tips**:
    - Break complex tasks into smaller, manageable steps.
        - e.g. Multi-step forms
    - Use progressive disclosure to reveal information gradually.
    - Implement familiar design patterns to reduce cognitive load.

### Hick's Law

- States that the time it takes to make a decision increases with the number and complexity of choices.
- **Tips**:
    - Limit the number of options presented to users.
    - Use categorization to organize large sets of options.
    - Implement search and filter functionality for extensive lists.

> Remember that good UX design is often invisible - it should enhance the user experience without drawing attention to itself.

---

## Further

### Books 📚

- Laws of UX (Jon Yablonski) ⭐

- Refactoring UI (Adam Wathan) ⭐

- Don’t Make Me Think (Steve Krug) ✅

### Learn 🧠

- UX for Beginners (Joel Marsh) 📚

- Refactoring UI (Adam Wathan) ⭐

### Reads 📄

- [Visual design rules you can safely follow every time](https://anthonyhobday.com/sideprojects/saferules/) ⭐

### Resources 🧩

- [Checklist Design](https://www.checklist.design/) ⭐

- [The Component Gallery](https://component.gallery/components/) ⭐

- [Web Interface Guidelines](https://interfaces.rauno.me/) ⭐

- [designtokens.fyi](https://designtokens.fyi/) ⭐

- [Design-Pattern Guidelines (NN/g)](https://www.nngroup.com/articles/design-pattern-guidelines/)
