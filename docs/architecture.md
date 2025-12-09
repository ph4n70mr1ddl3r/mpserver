---
stepsCompleted: [1, 2, 3, 4, 5]
inputDocuments:
  - docs/prd.md
  - docs/analysis/product-brief-mpserver-2025-12-09.md
workflowType: 'architecture'
lastStep: 5
project_name: 'mpserver'
user_name: 'Riddler'
date: '2025-12-09T20:24:00+08:00'
lastUpdated: '2025-12-09T20:50:00+08:00'
enhancements: 'Added database schema and WebSocket protocol specification'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

## Project Context Analysis

### Requirements Overview

**Functional Requirements:**
- 25 FRs centered on heads-up NLHE play-money tables: auth/balances (FR1–FR4), lobby/table access (FR5–FR8), gameplay/timing/reconnect (FR9–FR15), admin ops (FR16–FR18), trust positioning (FR19–FR20), logging to console only (FR21), guardrails/reconnect rules (FR22–FR23), and future-proofing hooks for more tables and crypto proofs (FR24–FR25).
- Architectural implications: real-time session/state management over WebSockets; strict timers (10s turn, 30s reconnect, 1m sitout); resilient reconnect/seat preservation; admin-configured single-table MVP; clear separation of auth/balance vs gameplay; fairness messaging surfaced in-product; console-only hand output with no persistence; hooks for future proofs/open-source clients.

**Non-Functional Requirements:**
- Performance: low-latency action handling comparable to mainstream poker; timers must be authoritative.
- Reliability: hand state survives disconnects within 30s; graceful timeout handling.
- Security (MVP baseline): validate/reject malformed WebSocket messages; maintain auth across reconnect windows.
- Scalability: MVP scope is single active table, but design to add tables later without rewrites.
- Observability: console emissions only (no stored hand histories).
- Future-proofing: architecture should admit cryptographic proof instrumentation and multi-table expansion without breaking flows.

**Scale & Complexity:**
- Complexity level: High (real-time state, strict timing, reconnect guarantees, security/fairness positioning).
- Primary domain: Web real-time game backend + web client over WebSockets/JSON on C++/Linux server.
- Estimated architectural components: ~7–9 (WebSocket gateway/session handler, auth/account/balance service, lobby/admin control, game/table engine with timer enforcement, reconnect/session restoration, fairness messaging/UX surfaces, observability/logging adapter, future proof/crypto hook layer, client UI).

### Technical Constraints & Dependencies
- Transport: WebSockets + JSON.
- Server platform: C++ on Linux.
- MVP: single active heads-up table per deployment; play-money only; no mental poker yet; no rake; console-only hand output (no persistence).
- Timing rules baked into engine (10s action, 30s reconnect, 1m sitout).
- Observers limited to lobby only (no table observation).
- Hooks needed for future cryptographic proofs and open-source client release without rewriting flows.

### Cross-Cutting Concerns Identified
- Real-time state integrity under disconnect/reconnect and timer expirations.
- Session/auth continuity across reconnect windows.
- Fairness positioning and future cryptographic-proof hooks baked into protocols/messages.
- Validation/guardrails on WebSocket messages to resist malformed or malicious clients.
- Separation of admin control vs player flows; single-table constraint now with path to multi-table later.
- Latency discipline in game engine and client updates; deterministic timer enforcement.
- Telemetry/logging constrained to console; need clean, parseable logs for ops.

## Starter Template Evaluation

### Primary Technology Domain
Full-stack with C++ backend (serving static assets and WebSocket/API) + TS/React frontend (canvas/WebAssembly).

### Starter Options Considered
- Vite React + TS (create-vite@8.2.0) — modern bundler, first-class ESM, wasm support, fast HMR; easy to emit static assets your C++ server can host.
- Next.js — powerful but less aligned with C++ serving static assets; server-side rendering not needed for this canvas-heavy client.
- CRA — deprecated/lagging; not recommended.

### Selected Starter: Vite React + TS (create-vite@8.2.0)
**Rationale for Selection:**
- Ships static bundle your C++ server can serve; no Node server required in prod.
- Native wasm import support for poker engine or helpers.
- Fast dev loop with HMR; straightforward to add Vitest + Testing Library for coverage.
- Minimal runtime overhead; good fit for canvas rendering.

