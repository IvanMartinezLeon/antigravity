
---
# 🚀 StartFlutter_Ultimate_Engine_v3.1

## Descripción
Creación de Proyecto Flutter Estándar con Múltiples Entornos e Internacionalización.
Verificar el entorno y crear un nuevo proyecto Flutter estándar con:
- Configuración para tres entornos (debug, profile, release) mediante archivos `.env`
- Sistema de internacionalización con soporte para español (castellano) e inglés
- Detección automática del idioma del dispositivo
- Estructura lista para producción

## 0. Verificación del Entorno (Flutter Doctor)

**Acción obligatoria:** Analizar la salud del SDK antes de cualquier operación.

```bash
flutter doctor

```

> **Regla Crítica:** Detener el workflow si hay errores (❌) en Flutter SDK o Android Toolchain.

---

## 1. 🎛️ Fase de Configuración Interactiva (Ficha de Inicio)

**Instrucción para el Agente:** Detente y solicita al usuario estos datos. No ejecutes ninguna acción hasta recibir la ficha completa.

> ### 📝 Ficha de Inicialización de Proyecto
> 
> 
> 1. **Nombre de la Aplicación:** `[Escribe_aquí]` (ej: `nexus_app`)
> 2. **Bundle ID (Org):** `[Escribe_aquí]` (ej: `com.estudio.nexus`)
> 3. **Gestor de Estado (Marca con [X] solo uno):**
> 
> Por ejemplo, podrías ponerme:
> "Nombre: fitness_tracker, Org: com.miestudio, Gestor: Bloc"
> 
> 

---

## 2. 🛠️ Ejecución del Motor de Creación

### A. Identidad y Skill de IA

**1. Verificar estado de carpeta:**
```bash
# Verificar contenido de la carpeta actual
ls -la
```

**2. Decisión automática:**
- Si está vacía → `flutter create .`
- Si tiene contenido → `flutter create <nombre_app>`

```bash
# Crear proyecto con el Bundle ID y Nombre definidos
flutter create --org <bundle_id> <nombre_app>
cd <nombre_app>

```

* **Skill activada:** La IA ahora opera como Senior Flutter & Dart Architect.

> **⚡ ACTIVACIÓN DEL SKILL:** El Agente debe ejecutar ahora:
> ```bash
> skill --name flutter-expert
> ```
> Esto carga las instrucciones del experto Flutter en el contexto de la sesión.

### B. Configuración de `pubspec.yaml` (Base Técnica)

El Agente debe asegurar la siguiente configuración para habilitar la generación de código y los entornos:

```yaml
flutter:
  generate: true # Obligatorio para intl y generadores
  uses-material-design: true
  assets:
    - assets/env/.env.debug
    - assets/env/.env.profile
    - assets/env/.env.release

```

### C. Inyección de Dependencias Dinámica

* **Comunes:** `flutter_dotenv`, `intl`, `flutter_localizations`.
* **Específicos:** Según el gestor de estado elegido en el punto 1.3.

---

## 3. 🧠 Cerebro del Proyecto & SDD

Configuración de la "Memoria en Disco" para evitar deudas técnicas:

1. **`PROJECT_MEMORY.md`**: (Raíz) Log de decisiones.
* **Reglas Iniciales Obligatorias:**
  - "Priorizar `AppLocalizations.of(context)!` sobre strings hardcoded"
  - "Usar `const`constructors siempre que sea posible"
  - "Nombres de clases en PascalCase, métodos y variables en camelCase"
  - "Archivos con nombres en snake_case"
  - "Evitar widget rebuilds innecesarios - usar `const` y `keys`"
  - "0 errores en `flutter analyze` antes de cada commit"


2. **`doc/PROJECT_SPECIFICATIONS.md`**: Definición de modelos y contratos antes de codificar.
3. **`doc/architecture.md`**: Guía de capas para este proyecto.

---

## 4. 🌐 Entornos e Internacionalización (intl) Profesional

### A. Archivos de Entorno

**Estructura de directorios:**
```bash
mkdir -p assets/env
```

**Contenido de archivos:**

*`assets/env/.env.debug`*
```env
ENVIRONMENT=debug
API_URL=https://api-dev.example.com
LOG_LEVEL=debug
FEATURE_FLAGS=true
APP_NAME="Mi App (DEV)"
```

