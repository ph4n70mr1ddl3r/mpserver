---
stepsCompleted: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
inputDocuments:
  - docs/analysis/product-brief-mpserver-2025-12-09.md
documentCounts:
  briefs: 1
  research: 0
  brainstorming: 0
  projectDocs: 0
workflowType: 'prd'
lastStep: 11
project_name: 'mpserver'
user_name: 'Riddler'
date: '2025-12-09'
---

# Product Requirements Document - mpserver

**Author:** Riddler
**Date:** 2025-12-09

## Executive Summary

mpserver delivers heads-up NLHE play-money cash tables with stable pacing, clean timeouts/disconnect handling, and a lobby for tables/online players. It launches as a traditional-feel online poker experience, then upgrades to mental poker for provable fairness so neither operators nor opponents can bias shuffles or see cards. Accounts are username/password with free balance reloads; no rake initially.

### What Makes This Special

Trust-first roadmap to provable fairness: mental poker protocols, per-hand cryptographic proofs, and open-source clients so players can verify shuffles and card secrecy. Positioned for users who distrust current sites’ “trust us” claims.

## Project Classification

**Technical Type:** game (online heads-up poker; real-time web/app + backend)  
**Domain:** gaming/gambling (fairness/security sensitive)  
**Complexity:** high (real-time play, security/fairness requirements)  
**Project Context:** Greenfield - new project

Real-time heads-up poker play-money platform with fairness/security as the differentiator; future phase introduces mental poker cryptography and open-source clients; no rake or payments in MVP; admin-managed tables and visible lobby.

## Success Criteria

### User Success
- Players complete heads-up hands with stable pacing; minimal disruptions from timeouts/disconnects.
- Experience feels as responsive as mainstream online poker, even as crypto/fairness features are added.
- Clear fairness cues (early demos/proofs roadmap) that build trust before mental poker ships.

### Business Success
- Play-money traction: daily active players and daily hands played sustain and grow.
- Post-monetization readiness: ability to track average rake per day and per hand once enabled.

### Technical Success
- Reliability: high % of hands completed without disconnect/timeout failures; graceful recovery when they occur.
- Performance: action latency comparable to mainstream online poker; supports multiple tables without degradation.
- Security/fairness posture: resists malicious clients; instrumented to add mental poker/crypto proofs later without rewrites.

### Measurable Outcomes
- Play-money DAU and hands/day targets (to be set): e.g., baseline to prove stable liquidity.
- Reliability target: % hands completed without failure/timeouts (set target).
- Latency target: max action latency comparable to current online poker (set target).
- Security/fairness: ability to detect/resist malicious clients; instrumentation for proof hooks.

## Product Scope

### MVP - Minimum Viable Product
- Heads-up NLHE play-money cash tables with stable pacing; clean timeout/disconnect handling.
- Admin can open/close tables; lobby lists tables and online players.
- Accounts with username/password; players can reload balance to a max value anytime; no rake initially.
- No mental poker or open-source release in MVP.

### Growth Features (Post-MVP)
- Mental poker cryptography with per-hand proofs; open-source clients.
- Rake enablement and reporting.
- Additional table formats or small extensions (e.g., >2 players if ever planned).

### Vision (Future)
- Full mental poker deployment with visible proofs and open-source clients as default.
- Real-money support with rake; broader ecosystem once fairness is provably established.

## User Journeys

**Journey 1: Fairness-Focused Grinder – Proving It’s Legit**
A grinder who’s convinced mainstream sites can bias shuffles signs up with a username/password, reloads free balance, and heads straight to a heads-up table. They test pacing (actions feel instant), watch timeouts resolve cleanly, and play a string of hands. They look for fairness cues (roadmap to proofs, visible integrity messaging) and see no rake. After multi-tabling a bit, they leave with confidence the platform feels like normal poker and is positioned to deliver provable fairness next.

