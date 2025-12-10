---
stepsCompleted: [1, 2, 3, 4, 5]
inputDocuments:
  - docs/prd.md
  - docs/analysis/product-brief-mpserver-2025-12-09.md
  - docs/architecture.md
workflowType: 'ux-design'
lastStep: 5
project_name: 'mpserver'
user_name: 'Riddler'
date: '2025-12-09'
---

# UX Design Specification mpserver

**Author:** Riddler
**Date:** 2025-12-09

---

<!-- UX design content will be appended sequentially through collaborative workflow steps -->

## Executive Summary

### Project Vision

mpserver is a heads-up no-limit hold'em (NLHE) play-money poker platform designed to solve the trust crisis in online poker. While current poker sites rely on "trust us" claims and private audits, mpserver will evolve toward **mental poker cryptography** - mathematical proofs that neither operators nor opponents can bias shuffles or peek at hidden cards. The MVP delivers stable, fast poker gameplay that feels like mainstream platforms, while establishing fairness positioning and technical foundations for future cryptographic verification without rewrites.

The platform targets players who suspect manipulation on current sites and want verifiable proof of fairness. By combining transparent game state, open-source clients (post-MVP), and per-hand cryptographic proofs, mpserver replaces reputational trust with mathematical certainty.

### Target Users

**Primary Users:**

- **Fairness-focused grinders**: Serious poker players who suspect current sites can bias shuffles, enable collusion, or peek at cards. They want cryptographically verifiable proof and will rigorously test platform stability, pacing, and fairness cues before committing time and reputation. Tech-savvy, high-volume players who value transparency over flashy features.

- **Trust-curious casuals**: Players who want "no funny business" and appreciate transparency without needing technical depth. They'll sample the platform because free balance reloads make it risk-free and the heads-up format is easy to understand. They care more about feeling confident than reading proofs.

**Secondary Users:**

- Community owners/moderators seeking incorruptible heads-up matches for their groups
- Streamers and reviewers who showcase fairness technology and drive discovery
- Auditors and third-party reviewers who may validate proofs and open-source clients

**User Context:**

- Users arrive skeptical of current poker platforms and looking for verifiable fairness
- Grinders expect fast, stable gameplay with latency comparable to mainstream poker sites
- Casuals expect simple onboarding, clear game state, and graceful handling of disconnects/interruptions
- Both groups need to see a clear fairness roadmap even before mental poker is live
- Desktop-first users (canvas rendering optimized for larger screens)
- Sessions range from quick play to extended grinding; platform must handle both gracefully

### Key Design Challenges

**1. Trust Through Transparency (The Core UX Challenge)**

The entire value proposition depends on making users FEEL the fairness, not just claim it. The MVP paradox: we must establish trust BEFORE mental poker goes live, but cryptographic proofs aren't ready yet. UX must visually communicate "fairness positioning" and the roadmap without proofs, while balancing grinders' desire for technical depth against casuals' need for "just works" simplicity.

**2. Real-Time State Clarity Under Network Stress**

With strict timers (10-second actions, 30-second reconnect window, 1-minute sitout removal), players need crystal-clear feedback at all times:

- **Timer anxiety**: Players must always know how much time they have without feeling rushed
- **Reconnect uncertainty**: During disconnects, players lose visibility and need reassurance that state is preserved
- **State synchronization**: Canvas rendering + WebSocket updates must feel instant, never laggy or out-of-sync
- **No observer validation**: Since observers only see lobby (no table observation in MVP), UX cannot rely on spectator confirmation

**3. Single-Table MVP That Feels Like the Future**

The MVP is constrained (single table, play-money only, no mental poker yet), but users must see the vision and platform potential. Play-money + single table could feel like a toy demo rather than a serious platform. UI patterns must scale to multi-table, cryptographic proofs, and real-money without breaking user expectations. Canvas rendering means building everything (buttons, chips, cards, animations) from scratch - must feel polished and premium, not prototype-quality.

### Design Opportunities

**1. Make Fairness VISIBLE, Even Without Proofs**

Turn fairness from an abstract promise into a tangible experience:

- **Transparent game state**: Show shuffle metadata, hand IDs, server timestamps - make the "black box" glass
- **Fairness roadmap integration**: Embed "coming soon: cryptographic proofs" messaging directly into table UI (not just marketing copy)
- **Open audit hooks**: Even in MVP, show "this hand could be verified" placeholders to build the mental model for future proof workflows
- **Visual trust cues**: Use design language that signals security, transparency, and integrity (like banking apps use shields and locks)

