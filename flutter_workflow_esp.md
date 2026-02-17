# Creación de Proyecto Flutter Estándar con Múltiples Entornos e Internacionalización

## Descripción
Verificar el entorno y crear un nuevo proyecto Flutter estándar con:
- Configuración para tres entornos (debug, profile, release) mediante archivos `.env`
- Sistema de internacionalización con soporte para español (castellano) e inglés
- Detección automática del idioma del dispositivo
- Estructura lista para producción

## Contenido
**Rol del Agente:**  
Eres un agente experto en Flutter, encargado de preparar desde cero un proyecto profesional con gestión de entornos e internacionalización.

**Misión:**  
- Verificar el entorno de desarrollo  
- Crear proyecto con estructura de entornos  
- Implementar sistema i18n con detección automática  
- Validar configuración completa antes del desarrollo  

> **Regla importante:**  
> No continúes ningún paso si las validaciones fallan.

## Objetivo
- Entorno de desarrollo verificado y funcional  
- Estructura base de Flutter con tres entornos independientes  
- Sistema de traducciones para español e inglés con detección automática  
- Archivos de configuración y dependencias validados  
- Proyecto listo para desarrollo inmediato

## Flujo de Trabajo Obligatorio

### 1. Verificación Automática del Entorno (Flutter Doctor)

**Acción obligatoria:**  
```bash
flutter doctor
```

**Validaciones automáticas:**
- Analiza la salida del comando
- Si hay cruces rojas (❌) en:
  - Flutter SDK
  - Android Toolchain
  - Chrome / Web
  - Dispositivo conectado (si aplica)

**Error crítico:**  
Detén el workflow inmediatamente. Informa al usuario qué componente falla y sugiere corrección específica. No avances hasta que `flutter doctor` muestre solo advertencias menores (⚠️).

### 2. Definición del Proyecto

**Validaciones previas:**
- Verifica si el nombre del proyecto fue proporcionado
- Si NO fue proporcionado → pregunta al usuario
- Verifica estado de la carpeta actual (vacía o con contenido)

**Decisión automática:**
```bash
# Si carpeta no está vacía
flutter create <nombre_proyecto>

# Si carpeta está vacía
flutter create .
```

**Restricciones:**
- Sin `--org` ni configuraciones avanzadas
- Configuración estándar de Flutter
- Nombre del proyecto en minúsculas con guiones (ej: `mi_app`)

### 3. Configuración de Múltiples Entornos (.env)

**Estructura de directorios:**
```bash
mkdir -p assets/env
touch assets/env/.env.debug
touch assets/env/.env.profile
touch assets/env/.env.release
```

**Contenido de archivos:**

*assets/env/.env.debug*
```env
ENVIRONMENT=debug
API_URL=https://api-dev.example.com
LOG_LEVEL=debug
FEATURE_FLAGS=true
APP_NAME="Mi App (DEV)"
```

*assets/env/.env.profile*
```env
ENVIRONMENT=profile
API_URL=https://api-staging.example.com
LOG_LEVEL=info
FEATURE_FLAGS=true
APP_NAME="Mi App (STAGING)"
```

*assets/env/.env.release*
```env
ENVIRONMENT=release
API_URL=https://api.example.com
LOG_LEVEL=error
FEATURE_FLAGS=false
APP_NAME="Mi App"
```

### 4. Configuración de Internacionalización (i18n)

**Acciones obligatorias:**

1. **Crear estructura de traducciones:**
```bash
mkdir -p assets/translations
touch assets/translations/en.json
touch assets/translations/es.json
```

2. **Contenido de traducciones:**

*assets/translations/en.json*
```json
{
  "app_name": "My App",
  "welcome": "Welcome",
  "settings": "Settings",
  "language": "Language",
  "environment": "Environment: {env}",
  "debug": "Debug",
  "profile": "Profile",
  "release": "Release"
}
```

*assets/translations/es.json*
```json
{
  "app_name": "Mi App",
  "welcome": "Bienvenido",
  "settings": "Ajustes",
  "language": "Idioma",
  "environment": "Entorno: {env}",
  "debug": "Desarrollo",
  "profile": "Preproducción",
  "release": "Producción"
}
```