**Journey 2: Trust-Curious Casual – Quick Session, Safe Recovery**
A casual player sees the lobby (tables + players online), creates an account in minutes, reloads balance, and sits at a heads-up table. Mid-hand they hit a timeout/disconnect; the session recovers gracefully without losing their place or chips. They finish a short session, reload easily, and feel the UX matches other poker sites even without mental poker live yet.

**Journey 3: Admin/Ops – Smooth Control and Monitoring**
An admin opens a set of heads-up tables, verifies they appear in the lobby, and monitors players online. They close an underused table and handle a small wave of disconnects gracefully. They watch for suspicious client behavior, relying on stability and guardrails to keep games fair and running.

**Journey 4: Security/Anti-Abuse – Blocking Malicious Clients**
A would-be malicious client tries to exploit protocol edges (timeouts, reconnects, message timing). The system detects anomalies, maintains game state integrity, prevents advantage, and keeps honest players’ experience stable.

### Journey Requirements Summary
- Signup/account: username/password, fast path to first sit; free reload flow.
- Lobby/table mgmt: list tables/players online; admin open/close tables.
- Table experience: heads-up NLHE, stable pacing, clean timeouts/disconnect handling, multi-table readiness.
- Trust cues: upfront fairness positioning/roadmap; later hooks for proofs/mental poker without rewrites.
- Ops/monitoring: visibility into sessions and anomalies; guardrails against malicious clients.

## Domain-Specific Requirements

### Gaming/Gambling Fairness (Future Real-Money Readiness)
- Fairness verification/certification: defer until real-money; plan to add formal audits/RNG or cryptographic proofs later.
- Responsible gaming: defer (no session limits/self-exclusion in MVP).
- Anti-abuse (collusion/bots/malicious clients): defer advanced detection; MVP focuses on stability and guardrails.
- Data/audit/disputes: defer hand-history retention and audit exports until monetization is in scope.

### Implementation Considerations
- MVP stays play-money with no KYC/AML.
- Design with hooks for future fairness proofs, audits, anti-abuse, and responsible gaming controls when real money is added.

## Innovation & Novel Patterns

### Detected Innovation Areas
- Provable fairness roadmap: mental poker protocols so neither server nor opponents can see hidden cards; per-hand cryptographic proofs.
- Transparency by design: open-source clients plus visible proofs to replace “trust us” fairness claims.
- Fairness-first positioning: heads-up play-money launch that’s instrumented for future cryptographic verification without rewrites.

### Market Context & Competitive Landscape
- Mainstream poker sites rely on reputational trust and private audits; none provide user-verifiable cryptographic proofs or operator-blind shuffles.
- Differentiator: operator-blind, user-verifiable play with a clear upgrade path from traditional to mental poker.

### Validation Approach
- Phase 1 (play-money): instrument for future proof hooks; measure stability/latency and fairness perception.
- Phase 2 (pre-monetization): introduce pilot cryptographic proofs per hand; open-source client; gather verification usage and external audits.
- Phase 3 (real-money): formal audits/certifications; user-facing proof workflows; attack/abuse testing.

### Risk Mitigation
- User comprehension: provide simple proof viewers and education to prevent confusion.
- Performance: budget crypto overhead; keep latency targets on par with mainstream poker.
- Security: harden against malicious clients and proof spoofing; ensure graceful degradation if proof services fail.
- Compliance (future): align proof/audit artifacts with regulatory expectations once monetization starts.

## Game-Specific Requirements

### Project-Type Overview
- Heads-up online poker; web client; C++/Linux server.
- Real-time over WebSockets with JSON payloads.

### Technical Architecture Considerations
- Transport: WebSockets + JSON for lobby/table state and actions.
- Server: C++ on Linux; keep latency targets comparable to mainstream online poker.

### Lobby & Table Management
- Admin-configured available tables; lobby lists tables (observers can see lobby but not table play).
- MVP: single table active at a time (per deployment/instance).
- No rake in MVP; play-money balances with free reloads.

### Gameplay / Turn Handling
- Turn timer: 10 seconds per action.
- Disconnect handling: 30 seconds to reconnect.
- Sit-out removal: remove after 1 minute sitout.