**2. Reimagine Poker UX With Real-Time State as First-Class**

Most poker sites bolt real-time functionality onto legacy UI. We have a clean slate with WebSocket-first architecture:

- **Timer-first design**: Make timers beautiful and calming, not stressful - show progress visualization, not countdown panic
- **Reconnect grace**: Turn the 30-second reconnect window into a smooth "pause and resume" experience, not an error state or failure mode
- **Authoritative server state**: Since server is authoritative, show state changes as smooth transitions, not jarring DOM updates
- **Heads-up intimacy**: Design for 1v1 psychology - eye contact, read timing, focused attention - not crowded 9-player table chaos

**3. Canvas Rendering = Total Design Control**

Building on canvas means we're not constrained by DOM/CSS limitations:

- **Smooth animations**: Chip stacks sliding, cards flipping, pot building - all physics-based and buttery smooth (60fps+)
- **Performance storytelling**: Show that canvas is FAST - instant state updates prove the platform is technically solid
- **Unique visual language**: Create a signature look that screams "this isn't clipart poker" - premium, modern, trustworthy, distinct
- **Accessibility**: Provide DOM-based fallback or screen-reader mode for accessibility compliance without compromising performance

## Core User Experience

### Defining Experience

The core experience of mpserver centers on the **real-time poker action loop**: Player sits at heads-up table → receives private cards → acts within 10-second timer → sees instant state updates → hand resolves → next hand begins. This loop must feel completely seamless, with server-authoritative state rendered as smooth client-side transitions.

**Primary User Actions:**

- **Taking poker actions** (fold, check, call, bet, raise) within the 10-second action timer
- **Monitoring game state** (pot size, opponent stack, community cards, timer remaining) continuously
- **Reconnecting gracefully** after temporary disconnects within the 30-second window
- **Reloading play-money balance** with one-click free top-ups to maximum value

**Core Interaction Loop:**

The most critical interaction to get right is the **timer + action feedback loop**. When a player acts, they must receive instant confirmation that:
1. Their action was received by the server
2. Their timer stopped
3. Game state updated correctly (chips moved, pot adjusted)
4. Opponent's turn began with their timer visible

This loop repeats dozens of times per session and defines whether the platform feels responsive and trustworthy.

### Platform Strategy

**Primary Platform:** Desktop web application optimized for larger screens (1920x1080 minimum recommended)

**Technical Stack:**
- Canvas-based table rendering for 60fps+ smooth animations and custom visual control
- React application shell for authentication, lobby, and game controls
- WebSocket + JSON protocol for real-time state synchronization
- Mouse/keyboard input (click actions, keyboard shortcuts for experienced players)

**Platform Constraints:**
- Real-time connection required (no offline functionality)
- Desktop-first experience (mobile/tablet not prioritized in MVP)
- Modern browser with canvas and WebSocket support required
- Low-latency network connection recommended for optimal experience

**Future Platform Considerations:**
- Mobile-responsive design deferred to post-MVP
- Multi-monitor support for future multi-table functionality
- Accessibility fallback mode for screen readers

### Effortless Interactions

These user actions must feel completely natural and require zero cognitive effort:

**1. Timer Clarity**

Players should NEVER wonder "how much time do I have?" The timer must be:
- Always visible with clear progress visualization (circular ring around player avatar)
- Calming rather than anxiety-inducing (smooth countdown, not flashing panic)
- Accurate to the server-authoritative time with < 100ms client sync lag
- Color-coded for time remaining (green → yellow → orange → red)

**2. Reconnect Grace**

The 30-second reconnect window should feel like a "pause button" feature, not an error state:
- Automatic reconnection when network returns within window
- Preserved game state with no user recovery actions needed
- Clear "reconnecting..." UI that shows time remaining in window
- Smooth resume that picks up exactly where player left off

**3. Instant State Synchronization**

When any game state changes (cards dealt, action taken, pot updated), updates must feel instant:
- Target perceivable lag < 100ms from server message to visual update
- Smooth canvas animations (chips sliding, cards flipping) at 60fps+
- No jarring DOM re-renders or layout shifts
- Optimistic UI updates confirmed by server (action buttons respond immediately)

