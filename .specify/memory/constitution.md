<!--
SYNC IMPACT REPORT
==================
Version change: 1.0.0 → 1.1.0
Bump rationale: MINOR — materially expanded guidance
  (5 resolved decisions, 3 principle refinements, 1 new section).
  No principles removed or reordered.

Modified principles:
  - VI. Atomic Skills — added composability clause
  - IX. Showcase as Documentation — scoped to template-repo;
    meta-repo follows conventional docs
  - X. Spec-Driven in Itself — reframed to clarify meta-repo
    scope; end users do not use spec-kit

Added sections:
  - Resolved Decisions (replaces Points to Clarify, with
    CL-01 through CL-05 decisions documented)
  - Repository Topology (two-repo architecture: meta + template)

Removed sections:
  - Points to Clarify (resolutions integrated into
    Resolved Decisions — original tension context preserved)

Templates requiring updates:
  - .specify/templates/plan-template.md — ✅ compatible
    (Constitution Check is dynamic; refined principles are
    picked up at runtime. Two-repo topology does not affect
    the plan template structure.)
  - .specify/templates/spec-template.md — ✅ compatible
    (principle-agnostic structure; no conflicts with refined
    scoping of IX and X.)
  - .specify/templates/tasks-template.md — ⚠ advisory
    (Phase N "Documentation updates in docs/" aligns with the
    new meta-repo docs allowance in Principle IX. No structural
    conflict, but task generation should be aware that docs/
    tasks apply to the meta-repo context, not the template-repo.)
  - .specify/templates/constitution-template.md — ✅ no update
    (scaffold template; this file is the live instance)
  - README.md — ⚠ pending (should reflect two-repo topology
    once project README is fleshed out)

Follow-up TODOs:
  - Create the template-repo when ready for first distribution
  - Define the meta → template sync/release process
  - Build the DESIGN.md token editing skill described in RD-02
  - Create /decisions route scaffold in the showcase
  - Create post-eject placeholder files (welcome.mdx,
    getting-started.mdx, etc.) per RD-04
-->

# Design System Template Constitution

## Core Principles

### I. Design First

When a tradeoff arises between design ergonomics and developer
ergonomics, design wins. Development adapts to serve the design
intent, not the other way around.

- Visual and interaction quality MUST NOT be compromised for
  implementation convenience.
- Component APIs MUST prioritize the mental model of the
  designer over the convenience of the developer.
- If a technical constraint forces a design compromise, it MUST
  be documented as a Design Decision Record (DDR) with the
  rejected design-first alternative.

### II. DESIGN.md as Single Source of Truth

DESIGN.md (following the Google Labs `design.md` format) is the
sole canonical source for design tokens. All downstream artifacts
are generated, never hand-edited.

- Tailwind config, CSS custom properties, DTCG token files, and
  Figma styles MUST be generated from DESIGN.md.
- Direct edits to generated artifacts MUST be rejected at review
  or overwritten by the generation pipeline.
