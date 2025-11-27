---
description: "Invocar el subagente Instructions Curator para actualizar las instrucciones del proyecto"
agent: instructions-curator
---

# Actualizar Instrucciones del Proyecto

Analizar cambios de código recientes y determinar si las instrucciones del proyecto necesitan actualización.

## Change Context

{{input}}

## Subagent Task

Execute the `instructions-curator` subagent to:

1. **Read current instruction files**:
   - `.github/copilot-instructions.md`
   - `.github/instructions/java-backend.instructions.md`
   - `.github/instructions/javascript-frontend.instructions.md`
   - `.github/instructions/css-styles.instructions.md`
   - `.github/instructions/html-thymeleaf.instructions.md`

2. **Analyze the changes** described above and determine:
   - Do they establish new code patterns?
   - Do they introduce new naming conventions?
   - Do they create reusable components?
   - Do they modify the technology stack?

3. **Propose specific updates** to the corresponding instruction files

4. **Apply changes** if appropriate, maintaining:
   - Existing format and style
   - Documentation in Spanish (project requirement)
   - Clear and concise code examples

## Expected Response Format

```markdown
## Analysis Completed

### Detected Changes:
- [List of relevant changes]

### Updates Made:
| File | Section | Change |
|------|---------|--------|
| [file] | [section] | [description] |

### Justification:
[Why these changes deserve to be documented]
```
