---
applyTo: "**/*.js"
description: "Instrucciones específicas para JavaScript en GuílleCalcular"
---

# Instrucciones JavaScript - GuílleCalcular

## Archivos Principales
- `pro.calcular.cl.js`: Motor principal de cálculos y renderizado
- `constantes_liquido_v2.js`: Procesamiento de datos legales del backend
- Solo modificar estos archivos si la tarea está relacionada con cálculos de liquidación

## Patrones de Función
```javascript
// Renderizado: render + TipoElemento
function renderInputField(), renderSelectField()

// Cálculos: calculate + Concepto
function calculateLiquidoSalary(), calculateTax()

// Validación: validate + Concepto
function validateRut(), validateRequiredFields()

// Formateo: format + Tipo
function formatCurrency(), formatNumber()

// Eventos: handle + Evento
function handleTabChange(), handleFormSubmit()
```

## Nomenclatura de IDs HTML
```javascript
// Campos de formulario: conceptoEnCamelCase
#sueldoBase, #horasSemanales, #afpComision

// Contenedores: concepto-fields-container
#contrato-fields-container, #haberes-fields-container

// Botones/acciones: accionDescriptiva
#generarSueldoLiquidoPDF, #guardarLiquidacion
```

## Gestión de Estado
- Variables globales en `window` para datos del backend
- Estado local en objetos JavaScript para formularios
- Usar `data-` attributes para configuración HTML
- Resetear campos a 0 al desactivar chips opcionales

## Sistema de Chips
- Chips para activar/desactivar campos opcionales
- Configurar con `showByChip` y `chipLabel` en fieldGroups
- Auto-reseteo de valores al desactivar

## Validación y Formateo
- Validación client-side + server-side
- Formatear números con separadores de miles
- Validar RUT chileno con RutUtils si está disponible
- Mostrar tooltips explicativos para campos legales

## Integración con Backend
- Usar constantes inyectadas vía Thymeleaf
- Procesar datos con `deepParseFloats()` para convertir strings a números
- Mantener sincronización con lógica del backend

### Patrón de Carga de Constantes (constantes_liquido_v2.js)
```javascript
// deepParseFloats convierte strings numéricos a números recursivamente
function deepParseFloats(obj) {
    if (Array.isArray(obj)) {
        return obj.map(deepParseFloats);
    } else if (typeof obj === "object" && obj !== null) {
        const parsed = {};
        for (const key in obj) {
            const value = obj[key];
            parsed[key] = typeof value === "string" && value.match(/^\d+(\.\d+)?$/)
                ? parseFloat(value)
                : deepParseFloats(value);
        }
        return parsed;
    }
    return obj;
}

// Objeto constantes con todos los datos legales procesados
const constantes = {
    afcGeneral: deepParseFloats(window.afcGeneralBackendData),
    afpGeneral: deepParseFloats(window.afpGeneralBackendData),
    uf: deepParseFloats(window.ufData),
    utm: deepParseFloats(window.utmData),
    // ... más constantes
};
```

### Patrón ApiResponse en Frontend
```javascript
// Las respuestas del backend usan ApiResponse wrapper
fetch('/api/endpoint')
    .then(response => response.json())
    .then(apiResponse => {
        if (apiResponse.error) {
            throw new Error(apiResponse.error);
        }
        const data = apiResponse.data; // Datos están en .data
        // procesar data...
    });
```

## Archivos JavaScript del Proyecto
```
static/dashboard/assets/js/
├── pro.calcular.cl.js          # Motor principal calculadora
├── constantes_liquido_v2.js    # Carga de constantes legales
├── finiquitoPRO.js             # Cálculos de finiquito
├── empleado-stepper.js         # Stepper de empleados
└── main.js                     # Inicialización general
```

## Buenas Prácticas
- Comentarios en español
- Funciones pequeñas y específicas
- Manejo de errores con try/catch
- Debounce en campos de entrada para performance
- Usar `console.error()` para errores, nunca `console.log()` en producción

## Testing JavaScript
- Tests con Jest para funciones de cálculo críticas
- Mock de datos del backend para tests unitarios