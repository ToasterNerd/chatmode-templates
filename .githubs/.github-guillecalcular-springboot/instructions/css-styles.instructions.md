---
applyTo: "**/*.css"
description: "Instrucciones específicas para CSS en GuílleCalcular"
---

# Instrucciones CSS - GuílleCalcular

## Framework Base
- Basado en Materialize CSS
- Personalización específica en `liquido_v2.css` y `liquido_v2_pt2.css`

## Nomenclatura de Clases
```css
/* Clases descriptivas con propósito claro */
.custom-rounded-input     /* Inputs con bordes redondeados */
.toggle-chip             /* Chips activables/desactivables */
.card-radius             /* Cards con radio personalizado */
.button-chip             /* Botones estilo chip */
```

## Sistema de Grid Responsivo
```css
/* Grid Materialize */
.col s12 m6 l4 xl3      /* Responsive columns */

/* Visibility helpers */
.hide-on-small-only     /* Ocultar en móviles */
.hide-on-med-and-down   /* Ocultar en tablets y móviles */
.hide-on-large-only     /* Ocultar en desktop */
```

## Estados de Validación
```css
/* Estados de error */
.invalid { border-color: #f44336; }
.helper-text.red-text { color: #f44336; }

/* Estados de éxito */
.valid { border-color: #4caf50; }

/* Estados deshabilitados */
.disabled { opacity: 0.5; cursor: not-allowed; }
```

## Componentes Personalizados
```css
/* Chips interactivos */
.toggle-chip {
    cursor: pointer;
    transition: all 0.3s ease;
}
.toggle-chip.selected {
    background-color: #2196f3;
    color: white;
}

/* Cards personalizados */
.card-radius {
    border-radius: 8px;
}

/* Inputs personalizados */
.custom-rounded-input {
    border-radius: 4px;
    padding-left: 12px;
}
```

## Colores del Proyecto
- Primario: Azul Materialize (#2196f3)
- Secundario: Blue-grey (#607d8b)
- Error: Red (#f44336)
- Éxito: Green (#4caf50)
- Texto: Grey darken-4 (#212121)

## Espaciado y Layout
- Usar clases Materialize para padding/margin cuando sea posible
- Espaciado consistente de 8px, 16px, 24px
- Altura mínima de elementos interactivos: 44px (accesibilidad)

## Responsive Breakpoints
- Small: 0-600px (móviles)
- Medium: 601px-992px (tablets)
- Large: 993px-1200px (desktop)
- Extra Large: 1201px+ (pantallas grandes)