- The DESIGN.md format follows the Google Labs specification
  (https://github.com/google-labs-code/design.md or its successor).

### III. Traceability of Design Decisions

Every visual decision present in DESIGN.md or in a component MUST
be traceable to a rationale.

- A Design Decision Record (DDR) in `/decisions` or a prose
  section within DESIGN.md MUST document: context, options
  considered, and rationale for the chosen option.
- Undocumented visual decisions MUST be flagged in review and
  blocked until a DDR is added.
- DDRs are immutable once ratified; superseded DDRs are marked
  deprecated with a pointer to the replacement.

### IV. Git Abstracted for Designers

The designer's happy path MUST NOT include branches, rebases,
or manual conflict resolution.

- The tooling MUST present a linear, simplified interaction
  model for design contributions.
- If a merge conflict arises during a designer-initiated flow,
  the process MUST halt and request developer assistance — it
  MUST NOT attempt automatic resolution.
- Git operations required by the spec-kit workflow (commit,
  branch) MUST be handled transparently by skill hooks.

### V. Canonical Token Direction: Repo to Figma

The repository is the canonical source for design tokens.
Figma is a consumer, not a producer.

- Design tokens flow: DESIGN.md → DTCG JSON → Tokens Studio
  plugin → Figma.
- Token changes MUST originate in the repository (via DESIGN.md
  edits) and propagate to Figma, never the reverse.
- The Tokens Studio plugin configuration MUST point to the
  repo-generated DTCG files as its token source.

### VI. Atomic Skills

Every spec-kit skill performs one discrete operation and returns
a verifiable result. Atomicity is defined by single
responsibility, not by number of internal steps.

- A skill MUST have a single responsibility with a clearly
  defined input and output.
- A skill is atomic when it has one well-defined responsibility,
  one public surface, and a single verifiable success/failure
  outcome — even if it internally orchestrates multiple
  sub-operations (e.g., editing a file, regenerating
  derivatives, creating a DDR, committing).
- A skill that mixes unrelated responsibilities (e.g., editing
  DESIGN.md AND creating a component AND updating the showcase)
  violates this principle.
- Composite workflows MUST be achieved by skills invoking other
  skills, not by monolithic multi-purpose skills.
  Orchestration is itself a valid single responsibility.
- Skills MUST be composable. A single user message that implies
  multiple actions (e.g., "change the accent color and add a
  Tag component") MUST chain skills automatically — Claude
  Code's natural skill-selection handles this when skill
  `description` frontmatter is precise and trigger-oriented.
- Skills MUST end in a clean state (commits applied, working
  tree stable) so subsequent skills start from a known
  baseline.
- Skills MUST NOT share mutable in-memory state between
  invocations — they communicate exclusively via the
  filesystem.

### VII. Self-Contained Template

The template ships complete: from zero to a navigable showcase
using the Sotano demo. Removing the demo to start a custom
system is a single command, not a migration.

- A single eject command MUST cleanly remove all Sotano-specific
  content while preserving the template infrastructure.
- After ejection, the template MUST remain functional with a
  showcase ready for new components.
- No manual file surgery, renaming, or configuration changes
  MUST be required to start a fresh system.

### VIII. Public by Default

Zero references to clients, internal projects, proprietary
stacks, or credentials — in code, documentation, examples, or
screenshots.

- Every file in the repository MUST be safe for public
  consumption at all times.
- Secrets, API keys, and credentials MUST be excluded via
  .gitignore and pre-commit hooks.
- Example content (including the Sotano demo) MUST use only
  fictional or openly licensed material.

### IX. Showcase as Documentation

Components are documented where they render. The Next.js
showcase with inline MDX is the documentation — there are no
external docs.

- Component documentation MUST live as MDX content co-located
  with its showcase rendering.
- There MUST NOT be a separate documentation site, wiki, or
  Storybook instance.
- Usage examples, prop tables, and design rationale MUST be
  visible in the running showcase.
- This principle applies to the published template repository.
  The meta-repository (used by maintainers to build and evolve
  the template) follows conventional documentation practices:
  a CONTRIBUTING.md, a `docs/` directory if needed, and inline
  comments. Spec-kit artifacts (specs, plans, tasks) live in
  the meta-repository and are not documentation but build
  artifacts.

### X. Spec-Driven in Itself

The spec-kit workflow (constitution → specify → clarify →
plan → tasks → analyze → implement) applies to construction
and evolution of the template by maintainers and contributors,
conducted in the meta-repository. End users of the published
template do NOT use spec-kit; they interact with the template's
skills directly via natural language.

- Structural changes to the template (DESIGN.md schema, folder
  structure, skill set) MUST pass through the spec-kit flow in
  the meta-repo before being propagated to the published
  template.
- Structural changes MUST NOT be made ad-hoc, even by
  maintainers.
- This principle is self-referential: amending this
  constitution requires following the spec-kit flow.

## Resolved Decisions

The following tensions between principles were identified in
v1.0.0 as "Points to Clarify" and have been resolved. The
original tension context is preserved to document why each
decision was necessary.

### RD-01: DDRs vs. Showcase-Only Documentation

**Original tension** (Principle III vs. Principle IX):
Principle III requires Design Decision Records in `/decisions`
or as DESIGN.md prose. Principle IX states the showcase is the
only documentation. Are DDRs part of the showcase, or are they
an explicit exception to "no external docs"?

**Options considered**:
- (A) DDRs render as showcase pages under a `/decisions` route.
- (B) DDRs are an explicit exception: developer-facing artifacts
  that live outside the showcase.
- (C) DDR content is embedded inline in the component showcase
  page it governs.

**Decision: Option A — DDRs render as showcase pages.**
DDRs render as showcase pages under a `/decisions` route within
the Next.js showcase. Each component page in the showcase MUST
cross-link to the DDRs that justify its design choices, and
each DDR MUST cross-link to the components it governs. This
preserves Principle IX (showcase as documentation) by keeping
DDRs inside the showcase rather than as external markdown.

### RD-02: Designer Token Change Proposals

**Original tension** (Principle I vs. Principle V):
Principle I says design wins tradeoffs. Principle V says the
canonical direction is repo → Figma. When a designer wants to
propose a token change, what is their workflow?

**Options considered**:
- (A) Designers edit DESIGN.md directly (requires repo access
  and minimal git literacy — tension with Principle IV).
- (B) Designers propose changes via issue or conversation; a
  developer transcribes to DESIGN.md.
- (C) A dedicated skill abstracts the DESIGN.md edit so the
  designer never touches the raw file.

**Decision: Option C — Skill-mediated token editing.**
A dedicated skill abstracts edits to DESIGN.md so designers
never touch the raw file. The skill accepts natural-language
input (e.g., "change the accent red to a more muted brick
tone"), translates it into a DESIGN.md edit, regenerates all
derived artifacts (Tailwind config, CSS variables, DTCG
tokens), creates the corresponding DDR with the rationale
provided by the designer, and commits — all in one invocation.
Direct manual edits to DESIGN.md by a designer remain possible
as a degraded fallback when the skill cannot apply, but the
canonical path is skill-mediated.

### RD-03: Git Abstraction Boundary for Self-Evolution

**Original tension** (Principle IV vs. Principle X):
Principle IV abstracts git for designers. Principle X requires
the full spec-kit flow for structural changes. If a designer
proposes a structural change (e.g., a new component category),
must they navigate the spec-kit flow, or is spec-kit
self-evolution a developer-only process?

**Options considered**:
- (A) Spec-kit self-evolution is developer-initiated; designers
  contribute intent, not specs.
- (B) The spec-kit flow itself abstracts git sufficiently that
  a designer can drive it end-to-end via skills.

**Decision: Reframing — spec-kit applies only to the meta-repo.**
Spec-kit applies only to the construction and evolution of the
template by maintainers and contributors, conducted in the
meta-repository. End users of the
template (designers and devs prototyping their own design
system) do not interact with spec-kit. They use the template's
skills via natural language. The `.specify/` directory is part
of the meta-tooling and is excluded from the published template
repo. This implies a two-repo architecture (see Repository
Topology section).

### RD-04: Showcase Continuity After Demo Ejection

**Original tension** (Principle VII vs. Principle IX):
Principle VII says ejecting Sotano is a single command.
Principle IX says the showcase IS the documentation. After
ejection, does the showcase exist as a usable shell, or does
the user have zero documentation until they build components?

**Options considered**:
- (A) Ejection leaves a scaffold showcase with placeholder
  content and "getting started" instructions.
- (B) Ejection leaves a truly empty showcase — documentation
  begins when the first component is added.

**Decision: Option A — Scaffold with intentional placeholders.**
Ejecting the Sotano demo MUST leave a useful scaffold showcase,
not an empty Next.js shell. The post-eject showcase MUST
include intentional placeholder pages (welcome, getting-started,
an empty components index, an empty decisions index) with copy
that guides the user toward their first change. Additional
constraints:
- Placeholders MUST be easy to replace — they live in discrete,
  obviously-named files (e.g., `welcome.mdx`,
  `getting-started.mdx`).
- Placeholder copy is in MDX (not hardcoded in TSX).
- The showcase remains functional if any placeholder file is
  deleted.
- Eject + scaffold is a single atomic operation.

### RD-05: Design-First vs. Atomic Skills

**Original tension** (Principle I vs. Principle VI):
Principle I says design wins tradeoffs. Principle VI says
skills must be atomic. If optimal designer UX requires a single
skill to orchestrate a complex multi-step process, does
Principle I override Principle VI?

**Options considered**:
- (A) Principle VI is absolute: the orchestration skill is
  itself atomic (its single responsibility is orchestration)
  and it delegates to other atomic skills internally.
- (B) Principle I can override VI in documented cases backed
  by a DDR justifying the exception.

**Decision: Option A — Orchestration is a valid single
responsibility.** Principle VI's atomicity is defined by single
responsibility, not by number of internal steps. A skill is
atomic when it has one well-defined responsibility, one public
surface, and a single verifiable success/failure outcome — even
if it internally orchestrates multiple sub-operations (e.g.,
editing a file, regenerating derivatives, creating a DDR,
committing). A skill that mixes unrelated responsibilities
(e.g., editing DESIGN.md AND creating a component AND updating
the showcase) violates Principle VI. Orchestration is itself a
valid single responsibility. This definition has been
integrated into the refined Principle VI above.

## Repository Topology

This template is built and distributed through two distinct
repositories:

**Meta-repository** (this repo, where the template is
constructed and evolved):
- Contains spec-kit (`.specify/`), the constitution, all
  feature specs, plans, tasks, DDRs about the template
  architecture, source for skills, and the canonical Sotano
  demo.
- Audience: maintainers and contributors.
- Documentation: CONTRIBUTING.md, `docs/`, inline comments.
- Public for transparency, but not the entry point for users.

**Template-repository** (published as a GitHub Template):
- Contains the runtime template: folder structure, DESIGN.md
  (Sotano demo populated), Next.js + MDX showcase, components,
  packaged skills in `.claude/skills/`, end-user README, eject
  script.
- Excludes: `.specify/`, the constitution itself (replaced by
  a user-facing principles doc if needed), meta-DDRs, internal
  feature specs.
- Audience: designers and devs prototyping their own design
  system.
- This is what users clone via "Use this template".

Synchronization from meta to template is a deliberate release
operation, performed manually in v1 and potentially automated
later. Meta is the source of truth; template is the
distribution artifact.

## Development Workflow

The spec-kit workflow governs all non-trivial changes to this
template within the meta-repository. The canonical sequence is:

1. **Constitution** — Verify the change aligns with these
   principles. If a principle must change, start here.
2. **Specify** — Write a feature spec from a natural language
   description.
3. **Clarify** — Identify and resolve underspecified areas
   through targeted questions.
4. **Plan** — Produce an implementation plan with technical
   context and project structure.
5. **Tasks** — Generate dependency-ordered, actionable tasks.
6. **Analyze** — Cross-artifact consistency check.
7. **Implement** — Execute the plan task by task.

Git operations (branch creation, commits) are handled
automatically by extension hooks defined in
`.specify/extensions.yml` and MUST NOT require manual
intervention in the happy path.

## Governance

This constitution is the supreme authority for the design
system template. When a practice, convention, or implementation
conflicts with a principle stated here, this document prevails.

**Amendment procedure**:
- Amendments MUST follow the spec-kit flow (Principle X):
  specify the change, clarify, plan, implement.
- Each amendment MUST include a DDR (Principle III) documenting
  the motivation and alternatives considered.
- Principle ordering reflects priority: in an unresolved
  conflict between two principles, the lower-numbered
  principle takes precedence.

**Versioning**:
- **MAJOR**: Principle removed, redefined, or reordered in a
  way that changes priority semantics.
- **MINOR**: New principle, section, or materially expanded
  guidance added.
- **PATCH**: Wording clarifications, typo fixes, non-semantic
  refinements.

**Compliance**:
- All contributions MUST be verified against these principles
  before merge.
- The `/speckit-analyze` skill SHOULD be run after task
  generation to check cross-artifact consistency.
- Violations MUST be resolved or granted an explicit exception
  via DDR before proceeding.

**Version**: 1.1.0 | **Ratified**: 2026-04-28 | **Last Amended**: 2026-04-28