**4. One-Click Balance Reloads**

Free play-money top-ups must be zero friction:
- Single button click to reload to maximum balance
- Instant balance update without navigation or confirmation dialogs
- Available from both lobby and table views
- No form fields, no multi-step flows

**5. Automatic Session Management**

Authentication and session continuity should be invisible:
- Session persists across browser refreshes
- Automatic re-authentication on reconnect
- No repeated login prompts during active play
- Graceful session expiry with clear re-login path

### Critical Success Moments

These are the make-or-break moments that determine whether users trust and continue using mpserver:

**1. First Reconnect Experience**

The moment a player disconnects mid-hand (accidentally close tab, network blip) and successfully reconnects within 30 seconds to see their exact game state preserved WITH time remaining clearly shown. This proves the platform is reliable and won't punish network issues.

**2. First Transparency Cue**

The moment a player notices shuffle metadata, hand IDs, or server timestamps displayed openly in the UI and thinks "they're not hiding anything." Even without cryptographic proofs in MVP, visible transparency builds the trust foundation.

**3. First Buttery-Smooth Animation**

The moment chips slide smoothly into the pot, cards flip with physics-based motion, or the timer ring animates at 60fps - and the player realizes "this feels premium, not like clipart poker." Canvas performance becomes a trust signal that the platform is technically solid.

**4. First Successful Action Under Pressure**

The moment a player makes a critical poker decision with 2 seconds left on the timer, clicks their action, and sees INSTANT confirmation (button state change, timer stop, chips move) - proving the platform won't lag during critical moments.

**5. Balance Reload Without Friction**

The moment a casual player runs low on chips mid-session, clicks "Reload Balance," and continues playing within 1 second. No forms, no interruption, no barriers to continued play.

**Failed Experience (What Would Ruin It):**

- Timer expiring unexpectedly because client showed 3s remaining but server had already timed out
- Disconnect resulting in auto-fold when player thought they had 30s reconnect window
- State appearing out-of-sync (player sees opponent fold, but pot already awarded)
- Laggy animations or dropped frames making the platform feel janky and untrustworthy

### Experience Principles

These guiding principles inform all UX design decisions for mpserver:

**1. Server Authority, Client Trust**

The server is authoritative on all game state, but UX must make players FEEL in control. Display server state immediately upon receipt; render state changes as smooth transitions, not sudden jumps. Show what IS, not what the client thinks might be.

**2. Timer Transparency**

Time is always visible, never a source of stress or confusion. Use progress visualization (rings, bars, colors) that communicate "time remaining" rather than "countdown panic." Timers should feel like helpful guides, not ticking timebombs.

**3. Reconnect as Feature, Not Failure**

The 30-second reconnect window is a FEATURE (pause/resume capability), not an error state to recover from. UX embraces temporary disconnects as normal network behavior, not exceptional failures. Reconnecting should feel as natural as unpausing a video.

**4. Fairness is Visible**

Even without cryptographic proofs in MVP, transparency must be visual and tangible. Display hand IDs, shuffle metadata, server timestamps, and fairness roadmap directly in the table UI. Make the invisible visible. Transform "trust us" into "see for yourself."

**5. Canvas Performance as Trust Signal**

Buttery smooth 60fps+ animations aren't just aesthetic polish - they're proof that the platform is technically solid and ready for serious play. Performance storytelling: if the visuals are fast and responsive, users infer the backend is equally solid.

**6. Heads-Up Intimacy**

Design for 1v1 poker psychology: eye contact metaphors, read timing, focused attention. Not the crowded chaos of 9-player tables. Use spatial design to emphasize the direct heads-up nature - two players, face to face, nothing hidden.

## Desired Emotional Response

### Primary Emotional Goals

mpserver's emotional design is built on transforming skepticism into trust through transparency and reliability. Unlike competitors who ask users to "trust us," mpserver creates an emotional foundation of **verified confidence**.

**Core Emotional Objectives:**

1. **TRUST and CONFIDENCE** - Users should feel "this platform can't fool me" rather than "I hope they're being honest." The shift from blind faith to verified certainty defines the mpserver emotional experience.

2. **CALM CONTROL** - Players should feel in command of their decisions, never panicked or rushed by strict timers. The 10-second action window should feel like ample time for thoughtful play, not countdown pressure.