**Initialization Command:**
```bash
npm create vite@8.2.0 mpclient -- --template react-ts
npm install
npm install -D vitest @testing-library/react @testing-library/user-event @testing-library/dom jsdom
```

**Architectural Decisions Provided by Starter:**
- **Language & Runtime:** TypeScript + React 19; ESM; JSX with TS.
- **Styling Solution:** Unopinionated; can add CSS modules or Tailwind if desired. Canvas rendering for table UI stays library-free.
- **Build Tooling:** Vite 7 (esbuild dev + Rollup prod); wasm imports supported out of the box; code-splitting, asset hashing.
- **Testing Framework:** Vitest + Testing Library (added above) for component/unit/integration coverage in jsdom; supports coverage collection.
- **Code Organization:** src/ with main.tsx entry, App.tsx root; straightforward to add feature folders (game, lobby, auth), hooks, and WebSocket client.
- **Development Experience:** Dev server with HMR; TypeScript tooling; env var support (.env.*); fast builds for canvas/wasm iterations.

**Note:** Initialize client with the above command; serve the built `dist/` via the C++ server (e.g., lightweight HTTP + WebSocket stack on Linux).

## Core Architectural Decisions

### Decision Priority Analysis
**Critical Decisions (Block Implementation):**
- Single C++ server hosts static client and exposes JSON/WS API; no separate Node runtime.
- SQLite single-file DB for accounts/sessions/tables/balances; SQL migrations executed by the C++ app at startup.
- WebSockets + JSON for gameplay; minimal HTTP endpoints for auth/register/reload/lobby.
- Auth: username/password with strong hashing (bcrypt/argon2) + signed HTTP-only session cookies stored in SQLite.
- Canvas-based table UI; React shell for auth/lobby/controls; WebSocket-driven game state.

**Important Decisions (Shape Architecture):**
- Schema: users, sessions, tables, seats, balances, hands_log (console emission only).
- Validation: strict JSON schema checks for HTTP/WS payloads; reject/close on malformed messages.
- Routing: React Router for lobby/auth views; custom WS reducer/store for table state.
- State: React Query (HTTP) + custom reducer/Zustand for WS game state.
- TLS termination via simple reverse proxy (nginx/traefik) in front of C++ app (optional for local dev).
- Logging: structured console logs; no persistent hand histories.

**Deferred Decisions (Post-MVP):**
- Multi-table scaling and connection fan-out.
- Cryptographic proofs/mental poker instrumentation.
- External monitoring/metrics stack and rate limiting.
- CI/CD hardening beyond a minimal build/test workflow.

### Data Architecture
- DB: SQLite (single file on disk); WAL mode for concurrency; migrations via SQL files run by C++ at startup.
- Caching: none initially; rely on in-memory structures for active tables.
- Modeling: normalized tables for users/sessions/tables/seats/balances; append-only hands_log for ops (console).

#### Database Schema

**Schema Design Principles:**
- All IDs are TEXT (UUIDs or generated unique strings)
- Timestamps stored as ISO-8601 UTC strings
- Foreign keys enforced for referential integrity
- Indexes on frequently queried columns
- WAL mode enabled for concurrent reads/writes

**Core Tables:**

