<!-- 
SYNC IMPACT REPORT

Version Change: INITIAL → 1.0.0
- This is the first formal constitution for testspeckit
- Focus: Test-Driven Development as core mandate (MANDATORY)
- Added 5 core principles emphasizing quality, independence, and testability
- Formalized governance around TDD, versioning, and amendment procedures

Principles (NEW):
1. Specification-Driven Design – All features begin with user stories and acceptance criteria
2. Test-Driven Development (MANDATORY) – Red-Green-Refactor enforced for all code
3. Independent User Stories – Each story testable, deployable, and valuable standalone
4. Contract Testing – Explicit contracts for all service boundaries
5. Simplicity & YAGNI – No premature complexity, justified when necessary

New Sections:
- Quality Gates & Testing Requirements (formalized test coverage, contract tests)
- Development Workflow (TDD cycle, user story independence, deployment strategy)
- Governance (Constitution supremacy, amendment procedure, compliance gates)

Templates Requiring Updates:
- ✅ spec-template.md – Already emphasizes user scenarios and acceptance scenarios (aligned)
- ✅ plan-template.md – Already includes Constitution Check gate (aligned)
- ✅ tasks-template.md – Already organizes by user story (aligned with independence principle)

Follow-up TODOs: None – all placeholders resolved

-->

# testspeckit Constitution

## Core Principles

### I. Specification-Driven Design

All features MUST begin with a formal specification document (`spec.md`) that captures:

- User stories organized by priority (P1, P2, P3...), each independently testable and valuable
- Functional and non-functional requirements with explicit acceptance criteria
- Edge cases and error scenarios
- Success criteria measurable without implementation details

**Rationale**: Writing specifications before code ensures alignment with user intent, enables
parallel development by story, and provides testability contracts that drive implementation.

### II. Test-Driven Development (MANDATORY)

All code MUST follow the Red-Green-Refactor cycle:

1. **RED**: Write test(s) that fail, capturing expected behavior
2. **GREEN**: Implement minimum code to pass test(s)
3. **REFACTOR**: Improve code structure without changing behavior

Tests MUST be written and must fail before implementation begins. No feature is considered
complete without passing tests. All new code requires new tests. Existing tests must continue
to pass.

**Rationale**: TDD ensures code is testable by design, documents expected behavior, prevents
regressions, and provides immediate feedback during development.

### III. Independent User Stories

Each user story MUST be independently:

- **Testable**: Runnable on its own without other stories
- **Implementable**: Written and merged without blocking other stories  
- **Deployable**: Can be released individually without breaking production
- **Valuable**: Delivers measurable user benefit standalone

No user story may depend on another story for core functionality. Shared infrastructure
(Phase 2 Foundational work) is required for all stories but is not a story dependency.

**Rationale**: Independent stories enable parallel development, reduce risk through incremental
delivery, and allow early user feedback on high-priority features.

### IV. Contract Testing

All service boundaries (API endpoints, database queries, inter-service calls) MUST have
explicit contract tests:

- **Input contracts**: Define valid request structures and constraints
- **Output contracts**: Define response structure and guarantees
- **Error contracts**: Define error codes and recovery behavior

Contract tests are written alongside or before implementation. They serve as the source of
truth for integrations across system components.

**Rationale**: Contracts prevent breaking changes, enable parallel development of client and
server, and provide clear integration documentation.

### V. Simplicity & YAGNI

All implementations MUST favor simplicity. Avoid speculative features, unnecessary abstraction,
and over-engineering.

Add complexity ONLY when:

- Required by a user story in the current scope
- Justified by measurable constraints or performance requirements
- Approved during architecture review (documented in plan.md)

Code review and design documents explicitly call out complexity. Over-engineering is a blocker.

**Rationale**: Simple code is faster to write, easier to test, and cheaper to maintain.
Premature complexity is the enemy of velocity and quality.

## Quality Gates & Testing Requirements

### Mandatory Testing Discipline

**Unit Tests**: Core business logic, algorithms, transformations must be unit tested with >80%
coverage in critical paths.

**Contract Tests**: All service boundaries (defined in principle IV) require passing contract
tests. Contract tests must run in CI/CD pipeline before merge.

**Integration Tests**: User stories involving multiple components or external integrations must
have integration tests verifying the full workflow matches acceptance scenarios.

**Test Organization**: Tests mirror source structure—`tests/contract/`, `tests/integration/`,
`tests/unit/` as appropriate to project type.

### Pre-Merge Gates

- All tests pass (unit + contract + integration as applicable)
- Code coverage reports generated (target: >80% in critical paths)
- No test regressions from main branch
- Constitution Check passes (see Development Workflow)

## Development Workflow

### Feature Lifecycle

**Phase 0: Specification**

1. Write `spec.md` capturing user stories (prioritized P1-P3+), functional requirements, and success criteria
2. Submit for review before implementation
3. User stories must have independent test scenarios

**Phase 1: Research & Design** (as needed)

1. Technical research documented in `research.md` if design is non-obvious
2. Data model documented in `data-model.md` if entities are needed
3. Service contracts defined in `contracts/` (one file per endpoint/boundary)

**Phase 2: Implementation (TDD Cycle)**

For each user story (starting with P1):

1. Write failing tests matching spec acceptance scenarios
2. Implement minimum code to pass tests
3. Refactor for clarity
4. Integration test the story end-to-end
5. Merge when all story tests pass

User stories can proceed in parallel after foundational (Phase 2) infrastructure is complete.

**Phase 3: Deployment**

1. Test independently (verify user story works without other stories)
2. Deploy (or prepare for production release)
3. Monitor for regressions

### Constitution Check Gate

Every plan.md MUST include a "Constitution Check" section verifying:

- Specification exists with user stories (principle I)
- Testing strategy defined and TDD approach documented (principle II)
- User stories are independent or justified as dependent (principle III)
- Contract tests identified for service boundaries (principle IV)
- Complexity justified if exceeding baseline architecture (principle V)

Violations require documented exception and approval before Phase 1 research begins.

## Governance

### Constitution as Authority

This constitution supersedes all other practices, conventions, and prior decisions. When a
conflict arises between this document and any other guidance, the constitution takes
precedence.

### Amendment Procedure

Constitution amendments require:

1. **Proposal**: Document the amendment with rationale (new principle, governance change, etc.)
2. **Review**: Justify why existing principles are insufficient or need revision
3. **Approval**: Record approval from project lead/stakeholder (document in amendment comment)
4. **Implementation**: Update this file, all dependent templates, and related guidance
5. **Tracking**: Version bump and date in footer (see Versioning Policy below)

### Versioning Policy

Constitution version follows semantic versioning:

- **MAJOR**: Backward-incompatible changes (principle removal, fundamental redefinition)
- **MINOR**: New principles added or existing principles significantly expanded
- **PATCH**: Clarifications, wording improvements, non-semantic refinements

Each amendment must justify the version bump rationale.

### Compliance Review

- Code reviews verify adherence to TDD (principle II) and testing gates
- Architecture reviews verify adherence to independence (principle III) and simplicity (principle V)
- Specification reviews verify adherence to user story prioritization (principle I)
- Integration tests verify contract compliance (principle IV)

---

**Version**: 1.0.0 | **Ratified**: 2025-12-21 | **Last Amended**: 2025-12-21