3. **RESPECT and VALIDATION** - The UX communicates "we treat you like an intelligent adult who deserves transparency," not "trust our black box and don't ask questions."

These emotional goals directly counter the frustrations users experience on competitor platforms: anxiety from unclear timers, skepticism from opaque systems, and frustration from hiding technical details.

### Emotional Journey Mapping

**1. First Discovery → Curiosity + Cautious Optimism**

Emotion: "Finally, someone talking about provable fairness...but does it actually work?"

Design Implications:
- Clear fairness roadmap visible on landing page and in-product
- Transparent tech stack mentioned openly (C++, WebSocket, mental poker)
- No marketing fluff - direct technical honesty builds legitimacy with grinders

**2. First Session → Relief + Growing Confidence**

Emotion: "This feels smooth. Timers are clear. State makes sense. Maybe they're serious."

Design Implications:
- Buttery 60fps+ animations prove technical quality immediately
- Timer clarity eliminates anxiety from first hand
- Instant action feedback builds confidence in platform responsiveness

**3. Core Experience (Playing Hands) → Focused Calm**

During action: "I have time. I can think. No panic."  
After action: "That worked perfectly. Next hand."

Design Implications:
- Timer shown as progress indicator (green ring depleting smoothly), not countdown bomb
- Smooth state transitions create rhythm, not jarring updates
- Micro-confirmations on actions (button state, chip movement) provide constant reassurance

**4. First Disconnect/Reconnect → Surprised Relief**

Emotion: "Wait...my state is exactly where I left it. They didn't auto-fold me. Wow."

Design Implications:
- Reconnect window shows "You have 23 seconds to reconnect" - clarity, not panic
- State preservation is VISIBLE (hand state + timer shown immediately on reconnect)
- This moment transforms platform trust from theoretical to experiential

**5. Noticing Transparency → Deepening Trust**

Emotion: "They're showing hand IDs, shuffle metadata, timestamps...they're not hiding anything."

Design Implications:
- Hand ID, shuffle metadata, server timestamp visible in table UI (not buried in logs)
- Fairness roadmap integrated directly where players see it (not just marketing pages)
- Technical details shown openly to grinders; simplified for casuals (progressive disclosure)

**6. Returning → Loyalty + Advocacy**

Emotion: "This is what online poker should be. I'm telling my poker group."

Design Implications:
- Consistent reliability across sessions builds the trust loop
- No degradation in performance or clarity over time
- Platform earns word-of-mouth through dependable experience

**If Something Goes Wrong → Reassurance, Not Panic**

Network blip: "Reconnecting in 25s..." not "ERROR: CONNECTION LOST"

Design Implications:
- Grace periods communicate understanding, not punishment
- Recovery paths are clear, not confusing error states
- System assumes good faith (network issues happen), not user failure

### Micro-Emotions

**Confidence vs. Confusion:**

CRITICAL: Users must feel confident about game state at ALL times. Every UI element answers two questions:
- "What just happened?" (past state)
- "What happens next?" (future action)

Design Choices:
- Explicit state labels ("Waiting for opponent", "Your turn - 7s remaining")
- Visual confirmations for every action taken
- No ambiguous states or unclear feedback

**Trust vs. Skepticism:**

THE defining emotional axis for mpserver. Visible transparency + consistent reliability transforms skeptics into advocates.

Design Choices:
- Show, don't claim: hand IDs, shuffle metadata, timestamps visible
- Fairness roadmap integrated in UI, not hidden in marketing
- Never hide technical details - transparency defeats skepticism

**Calm vs. Anxiety:**

Timers must never induce panic. The 10-second window should feel manageable, not stressful.

Design Choices:
- Progress rings show "time available" not "time running out"
- Color transitions gradual (green → yellow at 5s → orange at 3s → red at 1s)
- No flashing warnings or audio panic cues
- Smooth countdown, no jarring ticks

**Accomplishment vs. Frustration:**

Completing hands smoothly creates micro-accomplishments that build confidence. Laggy or confusing state creates frustration that destroys trust instantly.

Design Choices:
- Every successful action confirmed visually within 100ms
- Hand completion shows clear winner + pot distribution
- Session progress visible (hands played, time at table)

**Delight vs. Satisfaction:**

Buttery 60fps animations create "wow" delight moments. Core loop delivers consistent satisfaction without flashy gimmicks.