```sql
-- Users: Account credentials and profile
CREATE TABLE users (
  id TEXT PRIMARY KEY,
  username TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,  -- bcrypt/argon2 hash with salt
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  CONSTRAINT username_length CHECK (length(username) >= 3 AND length(username) <= 20)
);

CREATE INDEX idx_users_username ON users(username);

-- Sessions: Authentication sessions with HTTP-only cookies
CREATE TABLE sessions (
  id TEXT PRIMARY KEY,  -- Session token (signed cookie value)
  user_id TEXT NOT NULL,
  expires_at TEXT NOT NULL,
  created_at TEXT NOT NULL,
  last_accessed_at TEXT NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);

CREATE INDEX idx_sessions_user_id ON sessions(user_id);
CREATE INDEX idx_sessions_expires_at ON sessions(expires_at);

-- Tables: Poker tables configuration
CREATE TABLE tables (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  table_type TEXT NOT NULL DEFAULT 'heads_up_nlhe',  -- MVP: always heads_up_nlhe
  small_blind INTEGER NOT NULL,
  big_blind INTEGER NOT NULL,
  max_players INTEGER NOT NULL DEFAULT 2,  -- MVP: always 2
  status TEXT NOT NULL DEFAULT 'closed',  -- 'open', 'closed', 'in_play'
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  CONSTRAINT valid_status CHECK (status IN ('open', 'closed', 'in_play'))
);

CREATE INDEX idx_tables_status ON tables(status);

-- Seats: Player positions at tables
CREATE TABLE seats (
  id TEXT PRIMARY KEY,
  table_id TEXT NOT NULL,
  seat_index INTEGER NOT NULL,  -- 0 or 1 for heads-up
  user_id TEXT,  -- NULL if seat is empty
  stack INTEGER,  -- Current chip stack at table (NULL if empty)
  status TEXT NOT NULL DEFAULT 'available',  -- 'available', 'occupied', 'reserved'
  sat_at TEXT,  -- Timestamp when player sat down
  last_action_at TEXT,  -- Last action timestamp (for timeout tracking)
  created_at TEXT NOT NULL,
  updated_at TEXT NOT NULL,
  FOREIGN KEY (table_id) REFERENCES tables(id) ON DELETE CASCADE,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE SET NULL,
  CONSTRAINT unique_seat_per_table UNIQUE (table_id, seat_index),
  CONSTRAINT valid_seat_index CHECK (seat_index >= 0 AND seat_index < 2),
  CONSTRAINT valid_seat_status CHECK (status IN ('available', 'occupied', 'reserved'))
);

CREATE INDEX idx_seats_table_id ON seats(table_id);
CREATE INDEX idx_seats_user_id ON seats(user_id);
CREATE INDEX idx_seats_status ON seats(status);

-- Balances: Player play-money balances
CREATE TABLE balances (
  id TEXT PRIMARY KEY,
  user_id TEXT UNIQUE NOT NULL,
  balance INTEGER NOT NULL DEFAULT 0,  -- Play-money chips
  max_balance INTEGER NOT NULL DEFAULT 10000,  -- Max reload limit
  updated_at TEXT NOT NULL,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
  CONSTRAINT non_negative_balance CHECK (balance >= 0),
  CONSTRAINT balance_within_max CHECK (balance <= max_balance)
);

CREATE INDEX idx_balances_user_id ON balances(user_id);

-- Hands Log: Console-only hand history (not for persistence, just for debugging)
CREATE TABLE hands_log (
  id TEXT PRIMARY KEY,
  table_id TEXT NOT NULL,
  hand_number INTEGER NOT NULL,
  hand_state TEXT NOT NULL,  -- JSON blob of hand state for console output
  logged_at TEXT NOT NULL,
  FOREIGN KEY (table_id) REFERENCES tables(id) ON DELETE CASCADE
);

CREATE INDEX idx_hands_log_table_id ON hands_log(table_id);
CREATE INDEX idx_hands_log_logged_at ON hands_log(logged_at);
```

**Migration Strategy:**
- Migrations stored as numbered SQL files: `001_initial_schema.sql`, `002_add_column.sql`, etc.
- C++ server executes pending migrations on startup in order
- Migration tracking table records applied migrations:

```sql
CREATE TABLE schema_migrations (
  version INTEGER PRIMARY KEY,
  applied_at TEXT NOT NULL
);
```

### Authentication & Security
- Credentials: username/password hashed (bcrypt/argon2) with per-user salt.
- Sessions: signed HTTP-only cookies; server-side session table in SQLite.
- Transport: HTTPS via reverse proxy in prod; plain HTTP allowed in dev.
- Message validation: strict schema validation for HTTP/WS; close WS on invalid payloads.

### API & Communication Patterns
- HTTP (minimal): register, login, logout, reload balance, lobby list.
- WebSocket: table join/leave, actions, timers, reconnect; server enforces turn (10s), reconnect (30s), sit-out (1m).
- Errors: structured codes for HTTP; WS close codes + error payloads.

#### WebSocket Protocol Specification

**Connection Flow:**
1. Client establishes WebSocket connection to `/ws` endpoint
2. Server validates session cookie during upgrade
3. Connection binds to authenticated user_id
4. Client can now send/receive game messages

**Message Envelope:**

All WebSocket messages use this envelope structure:

