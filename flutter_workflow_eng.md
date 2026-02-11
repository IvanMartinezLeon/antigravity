# Standard Flutter Project Creation with Multiple Environments and Internationalization

## Description
Verify the environment and create a new standard Flutter project with:
- Configuration for three environments (debug, profile, release) using `.env` files
- Internationalization system with support for Spanish (Castilian) and English
- Automatic device language detection
- Production-ready structure

## Content
**Agent Role:**  
You are an expert Flutter agent, tasked with preparing a professional project from scratch with environment management and internationalization.

**Mission:**  
- Verify the development environment  
- Create a project with environment structure  
- Implement i18n system with automatic detection  
- Validate complete configuration before development  

> **Important Rule:**  
> Do not continue any steps if validations fail.

## Objective
- Verified and functional development environment  
- Flutter base structure with three independent environments  
- Translation system for Spanish and English with automatic detection  
- Validated configuration files and dependencies  
- Project ready for immediate development

## Mandatory Workflow

### 1. Automatic Environment Verification (Flutter Doctor)

**Mandatory action:**  
```bash
flutter doctor
```

**Automatic validations:**
- Analyze the command output
- If there are red crosses (❌) in:
  - Flutter SDK
  - Android Toolchain
  - Chrome / Web
  - Connected device (if applicable)

**Critical error:**  
Stop the workflow immediately. Inform the user which component fails and suggest a specific correction. Do not progress until `flutter doctor` shows only minor warnings (⚠️).

### 2. Project Definition

**Pre-validations:**
- Verify if the project name was provided
- If NOT provided → ask the user
- Verify the current folder status (empty or with content)

**Automatic decision:**
```bash
# If folder is not empty
flutter create <project_name>

# If folder is empty
flutter create .
```

**Restrictions:**
- No `--org` or advanced configurations
- Standard Flutter configuration
- Project name in lowercase with hyphens (e.g., `my_app`)

### 3. Multiple Environment Configuration (.env)

**Directory structure:**
```bash
mkdir -p assets/env
touch assets/env/.env.debug
touch assets/env/.env.profile
touch assets/env/.env.release
```

**File content:**

*assets/env/.env.debug*
```env
ENVIRONMENT=debug
API_URL=https://api-dev.example.com
LOG_LEVEL=debug
FEATURE_FLAGS=true
APP_NAME="My App (DEV)"
```

*assets/env/.env.profile*
```env
ENVIRONMENT=profile
API_URL=https://api-staging.example.com
LOG_LEVEL=info
FEATURE_FLAGS=true
APP_NAME="My App (STAGING)"
```

*assets/env/.env.release*
```env
ENVIRONMENT=release
API_URL=https://api.example.com
LOG_LEVEL=error
FEATURE_FLAGS=false
APP_NAME="My App"
```

### 4. Internationalization Configuration (i18n)

**Mandatory actions:**

1. **Create translation structure:**
```bash
mkdir -p assets/translations
touch assets/translations/en.json
touch assets/translations/es.json
```

2. **Translation content:**

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

3. **Update pubspec.yaml:**
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

4. **Create environment manager (lib/config/environment.dart):**
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
    appName = dotenv.env['APP_NAME'] ?? 'My App';
  }

  static String get environmentName => switch (environment) {
        AppEnvironment.debug => 'debug',
        AppEnvironment.profile => 'profile',
        AppEnvironment.release => 'release',
      };
}
```

5. **Update lib/main.dart:**
```dart
import 'package:flutter/material.dart';
import 'package:easy_localization/easy_localization.dart';
import 'config/environment.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Initialize environment configuration
  await EnvironmentConfig.initialize();
  
  // Initialize translations with automatic detection
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

### 5. Full Structure Validation

**Mandatory files:**
- `pubspec.yaml` (with flutter_dotenv and easy_localization)
- `lib/main.dart` (with environment and i18n initialization)
- `lib/config/environment.dart`
- `assets/env/.env.debug`
- `assets/env/.env.profile`
- `assets/env/.env.release`
- `assets/translations/en.json`
- `assets/translations/es.json`

**Mandatory folders:**
- `android/`
- `ios/`
- `assets/env/`
- `assets/translations/`
- `lib/config/`
- `lib/screens/` (optional but recommended)

**Critical error:**  
If any essential file is missing, stop the flow and inform specifically which element is absent.

### 6. Dependency Download and Validation

**Action:**
```bash
flutter pub get
```

**Validations:**
- Confirm successful installation of `flutter_dotenv` and `easy_localization`
- Verify no errors in dependency resolution
- Validate that assets are properly registered

**Dependency error:**  
Stop the workflow and show the specific error. Suggest checking Flutter version and package compatibility.

### 7. Final Project Status Confirmation

**Final verifications:**
- Verified Flutter environment (clean `flutter doctor`)
- Three environments configured with .env files
- Functional i18n system with Spanish and English
- Automatic device language detection
- Full and validated project structure
- Dependencies installed correctly

**Mandatory final agent message:**  
```
PROJECT CONFIGURED SUCCESSFULLY

Available environments:
  • Debug (development)
  • Profile (staging)
  • Release (production)

Configured languages:
  • Spanish (Castilian) - Automatically detected
  • English - Automatically detected

The project is ready for:
  • Immediate development
  • Testing in multiple environments
  • Deployment with environment-specific configuration
  • Expansion of translations (simply add new JSON files)

I am prepared to continue with:
  • Advanced architecture (Bloc, Riverpod, etc.)
  • Screen implementation
  • State management
  • Firebase integration
  • API connection
  • CI/CD implementation
```

## Agent Rules
- Never expose sensitive keys in source code
- Do not assume un-specified configurations
- Do not proceed without successful validation at each step
- Do not fix errors automatically without authorization
- Prioritize stability and security over speed
- Clearly document each sensitive configuration

## Final Workflow State
```
READY_FOR_DEVELOPMENT = TRUE
ENVIRONMENTS_CONFIGURED = debug, profile, release
I18N_CONFIGURED = es, en
AUTO_LOCALE_DETECTION = ENABLED
```