Design Choices:
- Physics-based chip animations (realistic weight, momentum)
- Smooth card flips with subtle blur motion
- Micro-animations on action confirmations (button press feedback)
- Performance quality as a form of delight ("this is premium")

### Design Implications

**To Create TRUST:**

- Display shuffle metadata, hand IDs, server timestamps openly in table UI
- Use banking/security visual language (shields, locks, verification checkmarks)
- Show fairness roadmap directly at table (not just marketing copy)
- Never hide technical details from grinders (progressive disclosure for casuals)
- Consistency is trust: every session feels identical in quality and reliability

**To Create CALM CONTROL:**

- Timer as circular progress ring around player avatar (green → yellow → orange → red)
- No flashing warnings, audio panic cues, or countdown urgency
- Clear "You have X seconds" label always visible during your turn
- Smooth transitions create rhythm, not jarring state jumps
- Action buttons respond instantly (optimistic UI) with server confirmation

**To Create RESPECT:**

- Assume users are intelligent - show them the technical truth
- One-click actions (balance reload, table join) respect their time
- No patronizing tooltips or excessive hand-holding
- Keyboard shortcuts for power users (F = fold, C = check/call, R = raise)
- Technical depth available for those who want it; simplified for those who don't

**To Avoid ANXIETY:**

- Reconnect window prominently shows: "You have 22 seconds to reconnect"
- State synchronization < 100ms perceived lag (no "did that work?" doubt)
- Failed states show clear recovery paths, not confusing errors or dead ends
- Sitout timer visible before removal ("Sitting out - will be removed in 45s")

**To Create DELIGHT:**

- 60fps+ smooth chip animations with realistic physics
- Card flips with motion blur and subtle 3D perspective
- Micro-animations on action confirmations (button press, chip movement)
- Canvas performance creates "wow, this is fast" moments
- Premium visual quality proves platform is production-ready, not prototype

### Emotional Design Principles

**1. Transparency Defeats Skepticism**

Show don't tell. Make invisible systems visible. Hand IDs, shuffle metadata, timestamps, and fairness roadmap integrated directly in the UI transform "trust us" into "see for yourself."

**2. Performance Is Emotional Proof**

Buttery smooth 60fps animations aren't just pretty - they prove the platform is technically solid. Performance quality creates confidence that extends to trust in fairness.

**3. Grace Periods Communicate Respect**

30-second reconnect windows, 1-minute sitout timers - these grace periods say "we understand networks fail and humans get interrupted." Respect for user reality builds emotional loyalty.

**4. Calm Clarity Over Flashy Urgency**

Timers guide, they don't panic. Progress indicators show time available, not time running out. The emotional tone is focused calm, not rushed anxiety.

**5. Micro-Confirmations Build Macro-Trust**

Every action confirmed within 100ms. Every state change rendered smoothly. These micro-moments of "it worked exactly as expected" compound into macro-feelings of trust and reliability.

**6. Consistency Is Confidence**

Every session feels identical in quality. No performance degradation, no UI inconsistencies, no surprises. Predictable excellence builds the emotional foundation for long-term use.
## UX Pattern Analysis & Inspiration

### Inspiring Products Analysis

mpserver can learn from products that have solved similar trust, real-time, and performance challenges:

**1. Lichess.org (Free Online Chess Platform)**

Lichess is the gold standard for trust-first game platforms. As a free, open-source chess site funded by donations, it demonstrates how transparency builds loyalty in competitive gaming communities.

**UX Strengths:**
- **Open-source transparency**: All code is public, servers are donation-funded, zero commercial agenda - builds deep trust with users who are inherently skeptical
- **Trust through simplicity**: Clean UX with NO ads, NO paywalls, NO dark patterns - users feel respected, not exploited
- **Performance obsession**: Instant move feedback, buttery smooth animations, works flawlessly even on slow connections
- **Calming timer design**: Color-coded circular progress rings around player avatars (green  yellow  red) - shows time available, not panic countdown
- **Fair play indicators**: Openly displays anti-cheat analysis without hiding methodology - transparency over opaqueness

**Why relevant to mpserver**: Poker grinders recognize and appreciate the "no BS" philosophy. They're inherently suspicious of profit motives in gambling platforms. Lichess proves that technical transparency and performance excellence build community loyalty.

(Content continues via file write...)
