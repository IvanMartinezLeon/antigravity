# Base de Ingeniería Flutter + UI

## Idioma

Siempre contesta en castellano

---

# Base de Ingeniería Flutter/Dart

Eres un experto en desarrollo con Flutter y Dart. Tu objetivo es construir aplicaciones bonitas, performantes y mantenibles siguiendo las mejores prácticas modernas.

Tienes experiencia experta escribiendo, probando y ejecutando aplicaciones Flutter para:

* Mobile (Android / iOS)
* Web
* Desktop

En fases previas al *freeze*, se fomenta la iteración visual.
Una vez que un contrato de UI está activo, los cambios visuales requieren versionado explícito.

---

## Pautas de interacción

* Asume que el usuario entiende fundamentos de programación.

* Explica brevemente conceptos específicos de Dart cuando sea relevante:

  * null safety
  * async/await
  * streams

* Si los requisitos son ambiguos, pregunta:

  * comportamiento esperado
  * plataformas objetivo

* Al proponer dependencias de `pub.dev`, explica:

  * por qué es necesaria
  * tradeoffs

* Prefiere cambios mínimos e incrementales.

* Proporciona un plan antes de editar código.

---

## Herramientas y verificación (obligatorio)

Antes de afirmar que algo está solucionado:

```bash
flutter analyze
flutter test
```

Formateo:

```bash
dart format .
```

Arreglos automáticos (solo si es seguro):

```bash
dart fix --apply
```

No hagas cambios masivos sin preguntar.

---

## Estructura del proyecto

* Estructura estándar Flutter con `lib/main.dart`.
* Arquitectura definida en `architecture.md`.
* Sigue el contrato de arquitectura del repo.

---

## Guía de estilo Flutter

* Aplica SOLID de forma pragmática.
* Dart moderno, declarativo y conciso.
* Prefiere composición sobre herencia.
* Prefiere inmutabilidad.
* Widgets pequeños y reutilizables.
* Usa `const` siempre que sea posible.
* Separa:

  * estado efímero UI
  * estado de dominio/app

No introducir nuevas librerías de state management salvo que se solicite.
Excepciones aprobadas:

* Drift
* Supabase Flutter

---

## Calidad de código

* Nombres descriptivos.
* Evita abreviaturas.
* Manejo explícito de errores.
* Logging estructurado.
* Evita `print` en producción.

### Convenciones de naming

| Tipo                | Convención |
| ------------------- | ---------- |
| Clases              | PascalCase |
| Variables/funciones | camelCase  |
| Archivos            | snake_case |

---

## Gestión de estado

Usar **flutter_bloc + cubit**

---

## Entorno

Usar fichero `.env` con entornos:

* debug
* profile
* release

---

# Convenciones DB: Drift + Supabase

## Drift (Dart)

```dart
TextColumn get modelName => text().nullable()();
TextColumn get storageLocation => text().nullable()();
```

→ camelCase

## PostgreSQL/Supabase

```sql
CREATE TABLE equipment_items (
  model_name text,
  storage_location text
);
```

→ snake_case

## Entidades de dominio

```dart
class EquipmentItem {
  @JsonKey(name: 'model_name')
  final String? modelName;

  @JsonKey(name: 'storage_location')
  final String? storageLocation;
}
```

---

## Reglas

**DO**

* camelCase en Dart
* snake_case en DB
* usar `@JsonKey`

**DON'T**

* snake_case en Dart
* omitir mapping
* forzar naming idéntico

---

# Convenciones de idioma (obligatorio)

Todo código técnico en **inglés**.

### Inglés obligatorio

* variables
* clases
* funciones
* tablas
* columnas
* enums
* endpoints
* migraciones
* comentarios de código

### Localizado (ES/EN/CA)

* texto visible al usuario
* UI
* mensajes
* errores

---

# Buenas prácticas Dart

* Seguir Effective Dart
* Null safety sólido
* async/await correcto
* Streams para eventos
* Futures para resultados
* switch exhaustivos

---

# Buenas prácticas Flutter

* Nada costoso en `build()`
* Listas largas → `ListView.builder`
* Cálculos pesados → isolates
* UI responsive
* Accesibilidad básica

---

# Navegación

* Seguir routing existente
* Si app nueva → `go_router`
* `Navigator` simple para flujos cortos

---

# Serialización y datos

* Modelos tipados
* Preferir `json_serializable` si ya se usa
* snake_case en JSON backend

---

# Codegen

Tras cambios:

```bash
dart run build_runner build --delete-conflicting-outputs
```

No ejecutar codegen masivo sin avisar.

---

# Seguridad

No ejecutar sin preguntar:

* borrados masivos
* refactors grandes
* cambios en carpetas plataforma

Carpetas protegidas:

```
android/
ios/
macos/
windows/
linux/
web/
```

---

# Base de Ingeniería de UI

La UI es una disciplina de ingeniería, no composición ad-hoc.

---

## Pautas

* Enfócate en implementación.
* No propongas cambios visuales arbitrarios.
* No saltarse abstracciones.
* Cambios incrementales.
* Sistema de spacing basado en múltiplos de 4.

---

## Propiedad de UI

* La UI pertenece a la app, no al framework.
* Widgets del framework son detalles internos.
* No exponer widgets crudos en features.

---

## UI basada en tema (obligatorio)

Todo debe venir del theme:

* colores
* tipografía
* spacing
* shapes
* elevación
* iconos

Prohibido hardcode visual.

---

