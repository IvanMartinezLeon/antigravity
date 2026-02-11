# Proyecto Flutter Antigravity

Bienvenido al proyecto Flutter Antigravity. Este repositorio sigue estándares de ingeniería estrictos y una arquitectura modular para garantizar la escalabilidad, el mantenimiento y la preparación para producción.

## 📖 Documentación Clave

Este proyecto se rige por varios documentos fundamentales que definen nuestros estándares de desarrollo:

- **[Reglas de Ingeniería](file:///Users/ivan.martinezl/Documents/Projects/antigravity/flutter_rules_esp.md)**: Nuestra base para el desarrollo Flutter/Dart, ingeniería de UI y estándares de codificación.
- **[Contrato de Arquitectura](file:///Users/ivan.martinezl/Documents/Projects/antigravity/flutter_rules_esp.md#L386)**: Versión 1.0.0 de nuestro compromiso arquitectónico.
- **[Flujo de Trabajo Estándar](file:///Users/ivan.martinezl/Documents/Projects/antigravity/flutter_workflow_esp.md)**: Guía paso a paso para la inicialización y configuración del proyecto.

---

## 🛠 Principios de Ingeniería

### 1. Contrato de Arquitectura (v1.0.0)
- **Orientado a Features**: La unidad estructural principal es la "feature" o funcionalidad, no la capa técnica.
- **Aislamiento**: Las "features" son independientes y están estrictamente desacopladas.
- **Composition Root**: Todo el cableado, inyección de dependencias y registro de navegación ocurre en un único punto.
- **Eliminabilidad**: Una funcionalidad está bien implementada solo si se puede eliminar con un impacto mínimo.

### 2. Ingeniería de UI
- **Basada en Temas**: Todos los colores, espaciado (múltiplos de 4) y tipografía provienen del `Theme`.
- **Independencia del Framework**: No dependemos de los valores por defecto del framework; utilizamos nuestras propias abstracciones.
- **Estabilidad**: La UI se trata como una disciplina, evitando composiciones ad-hoc.

### 3. Calidad y Estilo de Código
- **Gestión de Estado**: Uso estándar de **flutter_bloc + cubit**.
- **Dart Moderno**: Declarativo, conciso y siguiendo estrictamente el Null Safety.
- **SOLID**: Aplicado de forma pragmática para un código mantenible.

---

## 🚀 Resumen del Flujo de Trabajo Estándar

Nuestro proceso de inicialización garantiza un entorno listo para producción:

1. **Verificación del Entorno**: Comprobación obligatoria con `flutter doctor`.
2. **Definición del Proyecto**: Creación estándar de Flutter.
3. **Configuración Multi-entorno**: Tres entornos independientes (debug, profile, release) mediante archivos `.env`.
4. **Internacionalización (i18n)**: Soporte para inglés y español con detección automática del idioma del dispositivo.
5. **Validación**: Comprobación exhaustiva de la estructura y dependencias antes de comenzar el desarrollo.

---

## 🌐 Política de Idioma
- **Código Técnico**: Exclusivamente en **Inglés** (variables, clases, comentarios).
- **Interfaz de Usuario**: Localizada (Inglés/Español/Catalán).
- **Interacción con el Asistente**: El asistente de IA **siempre responde en castellano**.

---

*Para la versión en inglés, consulta [README.md](file:///Users/ivan.martinezl/Documents/Projects/antigravity/README.md)*
