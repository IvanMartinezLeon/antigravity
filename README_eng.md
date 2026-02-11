# Antigravity Flutter Project

Welcome to the Antigravity Flutter project. This repository follows strict engineering standards and a modular architecture to ensure scalability, maintainability, and production readiness.

## 📖 Key Documentation

This project is governed by several core documents that define our development standards:

- **[Engineering Rules](file:///Users/ivan.martinezl/Documents/Projects/antigravity/flutter_rules_eng.md)**: Our base for Flutter/Dart development, UI engineering, and coding standards.
- **[Architecture Contract](file:///Users/ivan.martinezl/Documents/Projects/antigravity/flutter_rules_eng.md#L422)**: Version 1.0.0 of our architectural commitment.
- **[Standard Workflow](file:///Users/ivan.martinezl/Documents/Projects/antigravity/flutter_workflow_eng.md)**: Step-by-step guide for project initialization and setup.

---

## 🛠 Engineering Principles

### 1. Architecture Contract (v1.0.0)
- **Feature-Oriented**: The main structural unit is the feature, not the technical layer.
- **Isolation**: Features are independent and strictly decoupled.
- **Composition Root**: All wiring, DI, and navigation registration happen in a single point.
- **Removability**: A feature is properly implemented only if it can be removed with minimal impact.

### 2. UI Engineering
- **Theme-Driven**: All colors, spacing (multiples of 4), and typography come from the `Theme`.
- **Framework Independence**: We don't rely on framework defaults; we use our own abstractions.
- **Stability**: UI is treated as a discipline, avoiding ad-hoc compositions.

### 3. Code Quality & Style
- **State Management**: Standard usage of **flutter_bloc + cubit**.
- **Modern Dart**: Declarative, concise, and strictly following Null Safety.
- **SOLID**: Applied pragmatically for maintainable code.

---

## 🚀 Standard Workflow Summary

Our initialization process ensures a production-ready environment:

1. **Environment Verification**: Mandatory `flutter doctor` check.
2. **Project Definition**: Standard Flutter creation.
3. **Multi-Environment Setup**: Three independent environments (debug, profile, release) using `.env` files.
4. **Internationalization (i18n)**: English and Spanish support with automatic device language detection.
5. **Validation**: Comprehensive check of structure and dependencies before development starts.

---

## 🌐 Language Policy
- **Technical Code**: Exclusively in **English** (variables, classes, comments).
- **User Interface**: Localized (English/Spanish/Catalan).
- **Assistant Interaction**: The AI assistant **always answers in Spanish**.

---

*For Spanish version, see [README_ES.md](file:///Users/ivan.martinezl/Documents/Projects/antigravity/README_ES.md)*
