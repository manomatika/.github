# ManoMatika

ManoMatika is a plugin-extensible application framework ecosystem. The product,
**ManoMatika**, is a QA-validated, pinned triple of three component versions: the
**matika** framework, one or more AppLugs (plugins), and the **ahimsa** recipe
engine that composes and validates them.

The core principle: **matika itself has zero knowledge of any business domain.**
All domain logic lives in plugins (AppLugs). The framework provides the runtime —
auth, RBAC, menus, persistence, CSRF, i18n, rate limiting — and a clean plugin
contract for everything else.

## Hierarchy

```
manomatika          product authority — the product is a pinned, QA-validated component triple
├── matika          the framework (plugin-agnostic FastAPI host)
│   └── eyerate     reference AppLug (plugin; loads into and depends on matika)
└── ahimsa          recipe engine (build / validation / release mechanism)
```

`matika` + `eyerate` are the **runtime stack** — the application that actually
runs. `ahimsa` is **mechanism**: it assembles and validates the application from
the outside, but is not part of the running app. `manomatika` is the **authority**
that pins a specific, validated combination and releases it as the product.

## Repositories

### 📦 [manomatika](https://github.com/manomatika/manomatika)
The **product authority**. A ManoMatika release is not any single repo's release —
it is this repository naming an exact set of component versions built and validated
together. It owns: the recipes that compose a product; the audit log
(`release-log.yaml` source + generated `RELEASES.md`); the per-version
manifest/BOM (each component pinned by **tag and resolved commit SHA**); the
product release and the single hosted installer binary; the cross-component
umbrella docs; and the QA gate a product version must pass before release.

### 🧩 [matika](https://github.com/manomatika/matika)
The framework. A FastAPI host with bcrypt + JWT + OAuth auth, role-based access
control, a server-side-filtered menu system, Alembic-managed core schema (SQLite
dev / PostgreSQL / MySQL prod), and a published frontend package
(`@manomatika/matika-frontend`) for AppLug UIs. The core ships with no business
features — every page beyond login and settings is contributed by an AppLug.

#### 📊 [eyerate](https://github.com/manomatika/eyerate)
The reference AppLug, and a child of matika: it declares an exact `matika_version`,
loads into the framework at runtime, and consumes `@manomatika/matika-frontend`.
It demonstrates the full plugin contract end-to-end: manifest, consolidated menu
file, role-scoped routes, Jinja2 templates, TypeScript admin pages, pluggable data
providers (Yahoo / Finnhub / Alpha Vantage), and plugin-managed database tables.
Adds financial security tracking (stocks, bonds, ETFs, mutual funds) to any matika
host. The canonical example to learn the AppLug system from.

### 🛠️ [ahimsa](https://github.com/manomatika/ahimsa)
The recipe **engine** — build, validation, and release *mechanism* only. A recipe
declares the exact matika version and exact AppLug versions that compose an
application, with no ranges and no wildcards. The validator enforces cross-component
version consistency, verifies remote `applug.json` manifests at the declared GitHub
tag, and checks `RELEASES.md` ↔ git-tag drift. The build pipeline produces installer
artifacts (macOS DMG / Windows EXE), triggered either on demand via
`workflow_dispatch` or by an rc/final tag push (classified into an rc or final
release channel). ahimsa owns no recipe *content* and hosts no product
releases — it hands artifacts off to `manomatika`. (Code-signing and
notarization are on the roadmap.)

## Mental Model

| Layer | Role |
|---|---|
| **manomatika** | Product authority — pins a validated component triple; owns the recipes, manifest, audit log, product release, and QA gate |
| **matika** | Plugin-agnostic FastAPI framework — runtime, auth, RBAC, menus |
| **applugs** | Plugins providing all business-domain features (e.g. eyerate) |
| **ahimsa** | Recipe engine — builds and validates an application from pinned component versions |

## Compatibility & Release Discipline

Every AppLug declares the exact `matika_version` it was built and tested against.
matika checks this at plugin load and refuses incompatible AppLugs. Recipes enforce
the same invariant statically: all AppLugs in a recipe must declare an identical
`matika_version`, and that version must match the bundled matika.

`VERSION` is the single source of truth for version metadata in each repo; tooling
propagates it to all version-bearing files and detects drift in CI. Recipes pin
each component by exact version — no ranges, no wildcards — and a product manifest
pins each by **tag and resolved commit SHA**.

`RELEASES.md` is the canonical, manomatika-wide tag log. Its human-edited source
(`release-log.yaml`) and the generated `RELEASES.md` live in **manomatika**; ahimsa
provides the rendering engine that produces the log from manomatika-hosted data and
validates it bidirectionally against git tags. Entries are repo-aware
(`## <repo> vX.Y.Z`).

Components ship as **prereleases**; nothing is blessed before QA. Only after a
candidate passes the QA gate is the **ManoMatika product release** cut, with the
validated installer attached.

## Project Status

All repositories are in active early development (v0.0.x). The first ManoMatika
product release is **v0.0.1 = matika v0.0.4 + eyerate v0.0.4 + ahimsa v0.0.1**,
currently in the release-engineering sequence. Backward compatibility within a
matika minor version is mandatory.

---
*Maintained by [Patrick James Tallman](https://github.com/pjtallman).*
