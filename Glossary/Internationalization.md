---
alias: I18n
---

- Prepares a website or application for [[localization]] to different languages and regions.

- One of the fundamental principles of i18n is extracting user-facing text from the codebase:
    - Store text strings in external resource files or databases
    - Use unique identifiers for each text string
    - Implement a system to load the appropriate language file based on user preferences

```javascript
// Instead of hardcoding text:
const greeting = "Hello, World!";

// Use a translation function:
const greeting = i18n.t("greeting");
```

- I18n involves adapting various elements to suit different locales:
    - Date and time formats
    - Number and currency formats
    - Text direction (left-to-right or right-to-left)

```javascript
const number = 1004580.89;
const formattedNumberDE = new Intl.NumberFormat("de-DE").format(number);
console.log(formattedNumberDE); // Output: 1.004.580,89
```

- Different languages have varying rules for pluralization, and i18n frameworks typically provide methods to handle this complexity:

```javascript
i18n.t("catCount", { count: 5 });
```