```typescript
// Client → Server
interface ClientMessage {
  type: string;           // Message type (lowerCamel)
  requestId?: string;     // Optional request tracking ID
  payload: object;        // Message-specific data
}

// Server → Client
interface ServerMessage {
  type: string;           // Response type (mirrors request or server-initiated)
  requestId?: string;     // Mirrors client requestId if applicable
  payload: object;        // Response data
}

// Server → Client (Error)
interface ErrorMessage {
  type: "error";
  requestId?: string;     // Mirrors client requestId if applicable
  payload: {
    code: string;         // Error code (e.g., "INVALID_ACTION", "TABLE_FULL")
    message: string;      // Human-readable error
  }
}
```

**Message Types & Schemas:**

**Authentication (implicit via cookie):**
- Connection upgrade validates session cookie
- Server automatically associates connection with `user_id`
- No explicit auth message needed after connection

**Lobby Messages:**

```typescript
// Client requests lobby state
{
  type: "getLobby",
  requestId: "req123",
  payload: {}
}

// Server responds with lobby data
{
  type: "lobbyState",
  requestId: "req123",
  payload: {
    tables: [
      {
        tableId: "t1",
        name: "Table 1",
        status: "open" | "in_play" | "closed",
        players: 2,
        maxPlayers: 2,
        smallBlind: 10,
        bigBlind: 20
      }
    ],
    onlinePlayers: [
      { userId: "u1", username: "alice" },
      { userId: "u2", username: "bob" }
    ]
  }
}
```

**Table Join/Leave:**

```typescript
// Client joins table
{
  type: "tableJoin",
  requestId: "req124",
  payload: {
    tableId: "t1",
    seatIndex: 0,      // 0 or 1 for heads-up
    buyIn: 1000        // Chips to bring to table
  }
}

// Server confirms join
{
  type: "tableJoined",
  requestId: "req124",
  payload: {
    tableId: "t1",
    seatIndex: 0,
    stack: 1000,
    tableState: {
      // Current table snapshot
      seats: [
        { seatIndex: 0, userId: "u1", username: "alice", stack: 1000 },
        { seatIndex: 1, userId: null, username: null, stack: null }
      ],
      handInProgress: false
    }
  }
}

// Client leaves table
{
  type: "tableLeave",
  requestId: "req125",
  payload: {
    tableId: "t1"
  }
}

// Server confirms leave
{
  type: "tableLeft",
  requestId: "req125",
  payload: {
    tableId: "t1",
    returnedChips: 950  // Chips returned to balance
  }
}
```

**Game Actions:**

```typescript
// Client performs action
{
  type: "action",
  requestId: "req126",
  payload: {
    tableId: "t1",
    actionType: "fold" | "check" | "call" | "bet" | "raise",
    amount?: number     // Required for bet/raise
  }
}

// Server acknowledges action
{
  type: "actionAck",
  requestId: "req126",
  payload: {
    success: true
  }
}

// Server broadcasts game state update to all players at table
{
  type: "gameState",
  payload: {
    tableId: "t1",
    handNumber: 42,
    street: "preflop" | "flop" | "turn" | "river" | "showdown",
    pot: 150,
    communityCards: ["Ah", "Kd", "Qs"],
    seats: [
      {
        seatIndex: 0,
        userId: "u1",
        username: "alice",
        stack: 950,
        bet: 50,
        cards: ["??", "??"],  // Hidden unless showdown or own cards
        isDealer: true,
        isActive: false,
        hasFolded: false
      },
      {
        seatIndex: 1,
        userId: "u2",
        username: "bob",
        stack: 900,
        bet: 100,
        cards: ["??", "??"],
        isDealer: false,
        isActive: true,
        hasFolded: false
      }
    ],
    currentPlayerSeat: 1,
    timeRemaining: 10  // Seconds remaining for current action
  }
}
```

**Timer/Tick Messages:**

```typescript
// Server sends periodic timer updates
{
  type: "tick",
  payload: {
    tableId: "t1",
    currentPlayerSeat: 1,
    timeRemaining: 7  // Seconds left for action
  }
}

// Server sends timeout notification
{
  type: "timeout",
  payload: {
    tableId: "t1",
    seatIndex: 1,
    defaultAction: "fold"  // Action taken on timeout
  }
}
```

**Reconnect Flow:**

