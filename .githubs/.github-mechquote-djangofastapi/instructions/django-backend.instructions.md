---
applyTo: "**/*.py"
description: "Instructions for Django development in MechQuote"
---

# Django Instructions - MechQuote

## 🌐 Language: ENGLISH ONLY

All new code must be in English:
- Variable/function names: `get_user_data()`, `calculate_total()`
- Comments and docstrings: `# Process the request`, `"""Return user profile"""`
- New file names: `laser_service.py`, `user_utils.py`

**Exception:** Existing model fields in Spanish (`nombre_empresa`, `usuario`) - maintain consistency.

## 🔧 Code Formatting Rules (CRITICAL)

**All Python code MUST comply with pre-commit hooks:**

### Line Length: MAX 119 CHARACTERS
```
⚠️ EVERY line must be < 120 characters (use max 119 to be safe)
   Lines with 120+ characters will FAIL validation
```

### Import Order (isort)
```python
# 1. Standard library
import json
import os
from datetime import datetime
from pathlib import Path

# 2. Django
from django.contrib.auth.decorators import login_required
from django.http import JsonResponse
from django.shortcuts import get_object_or_404, render

# 3. Third-party
import requests
from reportlab.lib.pagesizes import A4

# 4. Local apps
from authapp.models import UsuarioBueno
from uploadapp.models import Feedback_log

# 5. Relative imports
from .forms import ClienteForm
from .models import Cliente
```

### String Quotes
```python
# ✅ Use single quotes
name = 'value'
message = 'Hello world'

# ❌ Avoid double quotes (pre-commit will fix them)
name = "value"  # Will be converted to single quotes
```

## View Structure

```python
# Function-based views with decorators
@login_required
def view_name(request):
    user = request.user
    # Logic...
    return render(request, 'app/template.html', context)

# Class-based views
class NameListView(UserPassesTestMixin, ListView):
    model = Model
    template_name = 'app/list.html'
    
    def test_func(self):
        return is_admin(self.request.user)
```

## Django Models

```python
# Models with descriptive fields
class Cliente(models.Model):
    nombre_empresa = models.CharField(max_length=255)
    usuario = models.ForeignKey(
        'authapp.UsuarioBueno', 
        on_delete=models.CASCADE,
        related_name='clientes'
    )
    activo = models.BooleanField(default=True)
    
    def __str__(self):
        return f'{self.nombre_empresa} ({self.id_empresa})'

# Choices as tuple lists
TIPO_CHOICES = [
    ('COD', 'Description'),
    ('OTR', 'Other option'),
]
```

## Code Patterns

### Permission Checking
```python
def is_admin(user):
    usuario_bueno = user.usuariobueno_set.first()
    return usuario_bueno.plan == 'ADMIN' if usuario_bueno else False

# Custom decorator
@user_passes_test(is_admin)
def admin_only_view(request):
    pass
```

### Get Current User
```python
# Authenticated user always from request
user = request.user
usuario_bueno = UsuarioBueno.objects.filter(user=user).first()
```

### JSON Responses
```python
# Success
return JsonResponse({'success': True, 'data': result})

# Error
return JsonResponse({'success': False, 'error': 'Error message'}, status=400)
```

## Forms

```python
# Model-based forms
class ClienteForm(forms.ModelForm):
    class Meta:
        model = Cliente
        fields = ['nombre_empresa', 'direccion', 'telefono']
        widgets = {
            'nombre_empresa': forms.TextInput(attrs={'class': 'form-control'}),
        }
```

## URLs

```python
# urls.py for each app
from django.urls import path
from . import views

app_name = 'dashapp'

urlpatterns = [
    path('', views.piezas, name='piezas'),
    path('cliente/<int:pk>/', views.cliente_detail, name='cliente_detail'),
]
```

## FastAPI Integration

```python
import requests
import os

FASTAPI_BASE_URL = os.environ['MECHQUOTE_API_EXTRA_ENDPOINT']

def call_laser_api(dxf_file, params):
    """Call the laser cutting API."""
    files = {'upload_dxf_file': dxf_file}
    data = {
        'cantidad_piezas': params['cantidad'],
        'plancha_largo': params['largo'],
        # ... more parameters
    }
    
    response = requests.post(
        f"{FASTAPI_BASE_URL}/api/laser/analyze",
        files=files,
        data=data
    )
    
    if response.status_code == 200:
        return response.json()
    else:
        raise Exception(f"API Error: {response.text}")
```

## Async Processing

```python
import threading
import json
from pathlib import Path

TASKS_DIR = Path(__file__).resolve().parent / 'tasks'

def process_async(task_id, params):
    """Worker for background processing."""
    task_file = TASKS_DIR / f"{task_id}.json"
    
    # Update progress
    with open(task_file, 'w') as f:
        json.dump({'status': 'processing', 'progress': 50}, f)
    
    # Process...
    
    with open(task_file, 'w') as f:
        json.dump({'status': 'completed', 'result': result}, f)

# Start thread
thread = threading.Thread(target=process_async, args=(task_id, params))
thread.start()
```

## PDF Generation

```python
from reportlab.lib.pagesizes import A4
from reportlab.pdfgen import canvas

def generate_pdf(response, data):
    """Generate PDF with ReportLab."""
    p = canvas.Canvas(response, pagesize=A4)
    width, height = A4
    
    p.drawString(100, height - 100, f"Title: {data['title']}")
    p.save()
    return response
```

## File Handling

```python
from uploadapp.dropboxApi import save_logo, read_logo

# Upload file to Dropbox
def upload_file(file, path):
    save_logo(path, file.read())

# Read file from Dropbox  
def download_file(path):
    return read_logo(path)
```