3. **Actualizar pubspec.yaml:**
```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_dotenv: ^5.1.0
  easy_localization: ^3.0.3

flutter:
  uses-material-design: true
  assets:
    - assets/env/.env.debug
    - assets/env/.env.profile
    - assets/env/.env.release
    - assets/translations/
```

4. **Crear gestor de entornos (lib/config/environment.dart):**
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

5. **Actualizar lib/main.dart:**
```dart
import 'package:flutter/material.dart';
import 'package:easy_localization/easy_localization.dart';
import 'config/environment.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Inicializar configuración de entornos
  await EnvironmentConfig.initialize();
  
  // Inicializar traducciones con detección automática
  await EasyLocalization.ensureInitialized();
  
  runApp(
    EasyLocalization(
      supportedLocales: const [Locale('es'), Locale('en')],
      path: 'assets/translations',
      fallbackLocale: const Locale('es'),
      useOnlyLangCode: true,
      child: const MyApp(),
    ),
  );
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'app_name'.tr(),
      localizationsDelegates: context.localizationDelegates,
      supportedLocales: context.supportedLocales,
      locale: context.locale,
      debugShowCheckedModeBanner: false,
      theme: ThemeData(
        primarySwatch: Colors.blue,
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
    final envLabel = switch (EnvironmentConfig.environment) {
      AppEnvironment.debug => 'debug'.tr(),
      AppEnvironment.profile => 'profile'.tr(),
      AppEnvironment.release => 'release'.tr(),
    };

    return Scaffold(
      appBar: AppBar(
        title: Text('app_name'.tr()),
        actions: [
          PopupMenuButton<Locale>(
            onSelected: (locale) => context.setLocale(locale),
            itemBuilder: (context) => [
              const PopupMenuItem(value: Locale('es'), child: Text('🇪🇸 Español')),
              const PopupMenuItem(value: Locale('en'), child: Text('🇬🇧 English')),
            ],
          ),
        ],
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text('welcome'.tr(), style: Theme.of(context).textTheme.headlineMedium),
            const SizedBox(height: 16),
            Text(
              'environment'.tr(namedArgs: {'env': envLabel}),
              style: Theme.of(context).textTheme.bodyLarge,
            ),
            const SizedBox(height: 24),
            ElevatedButton(
              onPressed: () => Navigator.push(
                context,
                MaterialPageRoute(builder: (_) => const SettingsScreen()),
              ),
              child: Text('settings'.tr()),
            ),
          ],
        ),
      ),
    );
  }
}

class SettingsScreen extends StatelessWidget {
  const SettingsScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('settings'.tr())),
      body: Center(
        child: Text('language'.tr()),
      ),
    );
  }
}
```

### 5. Principios de Arquitectura Limpia (Antigravity)

**Acciones obligatorias:**

1. **Estructura orientada a Features:**
   ```bash
   mkdir -p lib/features/<feature_name>/presentation/{widgets,screens}
   mkdir -p lib/features/<feature_name>/domain/{entities,repositories}
   mkdir -p lib/features/<feature_name>/data/{repositories,datasources}
   ```

2. **Reglas del Contrato:**
   - **Aislamiento:** Las features no dependen entre sí directamente.
   - **Punto de Entrada:** Cada feature expone un archivo `public_api.dart`.
   - **Composition Root:** El wiring ocurre solo en `main.dart` o un `injection_container.dart`.

3. **Gestión de Estado:**
   - Usar `flutter_bloc` / `cubit` para lógica de negocio.
   - No instanciar Blocs dentro de otras features.

### 6. Software Sostenible (Green Software & SCI)

**Misión:** Minimizar la huella de carbono mediante eficiencia energética y hardware.

1. **Cálculo del SCI (Software Carbon Intensity):**
   La métrica para evaluar la sostenibilidad del software es:
   > **SCI = ((E * I) + M) / R**
   - **E:** Energía consumida (kWh).
   - **I:** Intensidad de carbono de la red (gCO2e/kWh).
   - **M:** Emisiones de carbono embebidas del hardware.
   - **R:** Unidad funcional (ej: por usuario, por petición).