### Reconnect & Session Rules
- Preserve seat/state across a 30s disconnect; auto-remove after sitout limit.
- Observers: lobby visibility only; no table observation in MVP.

### Data & Logging
- Hand history/logging: do not store; allow server console printing for ops/debug.

### Client Integrity / Anti-Cheat
- Advanced anti-cheat/anti-bot deferred; basic stability/guardrails only in MVP.

## Project Scoping & Phased Development

### MVP Strategy & Philosophy
- MVP Approach: Experience MVP (deliver stable heads-up play-money tables that feel like mainstream poker, with fairness positioning and future-proofing for mental poker).
- Resource Requirements: Lean team covering C++/Linux backend, web client, real-time/WebSocket expertise, and ops/monitoring.

### MVP Feature Set (Phase 1)
Core User Journeys Supported:
- Fairness-focused grinder: fast, stable heads-up play; clean timeouts/reconnect; lobby visibility.
- Trust-curious casual: quick signup, free reload, recover from disconnect.
- Admin/ops: open/close a single table, monitor lobby/players; basic guardrails.

Must-Have Capabilities:
- Web client; C++/Linux server; WebSockets + JSON.
- Admin-configured lobby and single active table (MVP).
- Heads-up NLHE play-money; no rake; free balance reloads.
- Timing: 10s turn; 30s reconnect; 1-minute sitout removal.
- Stable pacing; clean timeout/disconnect handling; observers see lobby only.
- Console-only hand output; no stored histories/logs.
- Basic stability/guardrails; advanced anti-cheat deferred.

### Post-MVP Features
Phase 2 (Post-MVP):
- Multi-table support; richer lobby/table filters.
- Initial cryptographic proofs pilot and open-source client.
- Basic anti-abuse signals; optional hand history retention/viewing.
- Table observation (if desired) and replay/logging.

Phase 3 (Expansion):
- Full mental poker deployment with per-hand proofs; operator-blind shuffles.
- Real-money/rake, compliance, and responsible gaming controls.
- Advanced anti-collusion/anti-bot detection; audit exports.
- Broader table formats and scaling.

### Risk Mitigation Strategy
- Technical Risks: Real-time stability and crypto overhead—target mainstream poker latency; start with single-table to simplify. Defer heavy proofs but design hooks now.
- Market Risks: Trust perception—use clear fairness messaging/roadmap; gather feedback from grinders/casuals in play-money.
- Resource Risks: If team is smaller, keep single-table MVP, minimal logging, and defer observers/table variants; add proofs/anti-abuse only after stability is proven.

## Functional Requirements

### Accounts & Balances
- FR1: Player can create an account with username/password.
- FR2: Player can sign in and remain authenticated across a session.
- FR3: Player can reload play-money balance up to a max value at any time.
- FR4: Player can view current balance from lobby and at table.

### Lobby & Table Access
- FR5: Player can view available tables and players online in the lobby.
- FR6: Player can join a heads-up table from the lobby (MVP: only one active table per deployment).
- FR7: Player can leave a table and return to the lobby without losing account state.
- FR8: Observer can view lobby listings but cannot observe table play.

### Gameplay & Session Control
- FR9: Player can take a seat at a heads-up NLHE table and play hands with standard rules.
- FR10: Player can act within a 10-second turn timer.
- FR11: Player action is auto-resolved (e.g., fold/check) when the turn timer expires.
- FR12: Player can reconnect within 30 seconds and resume their seat/state.
- FR13: System removes a seated player after 1 minute of sit-out.

### Timing, Reconnects, and Continuity
- FR14: System preserves hand state during a disconnect within the 30-second window.
- FR15: System enforces sit-out removal after 1 minute and returns the seat to availability.

### Admin Operations
- FR16: Admin can configure which tables are available in the lobby.
- FR17: Admin can open/close the single active table for the MVP deployment.
- FR18: Admin can view who is online and seated.