*`assets/env/.env.profile`*
```env
ENVIRONMENT=profile
API_URL=https://api-staging.example.com
LOG_LEVEL=info
FEATURE_FLAGS=true
APP_NAME="Mi App (STAGING)"
```

*`assets/env/.env.release`*
```env
ENVIRONMENT=release
API_URL=https://api.example.com
LOG_LEVEL=error
FEATURE_FLAGS=false
APP_NAME="Mi App"
```

### B. Configuración de Entornos (environment.dart)

**⚠️ IMPORTANTE: Añadir al `.gitignore`:**
```gitignore
# Archivos de entorno - NO subir a repositorio
assets/env/.env*
!.env.example
```

**Crear `.env.example` como plantilla:**
```bash
touch assets/env/.env.example
```

*`assets/env/.env.example`*
```env
ENVIRONMENT=debug
API_URL=https://api-dev.example.com
LOG_LEVEL=debug
FEATURE_FLAGS=true
APP_NAME="Mi App"
```

Crear `lib/config/environment.dart`:

```dart
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:flutter/foundation.dart';

enum AppEnvironment { debug, profile, release }

class EnvironmentConfig {
  static late AppEnvironment environment;
  static late String apiUrl;
  static late String logLevel;
  static late bool featureFlags;
  static late String appName;

  static Future<void> initialize() async {
    final envFile = kDebugMode ? '.env.debug' 
                 : kProfileMode ? '.env.profile' 
                 : '.env.release';
    
    environment = kDebugMode ? AppEnvironment.debug 
               : kProfileMode ? AppEnvironment.profile 
               : AppEnvironment.release;

    await dotenv.load(fileName: 'assets/env/$envFile');
    
    apiUrl = dotenv.env['API_URL'] ?? 'https://api.example.com';
    logLevel = dotenv.env['LOG_LEVEL'] ?? 'info';
    featureFlags = dotenv.env['FEATURE_FLAGS'] == 'true';
    appName = dotenv.env['APP_NAME'] ?? 'Mi App';
  }

  static String get environmentName => switch (environment) {
        AppEnvironment.debug => 'debug',
        AppEnvironment.profile => 'profile',
        AppEnvironment.release => 'release',
      };
}
```

### C. Internacionalización con intl

**1. Crear estructura de traducciones:**
```bash
mkdir -p lib/l10n
```

**2. Archivo `l10n.yaml`:**
```yaml
arb-dir: lib/l10n
template-arb-file: app_es.arb
output-localization-file: app_localizations
output-class: AppLocalizations
synthetic-package: false
output-dir: lib/l10n/generated
```

**3. `lib/l10n/app_es.arb`:**
```json
{
  "@@locale": "es",
  "appName": "Mi App",
  "welcome": "Bienvenido",
  "settings": "Ajustes",
  "language": "Idioma",
  "environment": "Entorno: {env}",
  "@environment": {
    "placeholders": {
      "env": {"type": "String"}
    }
  },
  "debug": "Desarrollo",
  "profile": "Preproducción",
  "release": "Producción"
}
```

**4. `lib/l10n/app_en.arb`:**
```json
{
  "@@locale": "en",
  "appName": "My App",
  "welcome": "Welcome",
  "settings": "Settings",
  "language": "Language",
  "environment": "Environment: {env}",
  "@environment": {
    "placeholders": {
      "env": {"type": "String"}
    }
  },
  "debug": "Debug",
  "profile": "Profile",
  "release": "Release"
}
```

**5. Configuración en `pubspec.yaml`:**
```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_dotenv: ^5.1.0
  intl: any
  flutter_localizations:
    sdk: flutter
```

**6. Generar localize:**
```bash
flutter pub get
flutter gen-l10n
```