```typescript
// Client reconnects after disconnect
{
  type: "reconnect",
  requestId: "req127",
  payload: {
    tableId: "t1"  // Table player was at
  }
}

// Server restores session (if within 30s window)
{
  type: "reconnected",
  requestId: "req127",
  payload: {
    success: true,
    tableState: {
      // Full current state snapshot
      ...
    }
  }
}

// Server rejects reconnect (timeout expired)
{
  type: "error",
  requestId: "req127",
  payload: {
    code: "RECONNECT_TIMEOUT",
    message: "Reconnect window expired. Seat has been released."
  }
}
```

**Hand Result:**

```typescript
// Server broadcasts hand result
{
  type: "handResult",
  payload: {
    tableId: "t1",
    handNumber: 42,
    winner: {
      seatIndex: 0,
      userId: "u1",
      username: "alice",
      winAmount: 200,
      handRank: "Pair of Aces"
    },
    finalCards: [
      { seatIndex: 0, cards: ["Ah", "As"] },
      { seatIndex: 1, cards: ["Kh", "Qd"] }  // Revealed at showdown
    ],
    pot: 200
  }
}
```

**Error Messages:**

```typescript
// Common error codes
{
  type: "error",
  requestId: "req128",
  payload: {
    code: "TABLE_FULL" | "INVALID_ACTION" | "INSUFFICIENT_BALANCE" | 
          "NOT_YOUR_TURN" | "INVALID_BET_AMOUNT" | "SEAT_OCCUPIED" |
          "RECONNECT_TIMEOUT" | "TABLE_NOT_FOUND",
    message: "Human-readable error description"
  }
}
```

**WebSocket Close Codes:**
- `1000` - Normal closure (client disconnect)
- `1008` - Policy violation (malformed message, validation failure)
- `1011` - Server error (unexpected server-side error)

**Example Message Sequences:**

**Sequence 1: Join Table → Play Hand → Win**
```
1. C→S: tableJoin {tableId: "t1", seatIndex: 0, buyIn: 1000}
2. S→C: tableJoined {seatIndex: 0, stack: 1000, tableState: {...}}
3. S→C: gameState {handNumber: 1, street: "preflop", currentPlayerSeat: 0, ...}
4. S→C: tick {timeRemaining: 10}
5. C→S: action {actionType: "raise", amount: 50}
6. S→C: actionAck {success: true}
7. S→C: gameState {street: "preflop", currentPlayerSeat: 1, ...}
8. ... (hand continues)
9. S→C: handResult {winner: {...}, pot: 200}
10. S→C: gameState {handNumber: 2, street: "preflop", ...}
```

**Sequence 2: Disconnect → Reconnect**
```
1. [Connection lost]
2. [Client reconnects within 30s]
3. C→S: reconnect {tableId: "t1"}
4. S→C: reconnected {success: true, tableState: {...}}
5. S→C: gameState {currentPlayerSeat: 0, timeRemaining: 5, ...}
```

**Validation Rules:**
- All incoming messages MUST match expected schema for their type
- Invalid messages → send error response + optionally close connection (1008)
- Out-of-turn actions → send error "NOT_YOUR_TURN"
- Invalid bet amounts → send error "INVALID_BET_AMOUNT"
- Malformed JSON → close connection (1008)

### Frontend Architecture
- Rendering: canvas for table; React components for lobby/auth/controls.
- State: React Query for HTTP data; WS reducer/Zustand store for live table state.
- Routing: React Router (lobby/auth/table routes).
- Testing: Vitest + Testing Library (already planned in starter); snapshot/interaction tests for UI; reducer tests for WS state.

### Infrastructure & Deployment
- Serving: C++ binary serves `dist/` and handles HTTP/WS on one port; optional nginx/traefik for TLS/static in front.
- Config: env vars for DB file path, ports, secrets (cookie signing, hash cost).
- Logging/Monitoring: structured stdout; rotate via supervisor/systemd; no external metrics in MVP.
- Deploy: Linux VPS, systemd service; manual rollout acceptable initially.

### Decision Impact Analysis
**Implementation Sequence:**
1) Define DB schema + migrations; implement auth/session; hash + cookie signing.
2) Implement HTTP endpoints (auth/register/reload/lobby).
3) Implement WS protocol, timers, reconnect rules, seat/table state machine.
4) Integrate client starter; set up React Router, Query, WS store; canvas table rendering.
5) Wire C++ static serving + TLS proxy (if used); logging and config.

