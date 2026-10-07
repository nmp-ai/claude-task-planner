# Example 01 — Feature

## Input (pasted text)

> We need users to be able to reset their password themselves. Right now they email support. Send a link by email, link should expire. Mobile and web.

---

## `/analyze` output

## Spec — Self-service password reset
Source: pasted text · Type: Feature

### Summary
Users who forget their password currently contact support. They need to reset it themselves via an emailed link on web and mobile, reducing support load.

### In scope / Out of scope
- In: request reset by email, reset link email, set new password page/screen, link expiry.
- Out: SMS reset, changing password while logged in, account recovery without email access.

### Functional requirements (EARS)
- FR-001: WHEN a user submits the "Forgot password" form with an email THE SYSTEM SHALL send a reset link if the email belongs to an account.
- FR-002: WHEN a user submits the form THE SYSTEM SHALL show the same confirmation message whether or not the email exists.
- FR-003: WHEN a user opens a valid reset link THE SYSTEM SHALL allow setting a new password that meets the password policy.
- FR-004: IF a reset link is expired or already used THEN THE SYSTEM SHALL reject it and offer to request a new one.
- FR-005: WHEN a password is reset THE SYSTEM SHALL invalidate all existing sessions of that user.

### Non-functional requirements
- NFR-001: Reset tokens are single-use, ≥ 128-bit random, stored hashed.
- NFR-002: Max 5 reset requests per email per hour.

### Technical approach (brief)
BE: token table + 2 endpoints (request, confirm), email template via existing mail service. Web: 2 pages. Mobile: 2 screens + deep link handling.

### Assumptions
- A1: An email service already exists and can send transactional emails.

### Open questions
- [NEEDS CLARIFICATION: How long should the link stay valid? (FR-004)]
- [NEEDS CLARIFICATION: Should the reset link open the mobile app via deep link, or always the web page? (FR-003)]

### Risks & dependencies
- Deep link configuration on iOS/Android may need a store release.

### Suggested task type
Story — new user-facing capability.

→ Next: `/clarify`

---

## `/clarify` result (user answered "1B, 2A")

### Clarifications
- Link validity → **B: 30 minutes**
- Mobile behavior → **A: deep link into the app, fallback to web**

FR-004 updated: IF a reset link is older than 30 minutes or already used THEN THE SYSTEM SHALL reject it and offer to request a new one.

---

## `/breakdown` output (abridged)

| Ref | Type | Title | Priority | SP | Hours | Covers | Blocked by |
|---|---|---|---|---|---|---|---|
| S1 | Story | [Auth] Allow users to reset password via email link on web | P1 | 5 | 19 | FR-001…FR-005, NFR-001, NFR-002 | – |
| S1.1 | Sub-task | DB: Add password_reset_tokens table | | | 2 | | – |
| S1.2 | Sub-task | BE: Add POST /auth/password-reset/request endpoint | | | 4 | | S1.1 |
| S1.3 | Sub-task | BE: Add POST /auth/password-reset/confirm endpoint | | | 5 | | S1.1 |
| S1.4 | Sub-task | [P] FE: Build Forgot password and Reset password pages | | | 5 | | – |
| S1.5 | Sub-task | QA: Add E2E tests for reset flow | | | 3 | | S1.2, S1.3, S1.4 |
| S2 | Story | [Auth] Open password reset links in the mobile app | P2 | 3 | 10 | FR-003 | S1 |
| S2.1 | Sub-task | [P] Mobile: Configure deep links for reset URL | | | 4 | | – |
| S2.2 | Sub-task | [P] Mobile: Build Reset password screen | | | 4 | | – |
| S2.3 | Sub-task | QA: Test deep link on iOS and Android | | | 2 | | S2.1, S2.2 |

**Totals:** P1 5 SP / 19h · P2 3 SP / 10h · Overall 8 SP / 29h

### S1 detail

**Title:** [Auth] Allow users to reset password via email link on web
**Priority:** P1 · **Story points:** 5 · **Total hours:** 19

#### User story
As a user who forgot my password, I want to reset it via an emailed link, so that I can log in without contacting support.

#### Acceptance criteria
- AC1: Given a registered email, when I submit "Forgot password", then I receive an email with a reset link within 2 minutes.
- AC2: Given any email, when I submit the form, then I see "If an account exists, we've sent a reset link." (same text for unknown emails).
- AC3: Given a valid link, when I set a password meeting the policy, then my password is changed and I am redirected to login.
- AC4: Given a link older than 30 minutes or already used, when I open it, then I see "This link has expired" and a "Send new link" button.
- AC5: Given I reset my password, when I return to another logged-in device, then that session is signed out.
- AC6: Given 5 requests for one email within an hour, when a 6th is submitted, then no email is sent.

---
Covers: FR-001, FR-002, FR-003, FR-004, FR-005, NFR-001, NFR-002
Source: pasted text