**7. Ejemplo de `lib/main.dart`:**
```dart
import 'package:flutter/material.dart';
import 'package:flutter_localizations/flutter_localizations.dart';
import 'package:flutter_gen/gen_l10n/app_localizations.dart';
import 'config/environment.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await EnvironmentConfig.initialize();
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: EnvironmentConfig.appName,
      debugShowCheckedModeBanner: false,
      localizationsDelegates: const [
        AppLocalizations.delegate,
        GlobalMaterialLocalizations.delegate,
        GlobalWidgetsLocalizations.delegate,
        GlobalCupertinoLocalizations.delegate,
      ],
      supportedLocales: AppLocalizations.supportedLocales,
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.blue),
        useMaterial3: true,
      ),
      home: const HomeScreen(),
    );
  }
}

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    final l10n = AppLocalizations.of(context)!;
    final envLabel = EnvironmentConfig.environmentName;

    return Scaffold(
      appBar: AppBar(
        title: Text(l10n.appName),
        actions: [
          PopupMenuButton<Locale>(
            onSelected: (locale) => context.setLocale(locale),
            itemBuilder: (context) => [
              PopupMenuItem(value: const Locale('es'), child: Text(l10n.language)),
              PopupMenuItem(value: const Locale('en'), child: Text(l10n.language)),
            ],
          ),
        ],
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(l10n.welcome, style: Theme.of(context).textTheme.headlineMedium),
            const SizedBox(height: 16),
            Text(l10n.environment(envLabel)),
            const SizedBox(height: 24),
            ElevatedButton(
              onPressed: () {},
              child: Text(l10n.settings),
            ),
          ],
        ),
      ),
    );
  }
}
```

**8. Uso en runtime:**
```dart
// Leer variables de entorno en cualquier widget
final apiUrl = EnvironmentConfig.apiUrl;
final isDebug = EnvironmentConfig.environment == AppEnvironment.debug;
final featuresEnabled = EnvironmentConfig.featureFlags;
```

---

## 5. 🔄 Protocolo de Sincronización y Calidad

Para cada tarea, el Agente seguirá este ciclo de vida estricto:

1. **SDD Update:** Actualizar especificaciones en `doc/`.
2. **Code & Gen:** Escribir código, ejecutar `flutter pub get` y `flutter gen-l10n`.
3. **Linter Check:** `flutter analyze` (0 errores permitidos).
4. **Memory Log:** Registrar aprendizajes en `PROJECT_MEMORY.md` con el tag `### [LOG_TECNICO]`.

---

## 6. ✅ Validación de Estructura Completa

**Archivos obligatorios:**
- `pubspec.yaml` (con flutter_dotenv, intl, flutter_localizations)
- `l10n.yaml` (configuración de localizaciones)
- `lib/config/environment.dart`
- `lib/l10n/app_es.arb`
- `lib/l10n/app_en.arb`
- `lib/l10n/generated/app_localizations.dart` (generado)
- `assets/env/.env.debug`
- `assets/env/.env.profile`
- `assets/env/.env.release`
- `assets/env/.env.example` (plantilla)
- `.gitignore` (con exclusión de .env)

**Carpetas obligatorias:**
- `android/`
- `ios/`
- `assets/env/`
- `lib/l10n/`
- `lib/config/`
- `doc/`

**Descarga de dependencias:**
```bash
flutter pub get
flutter pub upgrade --major-versions
```

**Verificación final:**
```bash
flutter analyze
```

> **Error Crítico:** Si falta cualquier archivo esencial o hay errores en `flutter analyze`, detén el flujo e informa específicamente qué elemento está ausente.

---

## 7. 📜 Reglas del Agente

- **Nunca expongas claves sensibles** en código fuente
- **No asumas configuraciones** no especificadas
- **No avances sin validación exitosa** en cada paso
- **No corrijas errores automáticamente** sin autorización
- **Prioriza estabilidad y seguridad** sobre velocidad
- **Documenta claramente** cada configuración sensible

---

## 📋 Resumen de Configuración Exitosa (Checklist Final)

* [ ] **Identidad:** Nombre y Bundle ID aplicados.
* [ ] **Build System:** `generate: true` activo en `pubspec.yaml`.
* [ ] **IA Expert Skill:** Habilidades activas (`skill --name flutter-expert`).
* [ ] **Agente con Memoria:** `PROJECT_MEMORY.md` con reglas de calidad iniciales.
* [ ] **Arquitectura:** Carpeta `doc/` y Gestor de Estado configurados.
* [ ] **Configuración de Red:** `environment.dart` listo para manejar múltiples `.env`.
* [ ] **Entornos:** Archivos .env.debug, .env.profile, .env.release creados.
* [ ] **i18n:** Archivos .arb para es/en con generación automática.
* [ ] **Validación Estructural:** Todos los archivos y carpetas obligatorios presentes.
* [ ] **Dependencias:** `flutter pub get` y `flutter pub upgrade --major-versions` ejecutados.
* [ ] **Linter:** `flutter analyze` con 0 errores.

