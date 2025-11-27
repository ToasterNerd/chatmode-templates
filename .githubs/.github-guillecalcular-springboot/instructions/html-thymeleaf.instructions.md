---
applyTo: "**/*.html"
description: "Instrucciones específicas para templates HTML/Thymeleaf en GuílleCalcular"
---

# Instrucciones HTML/Thymeleaf - GuílleCalcular

## Archivo Principal
- `calculadora_liquido_v2.html`: Vista principal de la calculadora
- Solo modificar si la tarea está relacionada con la interfaz de cálculos

## Estructura Base
```html
<!-- Inyección de datos backend -->
<script type="text/javascript" th:inline="javascript">
    window.afcGeneralBackendData = /*[[${afcGeneralData}]]*/ {};
    // ... más datos inyectados
</script>

<!-- Carga de scripts -->
<script th:src="@{/dashboard/assets/js/constantes_liquido_v2.js}"></script>
<script th:src="@{/dashboard/assets/js/pro.calcular.cl.js}"></script>
```

## Convenciones de IDs y Clases
```html
<!-- IDs descriptivos en camelCase -->
<input id="sueldoBase" name="sueldoBase">
<input id="horasSemanales" name="horasSemanales">

<!-- Contenedores con sufijo -fields-container -->
<div id="contrato-fields-container"></div>
<div id="haberes-fields-container"></div>

<!-- Clases personalizadas descriptivas -->
<input class="custom-rounded-input">
<div class="toggle-chip">
<div class="card-radius">
```

## Grid System Materialize
```html
<!-- Responsive grid -->
<div class="col s12 m6 l4 xl3">     <!-- Responsive columns -->
<div class="hide-on-small-only">    <!-- Ocultar en móviles -->
<div class="hide-on-med-and-down">  <!-- Ocultar en tablets y móviles -->
```

## Inyección de Datos Thymeleaf
```html
<!-- Variables globales (UTM, UF, IMM) -->
<div th:text="'$' + ${#numbers.formatInteger(valorUTM, 0, 'POINT')}">

<!-- Datos de servicios backend -->
window.afpGeneralBackendData = /*[[${afpGeneralData}]]*/ {};

<!-- Condicionales por rol de usuario -->
<div th:if="${userRole == 'CASA_PARTICULAR'}">
<li th:classappend="${userRole == 'CASA_PARTICULAR' ? 'disabled' : ''}">
```

## Tooltips y Ayuda
```html
<!-- Tooltips explicativos -->
<i class="material-icons tiny tooltipped" 
   data-position="top" 
   data-tooltip="Explicación del campo legal">info_outline</i>
```

## Formularios y Validación
```html
<!-- Formularios con validación -->
<form id="formularioGenerarSueldoLiquidoPDF" method="post">
    <!-- Campos con helper text para errores -->
    <span class="helper-text red-text left-align"></span>
</form>
```

## Navegación y Templates
- Template base: `dashboard/base_template.html` con fragments reutilizables
- Usar fragments de Thymeleaf para componentes comunes

### Fragments Disponibles en base_template.html
```html
<!-- Head con CSS y meta tags -->
<div th:replace="~{dashboard/base_template :: head}"></div>

<!-- Sidebar con página activa -->
<div th:replace="~{dashboard/base_template :: sidebar('calculadora-liquidov2')}"></div>

<!-- Navbar superior -->
<div th:replace="~{dashboard/base_template :: navbar}"></div>

<!-- Footer -->
<div th:replace="~{dashboard/base_template :: footer}"></div>

<!-- Scripts JS core -->
<div th:replace="~{dashboard/base_template :: core-js}"></div>
```

### Variables Globales Inyectadas (via GlobalModelAttributeAdvice)
```html
<!-- Disponibles en todas las vistas automáticamente -->
th:text="${userRole}"           <!-- USER, ADMIN, CASA_PARTICULAR -->
th:text="${subscriptionType}"   <!-- USER_BASIC, EMPRENDEDOR, etc. -->
```

## Estructura de Carpetas Templates
```
templates/
├── dashboard/
│   ├── base_template.html      # Template principal con fragments
│   ├── calculadora_liquido_v2.html
│   ├── empleados.html
│   ├── empresas.html
│   └── ...
├── landing/                    # Páginas públicas
├── subscription/               # Páginas de suscripción
├── login.html
├── register.html
└── ...
- Página activa con: `th:classappend="${activePage == 'calculadora-liquidov2'} ? 'active'"`
- Control de acceso: `th:if="${#authorization.expression('hasRole(\'USER\')')}"`

## Assets y Recursos
```html
<!-- CSS específico -->
<link th:href="@{/dashboard/assets/css/liquido_v2.css}" rel="stylesheet">

<!-- JavaScript específico -->
<script th:src="@{/dashboard/assets/js/pro.calcular.cl.js}"></script>
```