![alt text](227047079.png)

# JetLeaf Framework

🍃 JetLeaf is an enterprise-grade, modular Dart backend framework that provides a full-featured foundation for building server-side applications, microservices, and web APIs. It combines a powerful IoC container, annotation-driven configuration, environment & profile management, web primitives, templating, code-generation tools, and a rich set of utility libraries.

This repository contains the JetLeaf monorepo: core runtime modules, build tools, web server, template engine, utilities, and developer tooling used to bootstrap and operate JetLeaf-based applications.

## What JetLeaf stands for

- Simplicity: clear, annotation-driven configuration and idiomatic Dart APIs.
- Productivity: batteries-included features (DI, configuration, lifecycle, web, templates) so you can focus on business logic.
- Extensibility: modular packages and code-generation tools to integrate with existing projects and tooling.
- Robustness: lifecycle management, profile-aware configuration, conditional registration, and graceful shutdown.

## Goals and what it aims to achieve

JetLeaf aims to deliver a cohesive framework for building maintainable, testable, and deployable backend services in Dart by:

- Providing a central application context & IoC container (pod system) for wiring components and lifecycle hooks.
- Allowing environment- and profile-aware configuration across YAML/JSON/properties/Dart sources.
- Offering web primitives (routing, content negotiation, converters, sessions) to implement services and REST APIs quickly.
- Including a templates engine (JTL) for server-side rendering when needed.
- Shipping build and dev tooling to scan, generate, and optimize runtime metadata for fast startup and streamlined builds.

## Key features (overview)

- Application bootstrap and lifecycle: `JetApplication`, context creation, refresh, graceful shutdown.
- Dependency Injection: constructor/field/property injection, qualifiers, primary, lazy, scopes.
- Configuration management: property sources, placeholders, typed binding, and profile activation.
- Component scanning and stereotypes: `@Component`, `@Service`, `@Repository`, `@Controller`.
- Conditional registration: `@ConditionalOnProperty`, `@ConditionalOnPod`, `@ConditionalOnMissingPod`.
- Event system: publish/listen application events.
- Web framework: declarative controllers, routing, message converters, exception handling, file uploads, sessions.
- Templating: JTL template engine for HTML rendering with includes, loops, and conditionals.
- Tooling: code generators and dev CLI (`jl`) for scanning, generating bootstrap code and builds.

## Repository layout (modules you’ll find here)

- `jetleaf/` — Top-level package aggregating core exports and public entrypoint.
- `jetleaf_core/` — Core runtime: application context, pod lifecycle, annotations, events.
- `jetleaf_build/` — Build-time scanner and generator utilities.
- `jetleaf_web/` — HTTP server and web framework primitives (controllers, routing, converters).
- `jtl/` — Template engine (JetLeaf Template Language).
- `jetleaf_lang/`, `jetleaf_utils/`, `jetleaf_env/` — Supporting libraries (language utilities, parsers, env & property resolution).
- `jetleaf_devtool/` — Developer CLI and tooling (runtime inspection, `jl` command).
- Other focused packages: `jetleaf_logging`, `jetleaf_security`, `jetleaf_validation`, `jetleaf_convert`, `jetleaf_pod`, `jetleaf_resource`, `jetleaf_retry`, `jetleaf_scheduling`, `jetleaf_test`, `jetson` (JSON), and more.

(See each package for detailed docs and package-level README files.)

## Architecture (high level)

1. Application Bootstrap: `JetApplication` creates a bootstrap context, registers property sources, activates profiles and runs context initializers.
2. Component Discovery: runtime scanning (dev or build-time) discovers annotated classes and factory pods. Code generation via `jetleaf_build` can produce optimized declaration files.
3. Configuration & Environment: `jetleaf_env` resolves property sources (YAML, properties, environment variables, CLI args) and supports profile-specific overrides.
4. Pod Lifecycle & DI: `jetleaf_core` manages pod creation, dependency injection (constructor/field), `@PostConstruct`/`@PreDestroy`, lazy initialization, and ordering.
5. Web Layer: `jetleaf_web` dispatches HTTP requests to annotated controllers, performs content negotiation and uses message converters for (de)serialization.
6. Templates & Rendering: `jtl` renders server-side templates when building HTML responses.
7. Tooling: `jetleaf_devtool` and `jetleaf_build` provide CLI commands and generators to improve startup and deployment.

## Quick start (very small example)

1. Create an application entrypoint:

```dart
import 'package:jetleaf/jetleaf.dart';

void main(List<String> args) {
  JetApplication.run(MyApplication, args);
}

@JetLeafApplication()
class MyApplication {}
```

2. Define services and repositories:

```dart
@Service()
class UserService {
  final UserRepository repo;
  UserService(this.repo);
}

@Repository()
class UserRepository {}
```

3. Run the app:

```bash
# dart run lib/main.dart
```

For web apps, add controllers with `@RestController` and use `jetleaf_web` to start the HTTP server.

## Examples & recipes

- REST API controllers: use `@RestController`, `@GetMapping`, `@PostMapping` and argument resolvers for `@PathVariable`, `@RequestParam`, and `@RequestBody`.
- Scheduled tasks: `@Scheduled` annotations for cron/fixedDelay tasks.
- Profile-based beans: `@Profile('dev')` / `@Profile('prod')` to conditionally enable configuration.
- Conditional pods: `@ConditionalOnProperty(...)` to enable features via config flags.

## Tooling and build

- `jetleaf_build` provides scanners and generators to produce runtime declaration files and speed up startup.
- `jetleaf_devtool` includes the `jl` CLI to run dev server, generate assets, and build release artifacts.

## Contributing

Please see the package READMEs and `CONTRIBUTING.md` files in each module for contribution guidelines. Typical flow:

1. Fork the repo and create a feature branch.
2. Add/adjust tests under the package’s `test/` directory.
3. Run `dart test` for the affected package and ensure lints pass.
4. Open a PR with a clear description and reference to issues.

## Where to find more documentation

- Package-level READMEs (see the module directories) contain detailed usage and API references.
- Documentation site (if available) — links in package READMEs reference `https://jetleaf.hapnium.com`.

## License

See the repository `LICENSE` file for license terms.