**Cross-Component Dependencies:**
- Session cookie format shared between HTTP and WS upgrade.
- Timer/reconnect rules enforced server-side and reflected in client state.
- DB schema migrations must run before server start; client expects lobby/table shapes defined by API/WS contracts.

## Implementation Patterns & Consistency Rules

### Pattern Categories Defined
**Critical Conflict Points Identified:** naming, API/WS shapes, state/event handling, error/loading flows, file layout.

### Naming Patterns
**Database:** tables snake_case plural (`users`, `sessions`, `tables`, `seats`, `balances`, `hands_log`); columns snake_case (`user_id`, `created_at`); indexes `idx_<table>_<col>`; FKs `<ref>_id`.
**API/WS JSON:** camelCase fields; IDs as strings; timestamps ISO-8601 UTC strings.
**Endpoints/Routes:** REST paths plural (`/api/users`, `/api/sessions`, `/api/lobby`, `/api/reload`); WS events are lowerCamel verbs with nouns (`tableJoin`, `tableLeave`, `action`, `reconnect`, `tick`).
**Code:** React components PascalCase; files kebab-case (`lobby-view.tsx`); hooks `useX`; helper libs lowerCamel; constants SCREAMING_SNAKE.

### Structure Patterns
**Project org:** client feature folders (`lobby/`, `auth/`, `table/`, `ws/`, `canvas/`); co-locate tests as `*.test.ts/tsx`. Shared utils in `shared/` or `lib/`.
**Config:** `.env` for client build-time; backend env via process env vars; sample `.env.example`.
**Static:** built client in `dist/` served by C++; assets under `public/` before build.

### Format Patterns
**HTTP responses:** `{ data, error }` wrapper; `error` null on success; `error` = `{ code, message }`.
**WS messages:** envelope `{ type, requestId?, payload }`; server responses mirror `type`; errors use `{ type: "error", requestId?, payload: { code, message } }`.
**Status codes:** HTTP 200/201 for success, 400/401/403/429/500 as usual; WS close codes: 1008 (policy) for invalid payload, 1011 (server error).

### Communication Patterns
**Events:** `type` strings lowerCamel; payloads stable and versioned via optional `v` field if needed.
**State updates:** immutable updates; reducers/pure functions for WS events; React Query for HTTP fetches.
**Timing:** server authoritative; client renders timers but trusts server ticks.

### Process Patterns
**Validation:** schema-validate all incoming HTTP/WS payloads; reject/close on failure with code/message.
**Auth flow:** set HTTP-only signed cookie on login; send cookie on WS upgrade; server binds session to user id; expire on logout/timeout.
**Error handling (client):** toast/banner for recoverable errors; inline errors on forms; WS errors surface in a non-blocking way and prompt reconnect if needed.
**Loading:** boolean flags per request; disable buttons during submit; skeletons/spinners for lobby lists; table keeps rendering last known state during WS reconnect attempts.

### Enforcement Guidelines
**All AI Agents MUST:**
- Use snake_case in DB, camelCase in JSON, PascalCase for components.
- Wrap HTTP responses as `{ data, error }` and WS messages as `{ type, requestId?, payload }` (errors use `type: "error"`).
- Validate all external inputs (HTTP/WS) against schemas before processing.

**Pattern Enforcement:**
- Add lint rules for file naming and camelCase in TS.
- Add lightweight JSON schema validators in C++ for HTTP/WS.
- Document violations in PRs and align to these rules.

### Pattern Examples
**Good:**
- HTTP success: `{ "data": { "tables": [...] }, "error": null }`
- WS action: `{ "type": "action", "requestId": "abc", "payload": { "tableId": "t1", "action": "bet", "amount": 50 } }`
- Table row: `CREATE TABLE seats (id TEXT PRIMARY KEY, table_id TEXT NOT NULL, user_id TEXT, seat_index INTEGER NOT NULL, created_at TEXT NOT NULL);`
**Anti-Patterns:**
- Mixed casing in JSON (`user_id`), bare payloads without `type`, DB tables in PascalCase, mutating shared state in-place in reducers.