2. **Estrategias en Flutter:**
   - **Eficiencia Energética:**
     - Usar `const` para evitar reconstrucciones.
     - `RepaintBoundary` para aislar partes complejas de la UI.
     - Evitar animaciones infinitas en segundo plano.
   - **Eficiencia de Hardware:**
     - Minimizar el uso de CPU/GPU procesando datos localmente solo cuando sea necesario.
     - Reducir el tamaño del bundle para minimizar la energía de descarga (M).
   - **Eficiencia de Datos:**
     - Peticiones batch y caché agresiva para reducir tráfico de red.

### 7. Validación de Estructura Completa

**Archivos obligatorios:**
- `pubspec.yaml` (con flutter_dotenv y easy_localization)
- `lib/main.dart` (con inicialización de entornos e i18n)
- `lib/config/environment.dart`
- `assets/env/.env.debug`
- `assets/env/.env.profile`
- `assets/env/.env.release`
- `assets/translations/en.json`
- `assets/translations/es.json`

**Carpetas obligatorias:**
- `android/`
- `ios/`
- `assets/env/`
- `assets/translations/`
- `lib/config/`
- `lib/screens/` (opcional pero recomendado)

**Error crítico:**  
Si falta cualquier archivo esencial, detén el flujo e informa específicamente qué elemento está ausente.

### 8. Descarga y Validación de Dependencias

**Acción:**
```bash
flutter pub get
# Verificar y actualizar a últimas versiones mayores
flutter pub upgrade --major-versions
```

**Validaciones:**
- Confirmar instalación exitosa de `flutter_dotenv` y `easy_localization`
- Verificar que no haya errores en la resolución de dependencias
- Validar que los assets estén correctamente registrados

**Error de dependencias:**  
Detén el workflow y muestra el error específico. Sugiere revisar versión de Flutter y compatibilidad de paquetes.

### 9. Confirmación Final del Estado del Proyecto

**Verificaciones finales:**
- Entorno Flutter verificado (`flutter doctor` limpio)
- Tres entornos configurados con archivos .env
- Sistema i18n funcional con español e inglés
- Detección automática de idioma del dispositivo
- Estructura de proyecto completa y validada
- Arquitectura Limpia (Antigravity) aplicada
- Principios de Green Software e indicadores SCI listos
- Dependencias instaladas correctamente
- Limpiar comentarios del código

**Mensaje final obligatorio del agente:**  
```
PROYECTO CONFIGURADO EXITOSAMENTE

Entornos disponibles:
  • Debug (desarrollo)
  • Profile (preproducción)
  • Release (producción)

Idiomas configurados:
  • Español (castellano) - Detectado automáticamente
  • Inglés (English) - Detectado automáticamente

Sostenibilidad y Arquitectura:
  • Contrato Antigravity 1.0.0 activo
  • Prácticas de Green Software integradas
  • Métricas SCI preparadas para monitorización

El proyecto está listo para:
  • Desarrollo inmediato
  • Pruebas en múltiples entornos
  • Publicación con configuración específica por entorno
  • Ampliación de traducciones (simplemente añade nuevos archivos JSON)

Estoy preparado para continuar con:
  • Arquitectura avanzada (Bloc, Riverpod, etc.)
  • Implementación de pantallas
  • Gestión de estado
  • Integración con Firebase
  • Conexión con APIs
  • Implementación de CI/CD
```

## Reglas del Agente
- Nunca expongas claves sensibles en código fuente
- No asumas configuraciones no especificadas
- No avances sin validación exitosa en cada paso
- No corrijas errores automáticamente sin autorización
- Prioriza estabilidad y seguridad sobre velocidad
- Documenta claramente cada configuración sensible

## Estado Final del Workflow
```
READY_FOR_DEVELOPMENT = TRUE
ENVIRONMENTS_CONFIGURED = debug, profile, release
I18N_CONFIGURED = es, en
AUTO_LOCALE_DETECTION = ENABLED
```