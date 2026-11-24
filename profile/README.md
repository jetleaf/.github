<div align="center">

<img src="logo_sm.png" alt="Jetleaf Logo" width="180" />

# Jetleaf Framework

**Enterprise-grade, modular Dart backend framework bringing Inversion of Control, Actuator observability, and lightning-fast developer tooling to server-side Dart.**

[![pub package](https://img.shields.io/pub/v/jetleaf.svg?logo=dart&logoColor=white&color=0b2ab5)](https://pub.dev/packages/jetleaf)
[![pure AOT](https://img.shields.io/badge/AOT-100%25%20reflection--free-success.svg)](https://pub.dev/packages/jetleaf)
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![platform](https://img.shields.io/badge/platform-dart%20%7C%20server%20%7C%20cli%20%7C%20web%20%7C%20docker-lightgrey)](https://pub.dev/packages/jetleaf)
[![Actuator](https://img.shields.io/badge/Actuator-production%20ready-brightgreen.svg)](https://pub.dev/packages/jetleaf_actuator)

---

### The "Spring Boot for Dart" in the Full-Stack Flutter Era

[Documentation](https://jetleaf.hapnium.com) • [Quick Start](#-quick-start) • [Architecture](#-architecture-overview) • [Ecosystem Packages](#-monorepo-ecosystem) • [Roadmap](#-upcoming-ecosystem-roadmap)

</div>

---

## 🍃 What is Jetleaf?

**Jetleaf** is an enterprise-grade backend application framework for Dart. Inspired by modern application paradigms and Spring Boot, Jetleaf brings declarative Inversion of Control (IoC), constructor & property dependency injection, automatic component discovery, hierarchical environment profiles, production Actuator telemetry, and application lifecycle hooks to server-side Dart.

Designed from the ground up for **pure Ahead-of-Time (AOT) compilation (`dart compile exe`)**, Jetleaf uses static AST scanning and zero-reflection registries — delivering enterprise developer productivity with blazing-fast native startup times and zero runtime reflection overhead.

### The Full-Stack Dart Vision
Over 2 million developers build frontend mobile, web, and desktop apps with Flutter. Jetleaf bridges the divide by offering a unified backend experience:
* **Zero Context Switching**: Write frontend and backend in the same expressive language.
* **Shared Type Contracts**: Share models, validation rules, and DTOs across Flutter and Jetleaf without serialization drift.
* **Enterprise Ergonomics**: Enjoy dependency injection, declarative controllers, and production monitoring without leaving Dart.

---

## ✨ Key Capabilities

* **🚀 Declarative Application Bootstrap** — Boot clean microservices with `JetleafApplication.run()`, supporting banner art, PID management, environment resolution, and graceful shutdown signal handling (`SIGINT`, `SIGTERM`).
* **💉 Inversion of Control (IoC) & Pods** — Complete container lifecycle managing singleton and prototype pods, eager and lazy initialization (`@Lazy`), primary candidates (`@Primary`), and priority ordering (`@Order`).
* **⚡ 100% AOT Compilation** — Static AST analysis synthesizes deterministic constructor and method invocation tables at build time, completely eliminating runtime reflection and surviving Dart's aggressive tree-shaker.
* **📊 Production Observability (`jetleaf_actuator`)** — Built-in telemetry modeled after Spring Actuator. Exposes `/actuator/health`, `/actuator/metrics`, `/actuator/info`, system telemetry, isolate memory allocations, and thread diagnostics.
* **🌐 Declarative Web MVC (`jetleaf_web`)** — Annotation-driven REST routing with `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PathVariable`, `@RequestParam`, `@RequestBody`, and `ResponseEntity`.
* **🏎️ Sub-Second Developer Velocity (`jetleaf_tools`)** — High-performance resident compiler CLI (`jl dev`, `jl run`, `jl test`) featuring pre-warmed isolates, AST watching, and sub-100ms hot reload cycles.
* **⚙️ Hierarchical Configuration & Profiles** — Type-safe property binding (`@ConfigurationProperties`), `@Value` expression resolution, and cascade loading across command-line flags, environment variables, `.properties`, `.yaml`, and `.json`.
* **🛡️ Fault Tolerance & Enterprise Validation** — Declarative retry policies with exponential backoff (`jetleaf_retry`) and Hibernate-style constraint validation (`jetleaf_validation`).
* **🤖 AI-Native & Vibecoding Ready** — Strict typing and Model Context Protocol (`jetleaf_mcp`) integration enable AI agents (Cursor, Claude Desktop, Antigravity) to scaffold and self-heal production servers autonomously.

---

## 🚀 Quick Start

### 1. Installation

Add `jetleaf` and its core modules to your `pubspec.yaml`:

```yaml
dependencies:
  jetleaf: ^2.1.0
  jetleaf_core: ^2.1.0
  jetleaf_web: ^2.1.0
  jetleaf_actuator: ^1.1.0

dev_dependencies:
  jetleaf_tools: ^1.1.0
```

Or activate the `jl` CLI globally:

```bash
dart pub global activate jetleaf_tools
```

### 2. Write Your Application

Create a complete REST application with dependency injection and Actuator metrics in `lib/main.dart`:

```dart
import 'package:jetleaf/jetleaf.dart';
import 'package:jetleaf_web/jetleaf_web.dart';

// 1. Declare a business service pod
@Service()
class GreeterService {
  String greet(String name) => 'Welcome to Jetleaf, $name! 🍃';
}

// 2. Declare a REST Controller
@RestController()
@RequestMapping('/api/v1')
class HelloController {
  final GreeterService _greeterService;

  // Constructor dependency injection
  HelloController(this._greeterService);

  @GetMapping('/hello')
  ResponseEntity<Map<String, String>> sayHello(
    @RequestParam('name', defaultValue: 'Developer') String name,
  ) {
    return ResponseEntity.ok({
      'message': _greeterService.greet(name),
      'timestamp': DateTime.now().toIso8601String(),
    });
  }
}

// 3. Mark the application entrypoint
@JetleafApplicationStarter()
class ExampleApp {
  // Application bootstrap hooks can be placed here
}

void main(List<String> args) async {
  await JetleafApplication.run(ExampleApp(), args);
}
```

### 3. Run with Hot Reload

Start the resident JetleafVM daemon:

```bash
jl dev
```

* Hit your endpoint: `http://localhost:8080/api/v1/hello?name=Flutter`
* Inspect health telemetry: `http://localhost:8080/actuator/health`
* Inspect memory & metrics: `http://localhost:8080/actuator/metrics`

Edit any file and save — Jetleaf hot-reloads the changes in milliseconds!

---

## 🏛️ Architecture Overview

Jetleaf uses a cleanly layered, modular architecture with strict separation of concerns:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        DEVELOPER & AI TOOLING                          │
│         `jl dev` Resident VM • `jetleaf_mcp` Agent Protocols           │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                       JETLEAF WEB & ACTUATOR                           │
│     @RestController • Content Negotiation • /actuator Telemetry        │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        JETLEAF CORE (IoC)                              │
│       Typed Pods • Dependency Injection • Lifecycle • Profiles        │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                     PURE AOT COMPILATION ENGINE                        │
│        Zero runtime reflection • Static AST • `dart compile exe`       │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 📦 Monorepo Ecosystem

This repository is organized as a unified monorepo:

| Package | Version | Description |
|:---|:---:|:---|
| **[`jetleaf`](https://pub.dev/packages/jetleaf)** | `2.1.0` | Top-level application aggregator, bootstrap coordinator, and runner. |
| **[`jetleaf_core`](https://pub.dev/packages/jetleaf_core)** | `2.1.0` | Core IoC container, Pod definitions, lifecycle processors, and environment management. |
| **[`jetleaf_tools`](https://pub.dev/packages/jetleaf_tools)** | `1.1.0` | Resident compiler CLI (`jl`), `JetleafVM` isolate manager, and AST scanner. |
| **[`jetleaf_actuator`](https://pub.dev/packages/jetleaf_actuator)** | `1.1.0` | Production monitoring and telemetry endpoints (`/actuator/health`, `/metrics`, `/info`). |
| **[`jetleaf_web`](https://pub.dev/packages/jetleaf_web)** | `2.1.0` | Enterprise Web MVC, REST controllers, parameter resolvers, and HTTP servers. |
| **[`jetleaf_retry`](https://pub.dev/packages/jetleaf_retry)** | `2.1.0` | Declarative retries, exponential backoffs, and circuit-breaker patterns (`@Retryable`). |
| **[`jetleaf_validation`](https://pub.dev/packages/jetleaf_validation)** | `2.1.0` | Declarative validation constraints (`@Valid`, `@NotNull`, `@Size`, `@Email`). |
| **[`jetleaf_resource`](https://pub.dev/packages/jetleaf_resource)** | `2.1.0` | Resource caching, LRU/LFU eviction, TTL expiration, and rate limiting. |
| **[`jetleaf_scheduling`](https://pub.dev/packages/jetleaf_scheduling)** | `2.1.0` | Task scheduling and timed execution (`@Scheduled`, `@Cron`). |
| **[`jetson`](https://pub.dev/packages/jetson)** | `2.1.0` | High-performance JSON serialization engine and schema mapping utilities. |
| **[`jtl`](https://pub.dev/packages/jtl)** | `2.1.0` | Jetleaf Template Language for server-side HTML rendering. |

---

## 🗺️ Upcoming Ecosystem Roadmap

Jetleaf is expanding into a comprehensive enterprise application suite:

* 🗄️ **`jetleaf_data`** *(Spring Data for Dart)*: Declarative `Repository<T, ID>`, dynamic method query derivation (`findByEmail`), unified SQL (Postgres, MySQL, SQLite) and NoSQL (MongoDB, Redis) dialects, connection pooling, and `@Transactional` zone-scoped rollback management.
* 🔒 **`jetleaf_security`** *(Spring Security for Dart)*: Declarative `SecurityFilterChain` pipeline, CORS, CSRF, rate-limiting, stateless asymmetric/symmetric JWT engine, OAuth2/OIDC resource servers, and `@PreAuthorize` method security.
* 🤖 **`jetleaf_mcp`** *(Model Context Protocol Server)*: Exposing project scaffolding, AST inspection, route tables, and warm-VM test runners directly to AI agents (Cursor, Windsurf, Claude Desktop, Antigravity) for autonomous vibecoding.
* 🎨 **Jetleaf Studio / Builder**: Visual schema design and API scaffolding web platform located under `builder/`.
* 📢 **Jetleaf Marketing Engine**: 24/7 automated developer advocacy pipeline via GitHub Actions located under `marketing/`.

*(All upcoming packages on this roadmap are still in design — none of them are published to pub.dev yet.)*

---

## 🛠️ The `jl` Developer CLI

The `jetleaf_tools` CLI provides first-class developer tooling:

```bash
# Start resident dev manager with hot reload and isolate pre-warming
jl dev

# Run application in pre-warmed VM
jl run lib/main.dart

# Run discovered @JetleafTest test suites in pre-warmed VM
jl test --all

# Create a new Jetleaf project from starter templates
jl create my_microservice

# Build an optimized, native Ahead-of-Time executable
jl build --release
```

---

## 🤝 Contributing

We welcome contributions from the Dart and Flutter community!

1. Fork the repository and create your feature branch: `git checkout -b feature/amazing-feature`.
2. Ensure all tests pass across packages: `dart test`.
3. Verify that analysis reports zero warnings: `dart analyze`.
4. Open a Pull Request with a clear summary of your changes.

---

## 📄 License

Jetleaf is licensed under the [MIT License](LICENSE).
Built with ❤️ by [Hapnium](https://hapnium.com) and the Jetleaf Contributors.
