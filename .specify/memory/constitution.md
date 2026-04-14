<!--
==============================================================================
SYNC IMPACT REPORT
==============================================================================
Version change: unversioned (template) → 1.0.0

Added Sections:
  - Core Principles (I–VII: Flutter-First, Firebase-Native, Design Excellence,
    Real-Time & Offline-First, Security-by-Default, Localization, GitHub-Driven)
  - Technology Stack Constraints
  - Development Workflow
  - Governance

Modified Principles (template → concrete):
  - [PRINCIPLE_1_NAME] → I. Flutter-First Architecture
  - [PRINCIPLE_2_NAME] → II. Firebase-Native Backend
  - [PRINCIPLE_3_NAME] → III. Design Excellence (NON-NEGOTIABLE)
  - [PRINCIPLE_4_NAME] → IV. Real-Time & Offline-First
  - [PRINCIPLE_5_NAME] → V. Security-by-Default
  - [SECTION_2_NAME]   → VI. RTL & Localization
  - [SECTION_3_NAME]   → VII. GitHub-Driven Development

Templates updated:
  ✅ .specify/memory/constitution.md (this file)
  ⚠️  .specify/templates/plan-template.md — Constitution Check section
      references generic gates; should cite these principles by number.
  ⚠️  .specify/templates/tasks-template.md — task categories should include
      Localization (RTL) and Observability tasks per Principle VI & VII.

Deferred TODOs:
  - RATIFICATION_DATE: project start date inferred as 2026-04-14 (today).
==============================================================================
-->

# Shared Space – Flutter App Constitution

## Core Principles

### I. Flutter-First Architecture

Every screen, widget, and business-logic layer MUST be implemented in pure
Flutter/Dart. No platform channels or native modules may be introduced unless
no Flutter/Dart equivalent exists. The technology stack is non-negotiable:

- **Framework**: Flutter 3.24+ (stable channel), Dart 3.5+
- **State management**: Riverpod 2.5+ (providers + code generation); no
  other state management library may be added.
- **Routing**: `go_router` with declarative route definitions and auth guards.
- **Data models**: `freezed` + `json_serializable`; models MUST be immutable.
- **Code generation**: `build_runner` MUST be run after model changes.
- **Target platform**: Android API 24+ (MVP); iOS not in scope for Phase 1.

**Rationale**: A single consistent stack minimises cognitive overhead,
maximises code reuse, and ensures animations and UI behave identically
across all devices.

### II. Firebase-Native Backend

The app MUST use the existing Firebase project (`chaty-86c83`) via the MCP
Firebase integration. No alternative backend may be introduced.

- **Auth**: `firebase_auth` — Google Sign-In + Email/Password; no custom
  auth server.
- **Database**: `cloud_firestore` with the **existing flat-collection schema**
  defined in `firebase-blueprint.json`. Collections are top-level, not nested
  subcollections (e.g., `/apartmentMembers/{apartmentId_userId}`).
- **Push notifications**: `firebase_messaging` (FCM) — server-side triggers
  via Cloud Functions or Firestore triggers.
- **Offline persistence**: Firestore offline cache MUST be enabled with
  `cacheSizeBytes: Settings.CACHE_SIZE_UNLIMITED`.
- **Firestore rules**: All reads/writes MUST pass the rules in
  `firestore.rules`. The MCP Firebase tool MUST be used to validate rule
  changes before deployment.
- **GitHub integration**: A GitHub repository MUST be created and maintained
  via the MCP GitHub tool; the README MUST be updated with every significant
  feature or architectural change.

**Rationale**: The database already contains production data from the web
version of this product. Schema changes that break the web app are forbidden.

### III. Design Excellence (NON-NEGOTIABLE)

The UI MUST match the design system specified in PRD section 6. There is zero
tolerance for placeholder styling or "MVP-only" visual shortcuts. Every screen
shipped MUST satisfy all of the following before it is considered done:

- Uses only the defined colour palette (`AppColors`), typography
  (`AppTextStyles`), spacing (`AppSpacing`), and elevation (`AppShadows`).
- All primary CTAs and stat cards use the defined gradient system
  (`AppGradients`).
- Animations MUST be present: page transitions (fade+slide 300 ms),
  bottom-sheet open (slide-up 350 ms cubic-bezier), button press
  (scale 0.95 / 100 ms), and completion celebrations (confetti/checkmark).
- Loading states MUST use shimmer, not generic spinners.
- Fonts: Outfit (Latin) and Tajawal (Arabic) via `google_fonts`.
- Bottom navigation MUST implement the Liquid Glass style per PRD §6.9.8.
- Empty states MUST include an illustration and an actionable CTA.

**Rationale**: The app competes directly on user experience quality. A
beautiful, polished app retains users; a plain one does not.

### IV. Real-Time & Offline-First

All data that changes across devices MUST use Firestore `StreamProvider`
(real-time listeners). Pull-to-refresh patterns are forbidden for live data.

- Hive MUST be used for user preferences, current apartment selection, and
  pending-action queues (see PRD §8.3).
- `connectivity_plus` MUST drive an offline banner in the UI.
- Pending writes queued offline MUST retry automatically on reconnection with
  exponential back-off; actions failing after 5 retries MUST surface a
  user-visible failure notification.
- Real-time updates MUST be visible to all users within 1 second under normal
  network conditions.

