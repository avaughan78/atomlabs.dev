# Projects & Third-Party Providers

A reference list of active projects and the third-party services/SaaS each one depends on. Compiled from each repo's code, config, and env vars — not from provider dashboards, so billing-only services with no code footprint won't appear here.

---

## Punt & Prominence

Two-sided marketplace connecting local businesses (starting with Cambridge punting companies) with social media creators for promotional collaborations. Web app (Next.js) plus a companion Expo mobile app for creators (`mobile/`, internally named "Flamboyance").

Repo: `punt-and-prominence-platform`

| Provider | Used for |
|---|---|
| Supabase | Postgres DB, auth, storage |
| Stripe | Business billing/subscriptions |
| Resend | Production transactional email |
| Mailtrap | Dev/local transactional email |
| Google Places API | Address/location autocomplete |
| Meta Graph API | Instagram OAuth + data sync |
| TikTok OAuth | Creator TikTok account linking |
| RapidAPI | Third-party data lookups |
| Railway | Web app hosting (auto-deploy on push to `main`) |
| Codemagic | iOS build/sign/TestFlight submission for the mobile app |
| Expo / EAS | Mobile build tooling, invoked from CI |
| Apple App Store Connect | TestFlight/App Store submission (via EAS) |
| GitHub Actions | Nightly Instagram data sync cron |

---

## Flamboyance

A trivia/word game about English collective nouns for groups of animals (a *flamboyance* of flamingos, a *murder* of crows, etc.), with solo, 1v1 duel, and host-driven party modes. iOS app, web client, and Android client all share one Supabase backend.

Repos: `flamboyance` (iOS), `flamboyance-web`, `flamboyance-android`

| Provider | Used for |
|---|---|
| Supabase | Postgres DB, anonymous auth, Realtime, Storage, Edge Functions — shared backend for all three clients |
| Azure AI Foundry (Azure OpenAI, gpt-5-mini) | AI moderation of user-submitted community collective nouns |
| Resend | Transactional email (feedback, community-submission notifications) |
| Apple App Store Connect API | iOS metadata management, TestFlight/App Store submission |
| Google Play Console | Android app distribution, Play App Signing |
| GitHub Pages | Web client hosting (flamboyance.click) |
| GitHub Actions | Web client CI/CD |

Note: `flamboyance/secrets/flamboyance-c5972855208a.json` is a GCP service-account key, provisioned for future Play Console administration — not yet wired into any code.

Known limitation: identity is pure device-Keychain anonymous auth with no recovery path, so TestFlight → App Store transitions can silently orphan accounts.

---

## Hound-Up

Native iOS app for dog walkers/pet-care businesses — client/dog records, calendar/booking (with LLM-assisted parsing of pasted booking requests), walk logging with on-device voice-to-text, invoicing, calendar feed publishing, and support messaging.

Repos: `Hound-Up` (iOS), `houndup-web`

| Provider | Used for |
|---|---|
| Supabase (EU region) | Postgres DB, Edge Functions, Storage |
| Resend | Transactional email (invoices, support messages), sent from Supabase Edge Functions |
| Microsoft Azure OpenAI | Parses pasted booking-request text into structured appointments (server-side, via Edge Function) |
| Apple App Store Connect API | Build export/upload automation |
| Apple iCloud / CloudKit | Optional cross-device sync/backup of local data |
| Apple App Attest / DeviceCheck | Per-device backend request authentication |
| Apple Push Notification service (APNs) | Push capability (entitlement configured) |
| Apple MapKit | On-device address/place autocomplete |
| Apple Speech framework | On-device voice-to-text for walk notes |
| GitHub Pages | Marketing/legal site hosting (houndup.atomlabs.dev) |
| GitHub Actions | Static site deploy |

Explicitly absent: no payments processing, no analytics/tracking (disclaimed in the app's own privacy policy).

---

## HirePotential

iOS (and in-progress Android) app helping job seekers — CV/LinkedIn review, competency question practice, and mock interview rehearsal with AI scoring. No accounts, on-device storage, one-time IAP unlock.

Repos: `HirePotential` (iOS + Azure Functions backend), `hirepotential-web`, `hirepotential-android`

| Provider | Used for |
|---|---|
| Microsoft Azure Functions | Backend compute hosting |
| Azure AI Foundry | LLM-backed CV/LinkedIn review, question generation, interview scoring |
| Azure Table Storage | Per-device entitlement/attestation state |
| Resend | Transactional email for feedback/contact forms (replaced Mailjet) |
| Apple App Attest / DeviceCheck | iOS request authentication |
| Apple StoreKit 2 | iOS in-app purchase |
| Apple App Store Server Library / API | Purchase and refund verification |
| Apple App Store Connect API | Automated build upload/versioning |
| GitHub Pages | Marketing/privacy site hosting (hirepotential.co.uk) |
| Google Play Integrity API | Android device attestation (stubbed, not yet live) |
| Google Play Billing | Android in-app purchase (planned) |
| Google Play Developer API | Android purchase verification (planned) |

Explicitly absent: no CI/CD automation, no BaaS (Supabase/Firebase), no analytics or error tracking.

---

## Halosec (halov2)

Flask-based security-scanning SaaS — scans a domain, produces a weighted security-posture score across 8 audit areas, and offers an AI ("Gabriel") remediation chat to explain findings.

Repo: `halov2`

| Provider | Used for |
|---|---|
| Railway | Primary production hosting |
| Render | Alternate/secondary deployment target |
| Supabase | Optional auth, user accounts, saved scan history |
| Redis | Shared rate-limit counters across gunicorn workers (Flask-Limiter) |
| Stripe | One-off and subscription billing |
| OpenAI (or OpenAI-compatible endpoint) | Powers the "Gabriel" AI remediation chat |
| Mailtrap | Transactional email (e.g. score-drop alerts) |
| NVD (NIST) | CVE lookups for detected software/versions |
| crt.sh | Certificate Transparency log queries for subdomain discovery/recon |
| GitHub Actions + Claude Code | Automated PR review and `@claude`-triggered assistant |

Explicitly absent: no Vercel/Netlify/Firebase, no SendGrid/Twilio, no analytics or crash reporting.

---

## Cross-project provider index

Providers that show up in more than one project, for quick reference when auditing accounts/billing:

- **Supabase** — Punt & Prominence, Flamboyance, Hound-Up, halosec (each on its own separate project)
- **Resend** — Punt & Prominence, Flamboyance, Hound-Up, HirePotential
- **Azure AI Foundry / Azure OpenAI** — Flamboyance, Hound-Up, HirePotential (three separate Azure resources)
- **Apple App Store Connect API** — Punt & Prominence, Flamboyance, Hound-Up, HirePotential
- **GitHub Pages + GitHub Actions** — Flamboyance (web), Hound-Up (web), HirePotential (web)
- **Stripe** — Punt & Prominence, halosec
- **Mailtrap** — Punt & Prominence (dev only), halosec (production)
- **Railway** — Punt & Prominence, halosec
- **Google Play Console/Integrity/Billing** — Flamboyance, HirePotential

*Last compiled: 2026-09-03.*