**ESTADO FINAL: READY_FOR_SPECIFICATION_DRIVEN_DEVELOPMENT = TRUE**

---

## 🎉 Mensaje Final Obligatorio del Agente

```
✅ PROYECTO CONFIGURADO EXITOSAMENTE

Entornos disponibles:
  • Debug (desarrollo)     → API: api-dev.example.com
  • Profile (preproducción) → API: api-staging.example.com  
  • Release (producción)   → API: api.example.com

Idiomas configurados:
  • Español (castellano) - Detectado automáticamente
  • Inglés (English) - Detectado automáticamente

El proyecto está listo para:
  • Desarrollo inmediato
  • Pruebas en múltiples entornos
  • Publicación con configuración específica por entorno
  • Ampliación de traducciones (añade nuevos archivos .arb)

Estoy preparado para continuar con:
  • Arquitectura avanzada (Bloc, Riverpod, etc.)
  • Implementación de pantallas
  • Gestión de estado
  • Integración con Firebase
  • Conexión con APIs
  • Implementación de CI/CD
```

---

## 8. 🚀 Flujo de Trabajo para Nuevas Features

> **⚡ NOTA:** Este proceso se ejecuta **automáticamente** cuando solicites crear una nueva feature. El agente seguirá este protocolo sin que tengas que recordarlo.

Cuando el usuario solicite crear una nueva feature, seguir este protocolo:

### A. Activación del Skill
```bash
skill --name flutter-expert
```
> El agente debe activar el skill de Flutter Expert al inicio de cada nueva tarea.

### B. Creación de Documentación de Feature

**1. Crear carpeta de documentación:**
```bash
mkdir -p doc/<nombre-feature>
```

**2. Archivos obligatorios en `doc/<nombre-feature>/`:**

*`SPEC.md` - Especificación de la feature:*
```markdown
# <Nombre Feature>

## Descripción
Breve descripción de la feature.

## Funcionalidades
- [ ] Funcionalidad 1
- [ ] Funcionalidad 2

## dependencias
- Paquete 1
- Paquete 2

## UI/UX
Descripción de la interfaz esperada.

## Tests
- [ ] Test unitario 1
- [ ] Test de widget 1
```

*`ARCHITECTURE.md` - Decisiones técnicas:*
```markdown
# Arquitectura - <Nombre Feature>

## Patrón utilizado
Elegir: Bloc / Riverpod / Provider / GetX / MVVM

## Capas
- **Presentation:** Widgets, Pages
- **Domain:** Entities, UseCases
- **Data:** Repositories, DataSources

## Modelos
- Modelo 1
- Modelo 2
```

*`TODO.md` - Checklist de implementación:*
```markdown
# TODO - <Nombre Feature>

## Fase 1: Setup
- [ ] Añadir dependencias
- [ ] Crear estructura de carpetas

## Fase 2: Modelos
- [ ] Crear modelo X
- [ ] Crear modelo Y

## Fase 3: Lógica de Negocio
- [ ] Implementar caso de uso 1

## Fase 4: UI
- [ ] Crear página principal
- [ ] Crear componentes

## Fase 5: Testing
- [ ] Tests unitarios
- [ ] Tests de widget
```

### C. Protocolo de Implementación

1. **SDD Update:** Actualizar `doc/<nombre-feature>/SPEC.md`
2. **Code:** Implementar código
3. **Gen:** Ejecutar `flutter pub get` y generadores necesarios
4. **Analyze:** `flutter analyze` (0 errores)
5. **Test:** Ejecutar tests
6. **Memory Log:** Registrar en `PROJECT_MEMORY.md`

---

### 🚀 ¿Iniciamos el motor?

Por favor, completa la ficha de la **Sección 1** para proceder.

1. **Nombre de la App:** 2.  **Bundle ID:** 3.  **Gestor de Estado:**
* [ ] **Riverpod**
* [ ] **Bloc / Cubit**
* [ ] **Híbrido (Pro)** (Riverpod + Bloc)