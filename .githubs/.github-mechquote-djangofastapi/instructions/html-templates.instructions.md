---
applyTo: "**/*.html"
description: "Instructions for HTML/Django templates in MechQuote"
---

# HTML/Templates Instructions - MechQuote

## Base Template Structure

```html
{% extends 'base.html' %}

{% load static %}

{% block title %}Page Title{% endblock %}

{% block extra_css %}
<link rel="stylesheet" href="{% static 'app/css/page.css' %}">
{% endblock %}

{% block content %}
<div class="container">
    <!-- Main content -->
</div>
{% endblock %}

{% block extra_js %}
<script src="{% static 'app/js/page.js' %}"></script>
{% endblock %}
```

## Forms with CSRF

```html
<form method="post" enctype="multipart/form-data" id="main-form">
    {% csrf_token %}
    
    <!-- Form fields -->
    <div class="form-group">
        <label for="field">Label</label>
        <input type="text" name="field" id="field" class="form-control" required>
    </div>
    
    <!-- Files -->
    <div class="form-group">
        <label for="file">DXF File</label>
        <input type="file" name="file" id="file" accept=".dxf" required>
    </div>
    
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

## Data Iteration

```html
<!-- Data table -->
<table class="table">
    <thead>
        <tr>
            <th>Name</th>
            <th>Value</th>
            <th>Actions</th>
        </tr>
    </thead>
    <tbody>
        {% for item in items %}
        <tr>
            <td>{{ item.nombre }}</td>
            <td>{{ item.valor|floatformat:2 }}</td>
            <td>
                <a href="{% url 'app:detail' item.pk %}" class="btn btn-sm">View</a>
            </td>
        </tr>
        {% empty %}
        <tr>
            <td colspan="3">No data available</td>
        </tr>
        {% endfor %}
    </tbody>
</table>

<!-- Cards in grid -->
<div class="row">
    {% for cliente in clientes %}
    <div class="col-md-4">
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">{{ cliente.nombre_empresa }}</h5>
                <p class="card-text">{{ cliente.direccion }}</p>
            </div>
        </div>
    </div>
    {% endfor %}
</div>
```

## Conditionals

```html
<!-- Check permissions -->
{% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}</p>
{% else %}
    <a href="{% url 'login' %}">Log in</a>
{% endif %}

<!-- Check user plan -->
{% if usuario_bueno.plan == 'ADMIN' %}
    <a href="{% url 'admin:index' %}">Admin Panel</a>
{% endif %}

<!-- Show messages -->
{% if messages %}
<div class="messages">
    {% for message in messages %}
    <div class="alert alert-{{ message.tags }}">
        {{ message }}
    </div>
    {% endfor %}
</div>
{% endif %}
```

## JavaScript Variables from Django

```html
{% block extra_js %}
<script>
    // Global variables from Django
    window.API_BASE = "{{ api_base_url }}";
    window.CSRF_TOKEN = "{{ csrf_token }}";
    window.USER_ID = {{ user.id|default:'null' }};
    window.USER_CURRENCY = "{{ usuario_bueno.preferred_currency|default:'EUR' }}";
    
    // JSON data
    window.INITIAL_DATA = {{ data_json|safe }};
</script>
<script src="{% static 'app/js/page.js' %}"></script>
{% endblock %}
```

## Static Files

```html
<!-- CSS -->
<link rel="stylesheet" href="{% static 'app/css/styles.css' %}">

<!-- JavaScript -->
<script src="{% static 'app/js/main.js' %}"></script>

<!-- Images -->
<img src="{% static 'app/img/logo.png' %}" alt="Logo">

<!-- With version for cache busting -->
<link rel="stylesheet" href="{% static 'app/css/styles.css' %}?v=1.2.0">
```

## Dynamic URLs

```html
<!-- URL without parameters -->
<a href="{% url 'dashapp:piezas' %}">View pieces</a>

<!-- URL with parameters -->
<a href="{% url 'dashapp:cliente_detail' pk=cliente.pk %}">View client</a>

<!-- URL with query params -->
<a href="{% url 'dashapp:lista' %}?page={{ page_obj.next_page_number }}">Next</a>

<!-- In JavaScript -->
<script>
    const detailUrl = "{% url 'dashapp:detail' pk=0 %}".replace('0', itemId);
</script>
```

## Reusable Components

```html
<!-- Include partials -->
{% include 'components/navbar.html' %}
{% include 'components/footer.html' %}

<!-- With context -->
{% include 'components/card.html' with title=item.nombre description=item.desc %}

<!-- Conditional -->
{% include 'components/admin_panel.html' if user.is_staff %}
```

## Laser Analysis Form (Specific Example)

```html
<form id="laser-analysis-form" method="post" enctype="multipart/form-data">
    {% csrf_token %}
    
    <!-- DXF File -->
    <div class="form-group">
        <label for="dxf-file">DXF File *</label>
        <input type="file" 
               name="upload_dxf_file" 
               id="dxf-file" 
               accept=".dxf" 
               required>
        <small class="form-text text-muted">DXF format, max 50MB</small>
    </div>
    
    <!-- Sheet dimensions -->
    <div class="row">
        <div class="col-md-4">
            <label for="sheet-length">Length (mm) *</label>
            <input type="number" 
                   name="plancha_largo" 
                   id="sheet-length" 
                   min="1" 
                   step="0.1" 
                   required>
        </div>
        <div class="col-md-4">
            <label for="sheet-width">Width (mm) *</label>
            <input type="number" 
                   name="plancha_ancho" 
                   id="sheet-width" 
                   min="1" 
                   step="0.1" 
                   required>
        </div>
        <div class="col-md-4">
            <label for="sheet-thickness">Thickness (mm) *</label>
            <input type="number" 
                   name="plancha_espesor" 
                   id="sheet-thickness" 
                   min="0.1" 
                   step="0.1" 
                   required>
        </div>
    </div>
    
    <!-- Progress bar (hidden initially) -->
    <div id="progress-container" class="hidden">
        <div class="progress">
            <div id="progress-bar" class="progress-bar" style="width: 0%"></div>
        </div>
        <p id="progress-text">Processing...</p>
    </div>
    
    <button type="submit" id="submit-btn" class="btn btn-primary">
        Analyze
    </button>
</form>
```

## Pagination

```html
{% if page_obj.has_other_pages %}
<nav aria-label="Pagination">
    <ul class="pagination">
        {% if page_obj.has_previous %}
        <li class="page-item">
            <a class="page-link" href="?page={{ page_obj.previous_page_number }}">
                Previous
            </a>
        </li>
        {% endif %}
        
        <li class="page-item active">
            <span class="page-link">
                Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}
            </span>
        </li>
        
        {% if page_obj.has_next %}
        <li class="page-item">
            <a class="page-link" href="?page={{ page_obj.next_page_number }}">
                Next
            </a>
        </li>
        {% endif %}
    </ul>
</nav>
{% endif %}
```
