# MechQuote Platform - Copilot Instructions

## Project Context

MechQuote is a quoting platform for machining and laser cutting. Two-service architecture:
- **Django App** (`app/`): Main web application with authentication, dashboard, and quote management
- **FastAPI Service** (`mechquote-api-extra/`): Laser cutting analysis API and nesting optimization

## Technology Stack

### Django Backend
- Django 4.2.4 with Python 3.x
- PostgreSQL (psycopg2-binary)
- ReportLab for PDF generation
- Stripe for payments
- Dropbox API for storage

### FastAPI API
- FastAPI 0.115.7 with Pydantic 2.10
- ezdxf for DXF file processing
- Shapely and rectpack for nesting algorithms
- NumPy and Matplotlib for geometric analysis

### Frontend
- Django templates with Jinja2
- Vanilla JavaScript
- Custom CSS

## Django Apps Structure

```
app/
├── authapp/        # Authentication and users (UsuarioBueno, UserProfile)
├── dashapp/        # Main dashboard, clients, machines, treatments
├── laserapp/       # Laser cutting API interface
├── uploadapp/      # File uploads (Feedback_log)
├── cajetinapp/     # Drawing titleblocks
├── masivoapp/      # Bulk processing
├── paymentapp/     # Stripe integration
└── proyectobase/   # Django settings and configuration
```

## General Principles

- Use established project patterns
- Dual validation: client-side + server-side
- **NEVER generate .md documentation files without asking the user first**

## 🌐 Language Convention - ENGLISH ONLY

**All code must be written in English.** This includes:

- ✅ Variable names: `user_count`, `total_price`, `is_active`
- ✅ Function names: `calculate_total()`, `get_user_data()`, `validate_input()`
- ✅ Class names: `LaserAnalysis`, `UserProfile`, `ClienteManager`
- ✅ File names: `laser_service.py`, `user_utils.js`, `base_template.html`
- ✅ Comments and docstrings: `# Calculate the total cost`, `"""Process DXF file"""`
- ✅ Constants: `MAX_FILE_SIZE`, `DEFAULT_MARGIN`, `ERROR_MESSAGES`
- ✅ Dictionary keys: `{'status': 'success', 'message': 'Created'}`
- ✅ Error messages: `'Invalid file format'`, `'User not found'`

**Exceptions (existing legacy code):**
- Some model field names may be in Spanish (e.g., `nombre_empresa`, `usuario`)
- Database table names inherited from legacy code
- When maintaining existing code, follow its current naming

## 🔧 Code Formatting & Linters (CRITICAL)

This project uses **pre-commit hooks** with flake8, isort, and autopep8. All generated code **MUST** comply with these rules:

### Line Length - MAXIMUM 119 CHARACTERS
```
⚠️ CRITICAL: Every line must be < 120 characters (max 119)
   Lines with 120+ characters will FAIL the pre-commit validation
```

### Flake8 Configuration
- **max-line-length**: 119 (use < 120 to be safe)
- **max-complexity**: 10
- **Ignored**: W503, C901

### isort Configuration (Import Sorting)
```python
# Correct import order:
# 1. FUTURE
# 2. STDLIB (standard library)
# 3. DJANGO
# 4. THIRDPARTY
# 5. FIRSTPARTY
# 6. LOCALFOLDER

# Example:
import json
import os
from datetime import datetime

from django.http import JsonResponse
from django.shortcuts import render

import requests
from authapp.models import UsuarioBueno

from .models import Cliente
```

### Line Breaking Strategies
```python
# Long strings - use implicit concatenation or parentheses
error_message = (
    "This is a very long error message that would exceed the line limit "
    "so we break it into multiple lines using parentheses"
)

# Long function calls - break after opening parenthesis
result = some_function_with_long_name(
    first_argument,
    second_argument,
    third_argument_with_long_name=value,
)

# Long dictionaries - one key per line
data = {
    'key_one': value_one,
    'key_two': value_two,
    'key_three': value_three,
}

# Long conditions - use parentheses
if (
    condition_one
    and condition_two
    and condition_three
):
    do_something()

# Long list comprehensions - break into multiple lines
items = [
    process_item(item)
    for item in collection
    if item.is_valid
]
```

### Pre-commit Hooks Active
- `trailing-whitespace`: No trailing spaces
- `end-of-file-fixer`: Files must end with newline
- `double-quote-string-fixer`: Use single quotes for strings
- `flake8`: PEP8 compliance
- `isort`: Import sorting
- `autopep8`: Auto-formatting

## Naming Conventions

- Python: snake_case for functions and variables, PascalCase for classes
- JavaScript: camelCase for functions and variables
- CSS: kebab-case for classes
- Django URLs: kebab-case (e.g., `/laser-cutting/analyze/`)
- FastAPI endpoints: snake_case in paths (e.g., `/api/laser/analyze`)

## Security

- Use `@login_required` on all Django views requiring authentication
- Validate permissions based on user plan (UsuarioBueno.plan)
- Sanitize inputs before processing
- Don't expose sensitive data in JSON responses

## Testing

- Unit tests with Django TestCase
- Integration tests for FastAPI endpoints
- Use pytest for FastAPI API

## Inter-Service Communication

Django calls FastAPI via HTTP using requests:
```python
FASTAPI_BASE_URL = os.environ['MECHQUOTE_API_EXTRA_ENDPOINT']
response = requests.post(f"{FASTAPI_BASE_URL}/api/laser/analyze", ...)
```

## 🚨 Documentation Policy - CRITICAL RULE 🚨

**NEVER** generate .md files after completing a task without explicit user permission.

### MANDATORY Process:
1. ✅ Complete the code implementation
2. ✅ Run tests (if applicable)
3. ✅ Inform the user: "Task completed"
4. 🛑 **STOP HERE** - DO NOT create any .md file
5. ❓ Only if you think it would be useful: Ask "Would you like me to generate documentation?"
6. ⏸️ Wait for explicit user confirmation
