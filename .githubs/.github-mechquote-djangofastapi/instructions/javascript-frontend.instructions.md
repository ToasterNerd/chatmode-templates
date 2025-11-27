---
applyTo: "**/*.js"
description: "Instructions for JavaScript frontend in MechQuote"
---

# JavaScript Instructions - MechQuote

## Function Patterns

```javascript
// Initialization: init + Module
function initLaserForm() { }
function initClienteSelector() { }

// API calls: fetch + Resource
function fetchAnalysisResult(taskId) { }
function fetchClienteData(clienteId) { }

// Handlers: handle + Event
function handleFormSubmit(event) { }
function handleFileUpload(event) { }

// Validation: validate + Field
function validateDxfFile(file) { }
function validateRequiredFields(form) { }

// Rendering: render + Element
function renderResultTable(data) { }
function renderProgressBar(progress) { }

// Utilities: descriptive format
function formatCurrency(value, symbol) { }
function formatPercentage(value) { }
```

## AJAX Calls to Django

```javascript
// POST with CSRF token
async function submitForm(formData) {
    const csrfToken = document.querySelector('[name=csrfmiddlewaretoken]').value;
    
    const response = await fetch('/api/endpoint/', {
        method: 'POST',
        headers: {
            'X-CSRFToken': csrfToken,
        },
        body: formData
    });
    
    if (!response.ok) {
        throw new Error(`Error: ${response.status}`);
    }
    
    return await response.json();
}

// Simple GET
async function fetchData(url) {
    const response = await fetch(url);
    const data = await response.json();
    
    if (data.success) {
        return data.data;
    } else {
        throw new Error(data.error);
    }
}
```

## Polling for Async Tasks

```javascript
// Polling pattern for long-running tasks
async function pollTaskStatus(taskId, onProgress, onComplete, onError) {
    const maxAttempts = 60;
    const interval = 2000; // 2 seconds
    
    for (let attempt = 0; attempt < maxAttempts; attempt++) {
        try {
            const response = await fetch(`/laser/task-status/${taskId}/`);
            const data = await response.json();
            
            if (data.status === 'completed') {
                onComplete(data.result);
                return;
            } else if (data.status === 'error') {
                onError(data.error);
                return;
            } else {
                onProgress(data.progress, data.message);
            }
            
            await new Promise(resolve => setTimeout(resolve, interval));
        } catch (error) {
            onError(error.message);
            return;
        }
    }
    
    onError('Timeout: Task took too long');
}
```

## File Handling

```javascript
// Validate DXF file before upload
function validateDxfFile(fileInput) {
    const file = fileInput.files[0];
    
    if (!file) {
        showError('Please select a file');
        return false;
    }
    
    if (!file.name.toLowerCase().endsWith('.dxf')) {
        showError('File must be a valid DXF');
        return false;
    }
    
    const maxSize = 50 * 1024 * 1024; // 50MB
    if (file.size > maxSize) {
        showError('File is too large (max 50MB)');
        return false;
    }
    
    return true;
}

// File preview
function showFilePreview(file) {
    const preview = document.getElementById('file-preview');
    preview.textContent = `📄 ${file.name} (${formatFileSize(file.size)})`;
    preview.classList.remove('hidden');
}
```

## Value Formatting

```javascript
// Currency format
function formatCurrency(value, symbol = '€') {
    const num = parseFloat(value) || 0;
    return `${num.toFixed(2)}${symbol}`;
}

// Percentage format
function formatPercentage(value) {
    const num = parseFloat(value) || 0;
    return `${num.toFixed(1)}%`;
}

// File size format
function formatFileSize(bytes) {
    if (bytes < 1024) return `${bytes} B`;
    if (bytes < 1024 * 1024) return `${(bytes / 1024).toFixed(1)} KB`;
    return `${(bytes / (1024 * 1024)).toFixed(1)} MB`;
}

// Dimensions format
function formatDimensions(width, height, unit = 'mm') {
    return `${width.toFixed(1)} x ${height.toFixed(1)} ${unit}`;
}
```

## UI Feedback

```javascript
// Show user messages
function showSuccess(message) {
    const toast = createToast('success', message);
    document.body.appendChild(toast);
    setTimeout(() => toast.remove(), 5000);
}

function showError(message) {
    const toast = createToast('error', message);
    document.body.appendChild(toast);
    setTimeout(() => toast.remove(), 8000);
}

// Progress bar
function updateProgress(progress, message) {
    const progressBar = document.getElementById('progress-bar');
    const progressText = document.getElementById('progress-text');
    
    progressBar.style.width = `${progress}%`;
    progressText.textContent = message || `${progress}%`;
}

// Loading state
function setLoading(isLoading) {
    const submitBtn = document.getElementById('submit-btn');
    const spinner = document.getElementById('loading-spinner');
    
    submitBtn.disabled = isLoading;
    spinner.classList.toggle('hidden', !isLoading);
}
```

## Event Listeners

```javascript
// Initialization on DOM load
document.addEventListener('DOMContentLoaded', function() {
    initLaserForm();
    initEventListeners();
});

function initEventListeners() {
    // Form submit
    const form = document.getElementById('laser-form');
    if (form) {
        form.addEventListener('submit', handleFormSubmit);
    }
    
    // File input change
    const fileInput = document.getElementById('dxf-file');
    if (fileInput) {
        fileInput.addEventListener('change', function(e) {
            if (validateDxfFile(this)) {
                showFilePreview(this.files[0]);
            }
        });
    }
    
    // Numeric input changes
    document.querySelectorAll('input[type="number"]').forEach(input => {
        input.addEventListener('change', recalculatePreview);
    });
}
```

## Django Template Integration

```javascript
// Variables injected from Django/Jinja2
// In template: <script>window.API_BASE = "{{ api_base_url }}";</script>

const API_BASE = window.API_BASE || '';
const CSRF_TOKEN = window.CSRF_TOKEN || '';
const USER_CURRENCY = window.USER_CURRENCY || 'EUR';

// Use global variables
function getApiUrl(endpoint) {
    return `${API_BASE}${endpoint}`;
}
```
