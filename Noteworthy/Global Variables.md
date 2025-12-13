---
alias: Why are global variables considered a bad practice?
---

> [!question] Why are global variables considered a bad practice?

- When the scope of program components is limited, code is easier to understand. Local variables are more readable as they are self-documenting within a certain context.
- Using global variables limits access control. They can be read and modified by any part of a program. This not only creates conflict but also reduces security when working with other people's code like 3rd party plugins / modules.
- Global variables can create concurrency issues.
- Using global variables pollutes namespace; it creates confusion, and we might unknowingly end up using global when we meant to use local and vice versa.
- Global variables cause functions to have hidden unintended side effects.
- Local variables are generally more performant.