**Rationale**: Shared-living coordination requires everyone to see the same
state instantly; stale data causes conflicts.

### V. Security-by-Default

All Firestore access MUST be gated by the rules in `firestore.rules`.
Client-side validation MUST always be accompanied by an equivalent server-side
rule. No rule may grant broader access than the minimum required.

- The `isGlobalAdmin()` helper (tied to `hwmk2004@gmail.com`) MUST NOT be
  expanded or replicated; it exists solely for production support.
- Apartment-scoped resources MUST verify `isApartmentMember` via the
  `apartmentMembers/{apartmentId_userId}` document pattern.
- Auth tokens MUST be refreshed; no long-lived tokens stored in Hive.
- API keys in `firebase-applet-config.json` MUST NOT be committed to a public
  repository unguarded; use `.gitignore` or equivalent Firebase App Check.

**Rationale**: The database holds financial and personal data for real
households; a breach would have direct real-world consequences.

### VI. RTL & Localization

The app MUST support English (LTR) and Arabic (RTL) from the very first
screen. Localization is NOT a post-MVP concern.

- `easy_localization` is the only permitted localization package.
- All user-facing strings MUST reside in `assets/translations/en.json` and
  `assets/translations/ar.json`; hardcoded strings are forbidden.
- Layout mirroring MUST be validated on every screen via `Directionality`.
- Directional assets (arrows, back/forward icons) MUST flip automatically
  based on locale.
- Date, time, and currency formatting MUST respect locale via `intl`.

**Rationale**: Arabic-speaking users are a primary market segment; shipping
without RTL is not acceptable.

### VII. GitHub-Driven Development

The GitHub repository (created via MCP GitHub tool) is the single source of
truth for code, documentation, and progress tracking.

- Every constitutionally significant feature MUST be developed on a named
  feature branch and merged via a Pull Request.
- The README MUST be updated to reflect new features after each merge.
- Issues linked to PRD phases MUST be created via the MCP GitHub tool and
  closed when the corresponding feature ships.
- Breaking changes to the Firestore schema or security rules MUST be
  documented in the PR description and reflected in `firebase-blueprint.json`
  and `firestore.rules`.

**Rationale**: A well-maintained public repository builds project credibility
and provides a transparent audit trail for all stakeholders.

## Technology Stack Constraints

These constraints are derived from the PRD and are non-negotiable unless
this constitution is formally amended (see Governance).

| Concern | Chosen Solution | Alternative Forbidden |
|---|---|---|
| State Management | Riverpod 2.5+ | Provider, Bloc, GetX |
| Navigation | go_router | Navigator 1.0, auto_route |
| Data Models | freezed + json_serializable | Manual models, drift |
| Local Cache | Hive + hive_flutter | SharedPreferences (for complex data) |
| Localization | easy_localization | flutter_i18n, intl alone |
| Firebase Firestore ID | `chaty-86c83` | Any other project |
| Firestore DB ID | `ai-studio-a21ea095-7636-4180-8c27-e7cc691f29fe` | default |
| Animations | flutter_animate or lottie (AI chooses) | ad-hoc AnimationController only |
| Icons | lucide_icons_flutter | material_icons alone |

## Development Workflow

1. **Spec-first**: Every feature begins with a `speckit-specify` run, producing
   `specs/{feature}/spec.md` before any code is written.
2. **Plan next**: `speckit-plan` generates `plan.md`, `data-model.md`, and
   `research.md` before tasks are created.
3. **Task-driven**: `speckit-tasks` produces `tasks.md`; tasks MUST be
   completed in dependency order.
4. **Firebase MCP for data ops**: Any query, document read, or validation
   against the live database MUST use the Firebase MCP tools — never raw curl
   or hardcoded test scripts.
5. **GitHub MCP for repo ops**: Repository creation, branch management, PR
   creation, and README updates MUST use the GitHub MCP tools.
6. **Constitution Check in plans**: Every `plan.md` MUST include a
   Constitution Check section verifying compliance with Principles I–VII.
7. **Commit hooks**: The `after_constitution`, `after_specify`, `after_plan`,
   `after_tasks`, and `after_implement` hooks defined in
   `.specify/extensions.yml` MUST be respected.

## Governance

This constitution supersedes all other project conventions and ad-hoc
practices. It applies to every file, widget, service, and rule in this
repository.

**Amendment procedure**:
1. Open a GitHub issue describing the required change and its rationale.
2. Update `constitution.md` with a version bump (see versioning policy below).
3. Update the Sync Impact Report comment at the top of this file.
4. Create a PR; the PR description MUST cite which principle(s) are affected.
5. Merge only after explicit human approval.

**Versioning policy**:
- **MAJOR** (`X.0.0`): Removal or incompatible redefinition of a principle.
- **MINOR** (`X.Y.0`): New principle, new section, or materially expanded guidance.
- **PATCH** (`X.Y.Z`): Clarifications, wording, typo fixes.

**Compliance review**: Every `plan.md` Constitution Check gate MUST be
reviewed before implementation tasks begin. Any violation requires a
documented justification in the plan's Complexity Tracking table.

**Guidance file**: Use `.specify/memory/constitution.md` (this file) as the
authoritative runtime reference for all AI agent sessions.

**Version**: 1.0.0 | **Ratified**: 2026-04-14 | **Last Amended**: 2026-04-14
