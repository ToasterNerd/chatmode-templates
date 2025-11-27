# GuílleCalcular - Instrucciones Copilot

## Contexto del Proyecto
GuílleCalcular es una aplicación Spring Boot 3.5.5 (Java 17) para cálculo de sueldos líquidos, finiquitos y aportes patronales en Chile.

## Principios Generales
- Responder siempre en español
- Mantener cumplimiento con normativa laboral chilena
- Priorizar seguridad y validación de datos
- Usar patrones establecidos del proyecto
- Documentar código en español
- **NUNCA generar archivos .md de documentación/resumen sin preguntar primero al usuario**

## Stack Principal
- Backend: Spring Boot 3.5.5, Java 17, Spring Security, JPA
- Frontend: Thymeleaf, Materialize CSS, JavaScript vanilla
- Base de datos: MySQL
- Build: Maven

## Archivos Centrales
Los archivos `calculadora_liquido_v2.html` y `pro.calcular.cl.js` son el núcleo de la aplicación. Solo focalizarse en ellos cuando la tarea esté relacionada con cálculos de sueldo líquido.

## Convenciones Clave
- Nomenclatura: CamelCase para Java, kebab-case para CSS, camelCase para JavaScript
- IDs descriptivos: `#sueldoBase`, `#horasSemanales`
- Servicios backend siguen patrón: `NombreService.getLatestAsMap()`
- Validación dual: client-side + server-side

## Seguridad
- Usar `@Valid` en controladores
- Sanitizar inputs antes de procesamiento
- Control de acceso basado en roles y suscripciones
- No exponer entidades JPA directamente, usar DTOs

## Testing
- Tests unitarios con Mockito
- Tests de integración con SpringBootTest
- Tests de API con Bruno (carpeta otros/BRUNO/)

## Custom Agents y Prompts
El proyecto utiliza Custom Agents de VS Code para flujos de trabajo especializados:

### Agents (`.github/agents/`)
- `beast-mode.agent.md`: Agente principal SOTA con todas las herramientas
- `instructions-curator.agent.md`: Agente especializado para mantener instrucciones actualizadas

### Prompts (`.github/prompts/`)
- `instructions-curator.prompt.md`: Para invocar el curador como subagente
- `update-instructions.prompt.md`: Atajo para actualizar instrucciones

### Uso de Subagentes
Después de implementar cambios significativos (nuevos patrones, convenciones, componentes), usar `runSubagent` para invocar el curador de instrucciones y mantener la documentación sincronizada.

## 🚨 Política de Documentación - REGLA CRÍTICA 🚨
**NUNCA, JAMÁS, BAJO NINGUNA CIRCUNSTANCIA** generar archivos .md después de completar una tarea sin permiso explícito del usuario.

### Archivos PROHIBIDOS de crear automáticamente:
- ❌ README.md
- ❌ RESUMEN*.md
- ❌ FEATURE_*.md
- ❌ DEPLOYMENT*.md
- ❌ TESTING*.md
- ❌ DOCUMENTATION*.md
- ❌ CHANGELOG*.md
- ❌ GUIDE*.md
- ❌ CUALQUIER archivo .md que documente cambios/features/implementaciones

### Proceso OBLIGATORIO:
1. ✅ Completar la implementación del código
2. ✅ Ejecutar tests (si corresponde)
3. ✅ Informar al usuario: "Tarea completada"
4. 🛑 **DETENER AQUÍ** - NO crear ningún archivo .md
5. ❓ **SOLO SI CREES QUE SERÍA ÚTIL**: Preguntar "¿Quieres que genere alguna documentación o archivo .md resumiendo los cambios?"
6. ⏸️ **ESPERAR** confirmación explícita del usuario
7. ✅ **SOLO SI EL USUARIO DICE "SÍ"**: Crear la documentación

### Ejemplos de confirmación válida:
- ✅ "sí, genera la documentación"
- ✅ "dale, hacé el .md"
- ✅ "generá un resumen"
- ❌ (silencio del usuario) - NO generar nada
- ❌ "ok" - NO es suficiente, pedir confirmación explícita

## Mensaje de Verificación
Siempre terminar las respuestas con: "listo troesma, metele ficha"