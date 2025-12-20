<!--
Sync Impact Report:
- Version change: none → 1.0.0 (initial constitution)
- Added principles: I. Test-Driven Development, II. Specification-First Development, III. CLI-Focused Interface, IV. Documentation & Transparency, V. Simplicity & Maintainability
- Added sections: Development Workflow, Quality Assurance, Governance
- Templates requiring updates: ✅ plan-template.md (Constitution Check section), ✅ spec-template.md (already aligned), ✅ tasks-template.md (already aligned with TDD workflow)
- Follow-up TODOs: none
-->

# Speckit Constitution
<!-- Example: Spec Constitution, TaskFlow Constitution, etc. -->

## Core Principles

### I. Test-Driven Development (NON-NEGOTIABLE)
<!-- Example: I. Library-First -->
TDD is mandatory for all feature development: Tests MUST be written first, written to fail, then implementation proceeds. The Red-Green-Refactor cycle MUST be strictly enforced for all code changes. This ensures specifications are testable, requirements are clear, and quality is built in from the start. Without tests, features cannot be considered complete or ready for integration.
<!-- Example: Every feature starts as a standalone library; Libraries must be self-contained, independently testable, documented; Clear purpose required - no organizational-only libraries -->

### II. Specification-First Development
<!-- Example: II. CLI Interface -->
All development MUST begin with clear, testable specifications. Features require user stories, acceptance criteria, and success metrics before implementation starts. Specifications drive all design decisions and serve as the source of truth for requirements. This ensures alignment between user needs and delivered functionality and provides measurable outcomes.
<!-- Example: Every library exposes functionality via CLI; Text in/out protocol: stdin/args → stdout, errors → stderr; Support JSON + human-readable formats -->

### III. CLI-Focused Interface
<!-- Example: III. Test-First (NON-NEGOTIABLE) -->
Speckit MUST provide a consistent, powerful CLI interface for all operations. Commands follow Unix philosophy: do one thing well, accept text input/output, handle errors gracefully via stderr. All functionality MUST be accessible via command-line tools with clear documentation and examples. This ensures accessibility, automation compatibility, and developer productivity.
<!-- Example: TDD mandatory: Tests written → User approved → Tests fail → Then implement; Red-Green-Refactor cycle strictly enforced -->

### IV. Documentation & Transparency
<!-- Example: IV. Integration Testing -->
All features, APIs, and workflows MUST be thoroughly documented. Code comments explain why, not just what. User-facing documentation includes examples, use cases, and troubleshooting guides. Decision records explain architectural choices. Transparency in development process, specifications, and implementation choices builds trust and enables collaboration.
<!-- Example: Focus areas requiring integration tests: New library contract tests, Contract changes, Inter-service communication, Shared schemas -->

### V. Simplicity & Maintainability
<!-- Example: V. Observability, VI. Versioning & Breaking Changes, VII. Simplicity -->
Prefer simple solutions over complex ones. YAGNI (You Aren't Gonna Need It) principle applies - implement only what's needed for current requirements. Code should be easy to understand, test, and modify. Technical debt MUST be actively managed and minimized. Start simple, iterate based on real needs rather than anticipated ones.
<!-- Example: Text I/O ensures debuggability; Structured logging required; Or: MAJOR.MINOR.BUILD format; Or: Start simple, YAGNI principles -->

## Development Workflow
<!-- Example: Additional Constraints, Security Requirements, Performance Standards, etc. -->

All development follows the TDD cycle: Write failing test → Write minimal code to pass → Refactor while keeping tests green. Feature development proceeds through specification → tests → implementation → documentation phases. Each user story must be independently implementable and testable. Code reviews must verify test coverage and specification compliance.
<!-- Example: Technology stack requirements, compliance standards, deployment policies, etc. -->

## Quality Assurance
<!-- Example: Development Workflow, Review Process, Quality Gates, etc. -->

Quality gates include: 100% test coverage for new features, all tests passing, documentation complete, specification requirements met. Integration tests verify component interactions. Contract tests ensure API compatibility. Performance tests validate non-functional requirements. No feature merges without meeting all quality standards.
<!-- Example: Code review requirements, testing gates, deployment approval process, etc. -->

## Governance
<!-- Example: Constitution supersedes all other practices; Amendments require documentation, approval, migration plan -->

Constitution amendments require: (1) documented rationale for change, (2) impact assessment on existing workflows, (3) community review period, (4) version bump following semantic versioning. Major changes (backward incompatible) require consensus. All team members must understand and follow constitution principles. Violations must be addressed promptly with corrective action.
<!-- Example: All PRs/reviews must verify compliance; Complexity must be justified; Use [GUIDANCE_FILE] for runtime development guidance -->

**Version**: 1.0.0 | **Ratified**: 2025-12-21 | **Last Amended**: 2025-12-21
<!-- Example: Version: 2.1.1 | Ratified: 2025-06-13 | Last Amended: 2025-07-16 -->