### Trust Positioning & Roadmap Hooks
- FR19: Player can see fairness positioning/roadmap messaging in-product (no proofs in MVP).
- FR20: System can surface that games are play-money and rake-free.

### Data & Logging
- FR21: System can print hand flow/output to server console for ops/debug (no stored hand histories in MVP).

### Security & Guardrails (MVP baseline)
- FR22: System can reject or ignore malformed/unsupported client messages to protect stability.
- FR23: System can detect a reconnect within the allowed window and restore the player session; otherwise, seat is released.

### Future-Proofing Hooks
- FR24: System can be configured to add additional tables post-MVP without changing core player flows.
- FR25: System can be instrumented later for cryptographic proof hooks and open-source client distribution without rewriting player flows.

## Non-Functional Requirements

### Performance
- Real-time actions (bets/folds/checks) complete within a latency envelope comparable to mainstream online poker for heads-up play-money.
- Turn processing respects the 10s timer without introducing extra delays; reconnect resumes state within the 30s window.

### Reliability
- Hands complete without interruption except for enforced timeouts; disconnect/reconnect flows preserve state within the 30s window.
- System can recover gracefully from a client disconnect without corrupting hand state.

### Security (MVP Baseline)
- WebSocket messages are validated; malformed/unsupported messages are rejected or ignored without destabilizing the table.
- Sessions maintain authentication across reconnect within the allowed window.

### Scalability (MVP scope)
- Supports the MVP shape: single active heads-up table, concurrent lobby users, with headroom for adding tables post-MVP without changing player flows.

### Observability & Logging
- Server can emit hand flow/output to console for ops/debug; no persisted hand histories in MVP.

### Future-Proofing Hooks
- Architecture can add cryptographic proof instrumentation later without rewriting player flows.
- System can add more tables post-MVP without breaking existing user flows.
## Project Scoping & Phased Development

### MVP Strategy & Philosophy
- MVP Approach: Experience MVP (deliver stable heads-up play-money tables that feel like mainstream poker, with fairness positioning and future-proofing for mental poker).
- Resource Requirements: Lean team covering C++/Linux backend, web client, real-time/WebSocket expertise, and ops/monitoring.

### MVP Feature Set (Phase 1)
Core User Journeys Supported:
- Fairness-focused grinder: fast, stable heads-up play; clean timeouts/reconnect; lobby visibility.
- Trust-curious casual: quick signup, free reload, recover from disconnect.
- Admin/ops: open/close a single table, monitor lobby/players; basic guardrails.

Must-Have Capabilities:
- Web client; C++/Linux server; WebSockets + JSON.
- Admin-configured lobby and single active table (MVP).
- Heads-up NLHE play-money; no rake; free balance reloads.
- Timing: 10s turn; 30s reconnect; 1-minute sitout removal.
- Stable pacing; clean timeout/disconnect handling; observers see lobby only.
- Console-only hand output; no stored histories/logs.
- Basic stability/guardrails; advanced anti-cheat deferred.

### Post-MVP Features
Phase 2 (Post-MVP):
- Multi-table support; richer lobby/table filters.
- Initial cryptographic proofs pilot and open-source client.
- Basic anti-abuse signals; optional hand history retention/viewing.
- Table observation (if desired) and replay/logging.

Phase 3 (Expansion):
- Full mental poker deployment with per-hand proofs; operator-blind shuffles.
- Real-money/rake, compliance, and responsible gaming controls.
- Advanced anti-collusion/anti-bot detection; audit exports.
- Broader table formats and scaling.

### Risk Mitigation Strategy
- Technical Risks: Real-time stability and crypto overhead—target mainstream poker latency; start with single-table to simplify. Defer heavy proofs but design hooks now.
- Market Risks: Trust perception—use clear fairness messaging/roadmap; gather feedback from grinders/casuals in play-money.
- Resource Risks: If team is smaller, keep single-table MVP, minimal logging, and defer observers/table variants; add proofs/anti-abuse only after stability is proven.
