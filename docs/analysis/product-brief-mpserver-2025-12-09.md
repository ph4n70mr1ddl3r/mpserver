---
stepsCompleted: [1, 2, 3, 4, 5, 6]
inputDocuments: []
workflowType: 'product-brief'
lastStep: 6
project_name: 'mpserver'
user_name: 'Riddler'
date: '2025-12-09'
---
# Product Brief: mpserver

**Date:** 2025-12-09
**Author:** Riddler

---

<!-- Content will be appended sequentially through collaborative workflow steps -->

## Executive Summary

mpserver will offer heads-up no-limit cash games that evolve to a mental poker model, delivering provable fairness. Players who currently suspect manipulated shuffles, collusion, or server peeking will get mathematically verifiable guarantees that neither the operator nor opponents can cheat. Open-source clients plus cryptographic proofs replace “trust us” claims with transparent evidence.

---

## Core Vision

### Problem Statement

Serious poker players often suspect hidden manipulation: biased shuffles, collusion aided by server insight, or card-leakage. Current platforms assert fairness but cannot provide user-verifiable proof, eroding trust and deterring play.

### Problem Impact

Players churn or sit out action due to doubt, missing games and EV. Trust-sensitive grinders and communities avoid or fragment across sites, limiting liquidity and engagement.

### Why Existing Solutions Fall Short

Major sites rely on reputational claims and private audits; they can’t expose user-verifiable math or prevent operator-level insight into cards. Anti-collusion tools exist but don’t eliminate the possibility of bias or peeking, and no mainstream option offers cryptographic proof of fair shuffles.

### Proposed Solution

Start with a stable, performant heads-up cash platform (timeouts, multi-table support, standard rules). Then upgrade to mental poker: cryptographic protocols ensure no server or client can see unexposed cards, and shuffles are mathematically provable to all participants. Open-source clients plus public proofs allow any player to verify fairness.

### Key Differentiators

- Provable fairness: mental poker so neither server nor opponents can see or bias hidden cards.
- Transparency: open-source clients and visible cryptographic proofs for each shuffle/hand.
- Trust-first positioning: designed for players who feel current sites can’t back their fairness claims.
- Roadmap: clear path from stable v1 to full mental poker deployment, maintaining liquidity and performance along the way.

## Target Users

### Primary Users

- Fairness-focused grinders and security-minded regulars who suspect current sites can bias shuffles or peek at cards. They want verifiable proof and will try play-money tables to validate pacing and fairness before committing time or reputation.
- Trust-curious casuals who want “no funny business” and are open to a heads-up format that feels fast and transparent. They will sample because it’s free to reload and easy to understand.

### Secondary Users

- Community owners/moderators who want incorruptible heads-up matches for their group.
- Streamers/reviewers who showcase fairness tech and drive discovery.
- Auditors/third-party reviewers who may validate proofs and open-source clients.

### User Journey

- Discovery: word of mouth in poker communities/forums, creator/streamer demos, fairness-focused content.
- Onboarding: lightweight account, play-money balance with free reloads; no deposits initially.
- Core Usage: heads-up NL cash play with stable pacing, timeouts, and multi-table support; early transparency cues even before mental poker goes live.
- Success Moment: viewing a shuffle/proof demo or open-source client link, seeing per-hand verification tools that show operators can’t see or bias cards.
- Long-term: regular play anchored on fairness signals; ready to transition seamlessly when mental poker launches.

## Success Metrics

- User success: players can play reliably with stable software and decent speed; tables feel as responsive as traditional online poker even with cryptography running each action.
- Trust signals: open-source client available and used; cryptographic verification runs in real time on each move/hand so players can see proof the operator can’t bias shuffles or view cards.
- Experience quality: gameplay pacing and UX match standard online poker expectations (timeouts handled cleanly, multi-table remains smooth as cryptography scales).

### Business Objectives

- Pre-monetization traction: daily players and daily hands played on play-money heads-up tables.
- Future monetization readiness: baseline the platform while play-money, then track average rake per day and average rake per hand once rake is enabled.

### Key Performance Indicators

- Reliability: percentage of hands completed without disconnects/timeouts; stability across multiple tables.
- Speed/pacing: action latency and table responsiveness comparable to traditional online poker despite cryptographic overhead.
- Trust engagement: availability and usage of open-source client and per-hand/per-move cryptographic proofs.
- Traction: daily active play-money users and total hands played per day.
- Monetization (when active): average rake per day and average rake per hand.

## MVP Scope
### Core Features

- Heads-up NLHE cash play-money tables with stable pacing; timeouts and disconnections handled cleanly.
- Admin can open/close tables; lobby lists tables and players online.
- Accounts with username/password; players can reload balance to a max value anytime; no rake initially.
- Basic game only for MVP (no mental poker yet; no open-source release yet).

### Out of Scope for MVP

- Real-money deposits/withdrawals.
- Tournaments/SNGs; tables with more than 3 players.
- Loyalty, leaderboards, clubs/private rooms.
- Mental poker and cryptographic proofs; open-source client release.

### MVP Success Criteria

- Server runs reliably; handles timeouts and disconnections gracefully.
- Resistant to potential malicious clients.
- Stable operation under expected load for heads-up play-money tables.

### Future Vision

- Full mental poker implementation with per-hand cryptographic proofs and open-source clients.
- Real-money support and rake.
