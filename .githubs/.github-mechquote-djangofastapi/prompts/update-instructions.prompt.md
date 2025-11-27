---
description: "Invoke the Instructions Curator subagent to update MechQuote project instructions"
agent: instructions-curator
---

# Update Project Instructions

Analyze recent code changes and determine if project instructions need updating.

## Change Context

{{input}}

## Subagent Task

Execute the `instructions-curator` subagent to:

1. **Read current instruction files**:
   - `.github/copilot-instructions.md`
   - `.github/instructions/django-backend.instructions.md`
   - `.github/instructions/fastapi-laser.instructions.md`
   - `.github/instructions/javascript-frontend.instructions.md`
   - `.github/instructions/html-templates.instructions.md`

2. **Analyze the changes** described above and determine:
   - Do they establish new code patterns?
   - Do they introduce new naming conventions?
   - Do they create reusable components?
   - Do they modify the technology stack?

3. **Propose specific updates** to the corresponding instruction files

4. **Apply changes** if appropriate, maintaining:
   - Existing format and style
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

## Technology Mapping

| Change in... | Update file... |
|--------------|----------------|
| Django (views, models, forms) | `django-backend.instructions.md` |
| FastAPI (routers, services, schemas) | `fastapi-laser.instructions.md` |
| JavaScript (frontend, AJAX) | `javascript-frontend.instructions.md` |
| HTML/Templates (Jinja2) | `html-templates.instructions.md` |
| Architecture, security, general | `copilot-instructions.md` |
