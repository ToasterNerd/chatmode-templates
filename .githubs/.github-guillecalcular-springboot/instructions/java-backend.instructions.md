---
applyTo: "**/*.java"
description: "Instrucciones específicas para desarrollo Java/Spring Boot en GuílleCalcular"
---

# Instrucciones Java/Spring Boot - GuílleCalcular

## Patrones de Código
- Usar Repository Pattern para acceso a datos
- Service Layer para lógica de negocio
- DTOs para exponer datos, nunca entidades JPA directamente
- Builder Pattern para objetos complejos

## Convenciones de Nomenclatura
```java
// Controladores: verbo + contexto + Controller
@GetMapping("/calculadora-liquidov2")
public String getCalculadoraLiquidoV2()

// Servicios: sustantivo + Service
public class AFPGeneralService {
    public Map<String, String> getLatestAsMap()
}

// Entidades: sustantivo en singular
@Entity
@Table(name = "afp_general")
public class AFPGeneral

// DTOs: sustantivo + DTO
public class SimulacionLiquidoDTO
```

## Validaciones Obligatorias
- Usar `@Valid` en todos los controladores que reciben datos
- Implementar validaciones: `@NotNull`, `@NotBlank`, `@Min`, `@Max`
- Validar datos de entrada exhaustivamente
- Logs de error con contexto relevante

## Servicios de Datos Legales
- Todos los servicios de constantes legales deben tener método `getLatestAsMap()`
- Manejar Optional correctamente para datos que pueden no existir
- Cachear datos que cambian poco (UTM, UF, IMM)

## Seguridad
- Nunca exponer entidades JPA en endpoints
- Validar permisos según roles y suscripciones
- Sanitizar inputs antes de procesar
- Control de acceso con Spring Security

## Estructura de Paquetes
```
com.toastersoftware.guillecalcular/
├── controller/         # Endpoints REST y MVC
│   └── advice/         # ControllerAdvice globales
├── service/            # Lógica de negocio
├── repository/         # Acceso a datos
├── entity/             # Entidades JPA
├── dto/                # DTOs
├── enums/              # Enumeraciones (EstadoCivil, TipoSuscripcion, etc.)
├── exception/          # Excepciones personalizadas y GlobalExceptionHandler
├── config/             # Configuraciones (Security, App)
├── persistence/        # Entidades de persistencia (UserEntity)
├── util/ y utils/      # Utilidades (ApiResponse, etc.)
└── SpringSecurityAppApplication.java
```

## Enumeraciones (Enums)
```java
// Ubicación: com.toastersoftware.guillecalcular.enums
// Nombres descriptivos en singular
public enum TipoSuscripcion {
    MENSUAL, TRIMESTRAL, ANUAL
}

public enum EstadoCivil {
    SOLTERO, CASADO, DIVORCIADO, VIUDO
}
```

## Servicios Scheduler
```java
// Patrón: NombreSchedulerService para tareas programadas
@Service
public class UFSchedulerService {
    private static final Logger logger = LoggerFactory.getLogger(UFSchedulerService.class);
    
    @Scheduled(cron = "0 0 0 * * *") // Ejecutar todos los días a las 00:00
    public void updateDailyUF() {
        logger.info("Iniciando actualización diaria de UF");
        // ...
    }
}
```

## Manejo de Excepciones
```java
// Excepciones personalizadas en exception/
public class SubscriptionLimitException extends RuntimeException { }

// GlobalExceptionHandler con @ControllerAdvice
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(SubscriptionLimitException.class)
    public ResponseEntity<ApiResponse<String>> handleSubscriptionLimitException(SubscriptionLimitException ex) {
        return ResponseEntity.status(HttpStatus.FORBIDDEN)
                .body(new ApiResponse<>(ex.getMessage()));
    }
}
```

## ApiResponse Wrapper
```java
// Usar ApiResponse<T> para respuestas REST consistentes
public class ApiResponse<T> {
    private T data;
    private String error;
    private String message;
}

// Uso en controladores:
return ResponseEntity.ok(new ApiResponse<>(dto));
return ResponseEntity.status(HttpStatus.FORBIDDEN).body(new ApiResponse<>("Error message"));
```

## ControllerAdvice Global
```java
// GlobalModelAttributeAdvice inyecta atributos en todas las vistas
@ControllerAdvice
public class GlobalModelAttributeAdvice {
    @ModelAttribute("userRole")
    public String getUserRole() { /* ... */ }
    
    @ModelAttribute("subscriptionType")
    public String getSubscriptionType() { /* ... */ }
}
```

## Repositorios - Métodos Comunes
```java
// Patrón para obtener el registro más reciente
Optional<UF> findTopByOrderByFechaDesc();
Optional<IMM> findTopByOrderByFechaCambioDesc();
```

## Testing Java
- Tests unitarios con JUnit 5 y Mockito para servicios
- Tests de integración con `@SpringBootTest` para controladores
- Tests de API con Bruno (carpeta `otros/BRUNO/`)
- Mockear repositorios y servicios externos en tests unitarios
- Usar `@WebMvcTest` para tests de controladores aislados