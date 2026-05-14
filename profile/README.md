# ManoMatika

ManoMatika builds **Matika**, a plugin-agnostic FastAPI framework for extensible
applications, along with the AppLugs and release tooling that make up its ecosystem.

The core principle: Matika itself has zero knowledge of any business domain.
All domain logic lives in plugins (AppLugs). The framework provides the runtime —
auth, RBAC, menus, persistence, CSRF, i18n, rate limiting — and a clean plugin
contract for everything else.

## Repositories

### 🧩 [Matika](https://github.com/manomatika/Matika)
The framework. A FastAPI host with bcrypt + JWT + OAuth auth, role-based access
control, a server-side-filtered menu system, Alembic-managed core schema (SQLite
dev / PostgreSQL / MySQL prod), and a published frontend package
(`@manomatika/matika-frontend`) for AppLug UIs. The core ships with no business
features — every page beyond login and settings is contributed by an AppLug.

### 📊 [EyeRate](https://github.com/manomatika/EyeRate)
The reference AppLug. Demonstrates the full plugin contract end-to-end:
manifest, consolidated menu file, role-scoped routes, Jinja2 templates,
TypeScript admin pages, pluggable data providers (Yahoo / Finnhub / Alpha
Vantage), and plugin-managed database tables. Adds financial security tracking
(stocks, bonds, ETFs, mutual funds) to any Matika host. The canonical example to
learn the AppLug system from.

### 📦 [Ahimsa](https://github.com/manomatika/ahimsa)
The build, validation, and release system. A *recipe repo* — `recipe.json`
declares the exact Matika version and exact AppLug versions that compose an
application, with no ranges and no wildcards. The validator enforces
cross-component version consistency, verifies remote `applug.json` manifests at
the declared GitHub tag, and checks `RELEASES.md` ↔ git-tag drift. The build
pipeline produces signed DMG/EXE installers; recipes are the lockfile.

## Mental Model

| Layer | Role |
|---|---|
| **Matika** | Plugin-agnostic FastAPI framework — runtime, auth, RBAC, menus |
| **AppLugs** | Plugins providing all business-domain features |
| **Ahimsa** | Recipe-based build pipeline assembling applications from pinned versions |

## Compatibility & Release Discipline

Every AppLug declares the exact `matika_version` it was built and tested against.
Matika checks this at plugin load and refuses incompatible AppLugs. Recipes
enforce the same invariant statically: all AppLugs in a recipe must declare an
identical `matika_version`, and that version must match the bundled Matika.

Releases follow a strict pipeline. `VERSION` is the single source of truth in
each repo; tooling propagates it to all version-bearing files and detects drift
in CI. `RELEASES.md` is the canonical tag log — every git tag matching `vX.Y.Z`
must have a corresponding entry, and Ahimsa validates this bidirectionally
across every repo a recipe references.

## Project Status

All three repos are in active early development (v0.0.x). v0.0.4 is the first
cycle where Matika, EyeRate, and Ahimsa are released together end-to-end.
Backward compatibility within a Matika minor version is mandatory.

---

*Maintained by [Patrick James Tallman](https://github.com/pjtallman).*
