# Architecture

## Product Purpose

This product provides adult-guided educational practice for basic receptive communication skills. It is for family/adult accounts, not clinics, schools, or clinical service delivery.

The product is not a diagnostic tool, medical device, treatment plan, formal progress-reporting system, or replacement for professional therapy, ABA services, supervision, individualized safety planning, or clinical judgment.

## Product Scope

The initial product supports:
- One adult account.
- One learner profile per adult account.
- Learner nickname only.
- Curated educational activity content.
- Admin-only content management after the curated library foundation exists.
- Simple practice summaries.
- Web subscription monetization at launch.
- Account deletion.
- Progress reset within the service.
- Owner/admin support for accounts, data, and subscriptions.

The initial product does not support:
- Multiple learners per adult account.
- Shared caregiver, clinician, school, or organization access.
- Child login.
- Formal therapy reporting.
- Offline use.
- User image uploads.
- Identifiable child photos.
- Custom adult-created content.

## Approved Platform

Approved launch platform:
- Frontend and backend hosting: Vercel.
- Database: Supabase Postgres.
- Authentication: Supabase Auth.
- Asset storage: Supabase Storage.
- Payments: Stripe subscriptions.

Any additional external service requires explicit approval before implementation.

## Engineering Principles

- Prefer boring, maintainable, well-tested architecture.
- Build a modular monolith first.
- Keep business logic in application/domain code by default.
- Keep the database responsible for integrity, ownership, constraints, and transactions.
- Optimize for a non-technical owner operating the product.
- Design APIs so future iOS and Android apps can recreate the web functionality.

## Data Principles

Collect the minimum data needed:
- Adult account identity.
- Learner nickname.
- Practice sessions.
- Trial attempts.
- Progress summaries.
- Subscription/customer state.
- Support/audit records.

Do not collect:
- Diagnoses.
- Therapy notes.
- Medical records.
- School identifiers.
- Full child names.
- Child account credentials.
- Uploaded child photos.

## Privacy And Security

Adults own accounts and supervise learner use. Children do not authenticate directly.

Server-side code is authoritative for:
- Authorization.
- Trial scoring.
- Progress updates.
- Rewards.
- Subscription access.
- Account deletion.
- Progress reset.

Never trust client-submitted correctness, ownership fields, subscription status, or role claims.

Service-role credentials must never reach the browser. Cookie-authenticated mutations must use appropriate CSRF/origin protection. Sensitive support/admin actions must be audit logged.

## Authentication And Authorization

The initial role model is:
- `adult`: owns one account and one learner profile.
- `admin`: product owner/support role for account, data, subscription, and content support.

No organization model, clinician role, caregiver sharing, school access, or multi-learner household model is included in the initial architecture.

## Payments

Stripe subscriptions are required at launch.

Subscription state must be enforced server-side. Webhook handling must be idempotent. Subscription records must be testable without relying only on Stripe dashboard behavior.

Do not implement iOS or Android in-app purchases until native app plans and platform payment requirements are separately reviewed.

## Content And Assets

Initial content is curated. Adults cannot upload images or create custom content.

Admin-only content management may be added after the curated library foundation. All content must include source metadata, alt text, category, skill eligibility, and review status.

Supabase Storage is approved for curated assets. User-uploaded learner assets are out of scope.

## Activity Engine

Activity definitions, trial generation, prompting rules, scoring rules, and progress calculations must be typed and tested.

The server must be able to regenerate or validate trial scoring from stored session seed, content version, and response payloads.

Prompted correct responses must be distinguishable from independently correct responses.

## Database And Migrations

Use Supabase Postgres migrations from the start.

Use transactions for multi-table mutations such as session completion, progress update, reward grant, account deletion, and progress reset.

Content must be versioned so historical practice data remains interpretable after curated content changes.

## API Design

Core workflows must be available through versioned JSON APIs suitable for web now and native apps later.

Do not hide mobile-needed behavior only in React components or web-only Server Actions.

Document API contracts for:
- Current adult account.
- Learner profile.
- Subscription status.
- Activity session lifecycle.
- Trial submission/session completion.
- Progress summary.
- Progress reset.
- Account deletion.
- Admin support workflows.

## Frontend Design

Adult UI should be calm, clear, and operational.

Learner UI should be simple, focused, touch-friendly, accessible, and robust on tablets.

Do not put scoring, authorization, subscription enforcement, or persistence rules only in client components.

## Accessibility

Target WCAG 2.2 AA.

Learner workflows must support:
- Touch.
- Keyboard interaction where practical.
- Reduced motion.
- Sufficient contrast.
- Clear focus states.
- Non-visual status feedback where practical.
- Adult controls for sound, animation, and prompting.

## Testing Philosophy

All implementation follows TDD.

Minimum required tests:
- Domain unit tests.
- Application use-case tests.
- API integration tests.
- Database migration/repository tests.
- Payment webhook tests.
- Authorization tests.
- E2E tests for critical workflows.
- Accessibility checks for adult and learner UI.

## Owner Operations

The non-technical owner must be able to:
- Find an adult account.
- View account status.
- View/reset progress.
- Help with subscription state.
- Trigger or verify account deletion.
- Manage curated content after admin CMS exists.
- Review support/audit history.

## Definition Of Done

A feature is done only when:
- Tests were written first.
- Tests pass.
- Typecheck, lint, and build pass.
- Security/privacy implications were reviewed.
- Accessibility was checked.
- Documentation was updated.
- Owner support implications are clear.

## Guidance For Codex Sessions

Before coding, read this file, the current task prompt, relevant framework docs, and existing tests.

Do not reintroduce multi-learner, organization, clinician-sharing, offline, image-upload, clinical-reporting, or adult-created-content features unless a new approved architecture decision explicitly adds them.