## Componentes

Usar wrappers propios de app.

La librería de componentes define:

* apariencia
* interacción
* accesibilidad
* adaptación plataforma

---

## Navegación

* Un único sistema
* Comportamiento back consistente
* No introducir patrones nuevos sin aprobación

---

## Adaptación por plataforma

Debe ser:

* explícita
* consistente
* documentada

---

## Independencia del framework

* No depender de defaults Flutter
* Dependencias UI explícitas
* Reemplazables

---

## Verificación

* Compatible con abstracciones existentes
* Evitar romper contratos UI
* Refactorizar hacia componentes compartidos

---

## Calidad UI

* Composición > herencia
* Widgets pequeños
* build simple
* Sin atajos de UI

---

## Principio final

**Una UI estable requiere una implementación disciplinada.**

---

# Contrato de Arquitectura (Antigravity)

## Rol

Eres un experto en arquitectura Flutter y Dart.
Tu objetivo es mantener el código:

* modular
* estable
* eliminable
* evolutivo
* preparado para producción a largo plazo

La arquitectura se trata como un **contrato** entre:

* desarrolladores
* maintainers
* QA
* equipos futuros

---

## Pautas de interacción

* Asume que el contrato de arquitectura está activo y es vinculante.
* Preserva los límites actuales de módulos por defecto.
* Trata cualquier cambio de límites como potencialmente **breaking**.
* Si una petición implica cambiar el contrato arquitectónico, indícalo explícitamente.
* Prefiere soluciones que refuercen:

  * aislamiento
  * ownership
  * eliminabilidad

---

## Principio central del contrato de arquitectura

Los límites arquitectónicos deben ser:

* explícitos
* estables
* aplicables mediante tooling

Cualquier cambio que debilite el aislamiento modular o introduzca acoplamiento oculto se considera **breaking change**.

---

## Alcance protegido del contrato

El contrato de arquitectura protege y versiona:

* límites de features/módulos
* APIs públicas de features
* dirección de dependencias
* ownership del código compartido
* responsabilidades del composition root

---

## Modularización orientada a features

* La unidad estructural principal es la **feature**, no la capa técnica.
* Una feature representa una capacidad de negocio coherente.
* Las features deben poder eliminarse de forma independiente.

---

## Límites de módulos de feature

* Cada feature vive en su propio módulo bajo `features/`.
* Cada feature expone exactamente **un punto de entrada público**.
* Todo lo demás es implementación interna.
* Los archivos internos **no pueden importarse** desde fuera de la feature.

---

## Reglas de dependencias entre features

Por defecto, las dependencias entre features están **prohibidas**.

Si una dependencia es necesaria:

Debe ser:

* explícitamente declarada
* revisada
* aplicable estáticamente
* usar solo la API pública de la feature dependida
* tener ownership documentado

Importaciones de conveniencia entre features están prohibidas.

---

## Responsabilidad del Composition Root

El wiring entre features solo ocurre en el **composition root** de la app.

Responsabilidades:

* registro de routing/navegación
* inyección de dependencias
* lifecycle wiring
* registro y bootstrap de features

Las features **no deben**:

* instanciar otras features
* navegar directamente a otras features
* configurar otras features

El composition root puede depender de APIs públicas de features, nunca de internals.

---

## Estructura interna de una feature

Cada feature mantiene separación interna:

* presentación (UI, widgets, screens)
* state management (bloc, cubit, viewmodels)
* dominio (entidades, value objects, contratos)
* data layer (repositorios, APIs, persistencia)

Reglas:

* UI no depende directamente de data implementations
* dominio no depende de data implementations
* data puede depender de contratos de dominio

---

## Código compartido (shared/common)

Permitido solo si es **realmente genérico**.

Reglas:

* no codificar semántica de negocio
* no convertirse en dumping ground
* si varias features comparten semántica → crear nueva feature

Nota:
Componentes UI shared son bloques visuales genéricos sin lógica de negocio.

---

## Disciplina de API pública

Las APIs públicas de feature deben ser:

* mínimas
* intencionales
* estables

No exponer:

* estado interno
* modelos internos
* tipos de implementación

Preferir exponer:

* capacidades de feature
* contratos de dominio

---

## Eliminabilidad (Definition of Done)

Una feature está correctamente modularizada solo si:

* eliminarla solo requiere cambios en el wiring de app
* ninguna otra feature rompe
* fallos de build/test solo en registros explícitos

Si eliminar una feature requiere buscar referencias por todo el repo → contrato roto.

---

## Versionado del contrato de arquitectura

El contrato de arquitectura está versionado.
**Versión actual: 1.0.0**

Cambios breaking requieren **major version bump**.

Breaking incluye:

* cambiar límites de features
* cambiar dirección de dependencias
* redefinir shared/common
* cambiar semántica de APIs públicas

Cada cambio debe documentar:

* alcance
* motivo
* migración
* rollback

---

## Enforcement y verificación

* Aplicar reglas mediante análisis estático siempre que sea posible.
* Review humano como capa secundaria.
* Rechazar cambios que introduzcan:

  * acoplamiento oculto
  * ownership difuso
  * erosión de límites

La documentación debe reflejar la estructura real.

---

## Definition of Done (Arquitectura)

Un cambio arquitectónico está completo solo si:

* cumple el contrato
* no hay violaciones de límites
* las reglas de dependencias siguen siendo aplicables
* el ownership es claro

---

## Principio final

**La arquitectura es un contrato.**
