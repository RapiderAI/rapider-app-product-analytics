<!-- Rapider Official App SOW & Specification | App #097: Product Analytics (product-analytics) | Domain: DAT -->

# #097 Product Analytics (`product-analytics`) — Scope of Work (SOW)

- **App Number & Slug:** `#097` / `097-product-analytics`
- **Proposed Rapider App Key:** `product-analytics`
- **Suite / Domain Key:** `DAT` (Data, Analytics & Business Intelligence Suite — `Dat*` Canonical Prefix)
- **Representative Product (`P1`):** Mixpanel
- **Combined Feature Catalog Reference:** [../combined-features/097-product-analytics-features.md](../combined-features/097-product-analytics-features.md)

---

## 1. Executive Outcome & Target Audience

`product-analytics` equips Product Managers, Growth Engineers, UX Researchers, and Customer Success teams with high-velocity digital behavioral telemetry across web, mobile, and API surfaces. While `#096` (`bi-dashboards`) governs cross-departmental tabular KPIs and financial scorecards, `product-analytics` answers sequence-sensitive behavioral questions: *Where exactly do new signups abandon onboarding? Which actions in Week 1 predict 90-day B2B account retention? Which enterprise accounts in `CRM` are showing declining seat engagement ahead of renewal?*

- **SME Fast-Path Defaults:** Startups and SMEs drop in a single `DatTelemetrySource` SDK key (with optional privacy-safe CSS/screen autocapture), immediately unlocking out-of-the-box `DAU/WAU/MAU` stickiness, onboarding funnels, N-day retention matrices, and user path Sankey diagrams without needing a dedicated data engineering team.
- **Enterprise Progressive Disclosure:** Scaling product organizations activate strict Lexicon Tracking Plans (`DatEventPropertySchema`) with unexpected-payload quarantine via Home `WorkItem`s, B2B Group/Account analytics stitched to canonical `CrmAccount` and `FinSubscription` ARR (`Decimal128`), Frequentist/Bayesian A/B experiment impact readouts (`DatExperimentImpactReport`), zero-copy warehouse views in `#099`, and GDPR right-to-erasure pipelines.
- **Measurable Success Outcomes:**
  - Self-serve multi-step funnel, cohort retention, and path queries configured in under 60 seconds without manual SQL joins.
  - 100% deterministic identity stitching from pre-signup anonymous sessions to authenticated users and B2B `CrmAccount` groups.
  - Zero unconsented PII ingestion via ingestion-edge property scrubbing and schema enforcement.
- **Top 6 Global Benchmarks Synthesized:** `P1` Mixpanel, `P2` Amplitude, `P3` PostHog, `P4` Pendo, `P5` Heap (Contentsquare), and `P6` Zoho Apptics & PageSense.

---

## 2. In-Scope, Out-of-Scope & Suite Ownership Boundaries

### In-Scope
1. Application telemetry sources (`DatTelemetrySource`), governed event/property tracking plans (`DatEventPropertySchema`), virtual merged/autocaptured event rules, and append-only event ingestion (`DatTrackedEvent`).
2. Anonymous-to-authenticated user identity resolution and B2B account-level rollup linked to `CrmAccount` and `CrmContact`.
3. Multi-step conversion funnels (`DatFunnelDefinition`) with strict/any-order sequences, conversion windows, property-constant constraints, time-to-convert distributions, and UI friction signals.
4. Cohort retention matrices and lifecycle stickiness (`DatRetentionCohort`: N-day, unbounded, `New/Current/Resurrected/Dormant`), plus forward/reverse Sankey/Sunburst user pathing (`DatUserPathAnalysis`).
5. Dynamic behavioral cohorts (`DatBehavioralSegment`) with real-time activation events to `MKT`, `CRM`, and `SUP`, plus experiment impact evaluation (`DatExperimentImpactReport`).
6. Consent-gated ingestion, PII scrubbing, GDPR/CCPA subject erasure, and grounded conversational product querying over `projectChannelMessage`.

### Out-of-Scope
1. Executive multi-entity BI financial scorecards, paginated reports, and general SQL semantic modeling — owned by `#096 Business Intelligence & Dashboards` (`bi-dashboards`).
2. Email/SMS/push marketing campaign delivery and in-app survey authoring — owned by `MKT` (`campaigns`, `email`, `sms`, `surveys`), which consume `DatBehavioralSegment` events.
3. Cloud SQL warehouse compute sizing and general ELT third-party connectors — owned by `#099` (`data-warehouse`) and `#098` (`data-integration`).
4. Customer support ticketing queues and SLA timers — owned by `SUP` (`support`).

### Cross-Suite Ownership & Integration Contract

| Domain / App | Owns (Single Source of Truth) | Integration Contract with `product-analytics` (`#097`) |
| :--- | :--- | :--- |
| **`product-analytics` (`#097`)** | `DatTelemetrySource`, `DatEventPropertySchema`, `DatTrackedEvent`, `DatFunnelDefinition`, `DatRetentionCohort`, `DatUserPathAnalysis`, `DatBehavioralSegment`, `DatExperimentImpactReport` | Publishes cohort membership deltas, product engagement scores, and funnel anomaly events. |
| **`CRM` (`crm`) & `FIN` (`subscriptions`)** | `CrmAccount`, `CrmContact`, `FinSubscription` | Read-only linkage for B2B account-level adoption rollups and `Decimal128` ARR-weighted retention; pushes read-only account engagement health signals via Kafka. |
| **`MKT` (`campaigns`, `surveys`) & `SUP` (`support`)** | `MktCampaign`, `MktSurvey`, `SupTicket` | Consumes `dat.product.segment.membership_changed.v1` to trigger targeted nudges/surveys; `SUP` links `SupTicket` to masked user session timelines for rapid triage. |
| **`#096` BI & `#099` Warehouse & `#100` Governance** | `DatDashboard`, `DatWarehouseTable`, `DatClassificationTag` | Exposes zero-copy aggregated behavioral views to `#099`/`#096`; synchronizes PII property masking with `#100`. |
| **`#102` CI/CD (`ci-cd`)** | Pipeline deployment events | Consumes `dev.cicd.deployment.succeeded.v1` to plot release markers directly on funnel and retention timelines (`PAN-027`). |

---

## 3. Primary Web Pages & Sidebar Navigation Architecture (12 Pages)

Every visible sidebar section strictly contains **`>= 3` visible items (`displayOnMenu: true`)**, while detail and drawer editors set `displayOnMenu: false`.

| # | Route | Page Title | Sidebar Section | Menu Visible | Key Components & UX |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `product-overview` | Engagement Pulse Hub | `Product Insights` | `true` (Order 1) | `DAU/WAU/MAU` stickiness ribbon, Product Engagement Score (PES), release marker timeline, `#activeFilterTagsBar`, `"Ask Product Analytics"` launcher. |
| 2 | `funnels` | Conversion Funnels | `Product Insights` | `true` (Order 2) | `findSWR` directory of `DatFunnelDefinition`s with conversion `%`, median time-to-convert, clickable funnel name, icon+text `Edit`, icon-only `Delete`. |
| 3 | `retention-cohorts` | Retention & Lifecycle | `Product Insights` | `true` (Order 3) | Triangular N-day/unbounded cohort heat-map matrix (`DatRetentionCohort`), `New/Current/Resurrected/Dormant` lifecycle bars, ARR-weighted toggle. |
| 4 | `user-paths` | User Flows & Journeys | `Product Insights` | `true` (Order 4) | Interactive Sankey/Sunburst `DatUserPathAnalysis` explorer showing top converting and drop-off paths N steps after entry or before churn/support events. |
| 5 | `behavioral-segments` | Behavioral Cohorts | `Cohorts & Experiments` | `true` (Order 5) | Dynamic `DatBehavioralSegment` catalog with live user/account counts, `CrmAccount` health breakdown, and `MKT`/`CRM` sync status pills. |
| 6 | `feature-adoption` | Feature Adoption Matrix | `Cohorts & Experiments` | `true` (Order 6) | 2x2 Breadth-vs.-Frequency quadrant scatter chart across governed Core Events, sliceable by `FinSubscription` plan tier. |
| 7 | `experiment-impact` | Experiment Readouts | `Cohorts & Experiments` | `true` (Order 7) | `DatExperimentImpactReport` scorecard table showing lift `%`, confidence intervals, SRM health check, and guardrail error metrics. |
| 8 | `event-lexicon` | Event Lexicon & Schema | `Data & Governance` | `true` (Order 8) | `DatEventPropertySchema` tracking plan registry with `APPROVED / UNEXPECTED / DEPRECATED` filter pills, `#activeFilterTagsBar`, PII badges, and merge action. |
| 9 | `live-event-stream` | Live Telemetry Debugger | `Data & Governance` | `true` (Order 9) | Real-time streaming inspector of incoming `DatTrackedEvent` payloads, schema validation pass/quarantine badges, and masked property preview. |
| 10 | `telemetry-sources` | SDK Sources & Privacy | `Data & Governance` | `true` (Order 10) | `DatTelemetrySource` key management, bot/IP exclusion lists, consent-gate rules, and GDPR subject erasure job console. |
| 11 | `funnel-detail` | Funnel Studio & Diagnostics | *(Hidden)* | `false` | Interactive step conversion canvas (`#/{funnel-detail}?id=...`), property breakdown table, friction/rage-click overlay, `"Save Drop-Off Cohort"`, and `Back to Funnels`. |
| 12 | `segment-funnel-drawer` | Funnel & Cohort Builder Drawer | *(Hidden)* | `false` | Sliding builder drawer for `DatFunnelDefinition`, `DatBehavioralSegment`, and `DatEventPropertySchema` edits; enforces `finally` button reset and `closePageDrawer()`. |

---

## 4. Canonical Data Models (`Dat*` — 8 Models)

All models omit `projectId`, use platform UUID `id` and optimistic `version`, store monetary metrics as `Decimal128`, and use `00000000-xxxx-xxxx-xxxx-xxxxxxxxxxxx` seed UUIDs.

| Model Name | Key Fields & Types | Relationships & External Refs | Invariants & Lifecycle |
| :--- | :--- | :--- | :--- |
| **`DatTelemetrySource`** | `id: UUID`, `sourceKey: string`, `name: string`, `platformType: enum(WEB, IOS, ANDROID, BACKEND_API)`, `writeKeyHash: string`, `autocaptureEnabled: boolean`, `consentMode: enum(STRICT_OPT_IN, OPT_OUT_ALLOWED)`, `excludedCidrs: string[]`, `monthlyQuotaEvents: int`, `isEnabled: boolean`, `version: int` | Referenced by `DatTrackedEvent.sourceId`. | Raw SDK write keys are shown once at creation and stored strictly as salted cryptographic hashes (`writeKeyHash`). |
| **`DatEventPropertySchema`** | `id: UUID`, `eventName: string`, `displayName: string`, `category: string`, `isCoreFeature: boolean`, `virtualRuleJson: object?`, `propertiesSchemaJson: {name, type, isPii, maskStrategy}[]`, `governanceStatus: enum(APPROVED, UNEXPECTED, QUARANTINED, DEPRECATED, BLOCKED)`, `ownerUserId: UUID?`, `activeWorkItemId: UUID?`, `version: int` | `activeWorkItemId -> Platform WorkItem.id`; syncs PII tags with `#100` `DatClassificationTag`. | `eventName` unique per workspace. `BLOCKED` events are dropped at ingestion edge; `isPii: true` properties enforce hashing/redaction before storage. |
| **`DatTrackedEvent`** | `id: UUID`, `sourceId: UUID`, `eventName: string`, `distinctUserId: string`, `anonymousId: string`, `crmAccountId: UUID?`, `crmContactId: UUID?`, `occurredAt: datetime`, `ingestedAt: datetime`, `propertiesJson: object`, `revenueAmount: Decimal128?`, `currencyCode: string?`, `frictionFlags: string[]`, `idempotencyKey: string` | `sourceId -> DatTelemetrySource.id`; optional `crmAccountId -> CrmAccount.id`, `crmContactId -> CrmContact.id`. | Append-only immutable telemetry record (except statutory GDPR subject erasure tombstoning). Deduplicated on `(sourceId, idempotencyKey)`. `revenueAmount` uses `Decimal128`. |
| **`DatFunnelDefinition`** | `id: UUID`, `name: string`, `description: string`, `stepsJson: {order, eventName, filterJson}[]`, `orderingMode: enum(STRICT, SEQUENTIAL, ANY_ORDER)`, `conversionWindowSeconds: int`, `holdConstantProperty: string?`, `breakdownProperty: string?`, `anomalyAlertEnabled: boolean`, `discussionChannelId: UUID?`, `ownerUserId: UUID`, `version: int` | References `DatEventPropertySchema.eventName`; `discussionChannelId -> projectChannelMessage`. | Must contain `>= 2` steps and `conversionWindowSeconds > 0`. |
| **`DatRetentionCohort`** | `id: UUID`, `name: string`, `anchorEventName: string`, `returnEventName: string`, `retentionMode: enum(N_DAY, UNBOUNDED, BRACKET)`, `intervalUnit: enum(DAY, WEEK, MONTH)`, `periodCount: int`, `weightByArr: boolean`, `segmentId: UUID?`, `ownerUserId: UUID`, `version: int` | Optional `segmentId -> DatBehavioralSegment.id`; when `weightByArr: true`, joins `FinSubscription` (`Decimal128`). | `periodCount` bounded `1..36` intervals for deterministic interactive rendering. |
| **`DatUserPathAnalysis`** | `id: UUID`, `name: string`, `direction: enum(FORWARD_FROM_START, REVERSE_BEFORE_END)`, `anchorEventName: string`, `maxSteps: int`, `excludedEvents: string[]`, `sessionTimeoutMinutes: int`, `ownerUserId: UUID`, `version: int` | References `DatEventPropertySchema.eventName`. | `maxSteps` bounded `2..10` to prevent Sankey combinatorial explosion. |
| **`DatBehavioralSegment`** | `id: UUID`, `code: string`, `name: string`, `entityScope: enum(USER, B2B_ACCOUNT)`, `criteriaAstJson: object`, `memberCount: int`, `engagementScoreAvg: Decimal128?`, `syncDestinations: enum(MKT, CRM, SUP, WAREHOUSE)[]`, `lastComputedAt: datetime?`, `isDynamic: boolean`, `version: int` | Emits membership deltas referencing `CrmContact.id` / `CrmAccount.id`. | `code` unique. Dynamic segments recompute incrementally and emit idempotent membership delta events. |
| **`DatExperimentImpactReport`** | `id: UUID`, `experimentKey: string`, `name: string`, `exposureEventName: string`, `variantProperty: string`, `primaryMetricFunnelId: UUID?`, `guardrailEvents: string[]`, `confidenceLevel: Decimal128`, `srmPassed: boolean`, `status: enum(RUNNING, CONCLUDED, ARCHIVED)`, `summaryJson: object`, `version: int` | `primaryMetricFunnelId -> DatFunnelDefinition.id`. | Flags invalid conclusions automatically whenever Sample Ratio Mismatch (`srmPassed === false`) is detected. |

---

## 5. Core End-to-End User Journeys (5 Journeys)

1. **Journey 1 — Onboarding Funnel Diagnosis & One-Click Drop-Off Cohort Activation:**
   - A Growth PM opens `funnels`, filters by `Category: "Onboarding"` (`#activeFilterTagsBar` displays `Category: "Onboarding" ✕`), and clicks the primary identifier `"Workspace Signup -> First App Deployed"` to open `#/funnel-detail?id=...`.
   - Step 3 (`Connect Data Source`) shows a `42%` drop-off correlated with `API_TIMEOUT` friction flags (`PAN-010`).
   - The PM clicks the dropped-off bar -> `"Save as Behavioral Cohort"` in `segment-funnel-drawer`, enables `syncDestinations: ['MKT', 'SUP']`, saves (drawer auto-closes via `closePageDrawer()`), and `MKT` immediately receives `dat.product.segment.membership_changed.v1` to send a guided setup email.
2. **Journey 2 — Unexpected Telemetry Quarantine & Steward Governance via Home `WorkItem`:**
   - A mobile release begins emitting an undeclared event `checkout_v2_tap` with an unapproved `rawEmail` property.
   - Ingestion edge scrubs `rawEmail`, marks `checkout_v2_tap` as `UNEXPECTED` in `DatEventPropertySchema`, and opens a Rapider Home `WorkItem` for the Product Data Steward.
   - Navigating from the Home `WorkItem` to `event-lexicon`, the Steward maps `checkout_v2_tap` as a virtual alias of `Checkout Initiated` (`APPROVED`) and marks `rawEmail` as `BLOCKED`, resolving the `WorkItem`.
3. **Journey 3 — B2B ARR-Weighted Retention & CRM Renewal Risk Handoff:**
   - A VP of Product opens `retention-cohorts`, toggles `"Weight by Subscription ARR"` (`PAN-016`), and inspects 12-week account retention joined live with `FinSubscription` (`Decimal128`) and `CrmAccount`.
   - Identifying enterprise accounts whose weekly Core Event frequency dropped `> 50%`, the VP creates a `B2B_ACCOUNT` `DatBehavioralSegment` (`"Enterprise Renewal At-Risk"`), publishing engagement health flags directly onto the `CRM` account timeline.
4. **Journey 4 — Reverse Pathfinding Before Support Escalation:**
   - A UX Lead opens `user-paths`, selects `REVERSE_BEFORE_END` anchored on `Support Ticket Submitted` (`SupTicket`), and discovers `31%` of tickets follow `Billing Settings -> Export Invoice PDF -> Error 502`.
   - Clicking `"Back"` from any drilled cohort view restores filter chips cleanly without losing state.
5. **Journey 5 — Grounded "Ask Product Analytics" & Release Marker Correlation:**
   - Inside `product-overview`, a PM asks in the `projectChannelMessage` AI drawer: *"Did iOS checkout conversion change after yesterday's v4.8 CI/CD deploy?"*
   - The AI correlates the `#102` `dev.cicd.deployment.succeeded.v1` marker against the governed `Checkout Funnel`, returning a before/after conversion comparison citing exact `DatFunnelDefinition` and `DatEventPropertySchema` IDs.

---

## 6. Consolidated Feature Priorities & Work Packages (`P0`, `P1`, `P2`)

### `P0` Foundation Work Packages
- **`WP-P0-1` Telemetry Ingestion, Identity Merge & Lexicon Core (`PAN-001`, `PAN-002`, `PAN-003`, `PAN-024`):** Build `event-lexicon`, `live-event-stream`, and `telemetry-sources` with consent-gated ingestion, PII scrubbing, and `anonymousId -> userId -> CrmAccount` stitching (`Product Engineer` / `Data Steward`).
- **`WP-P0-2` Multi-Step Funnels & Drop-Off Cohorts (`PAN-007`, `PAN-008`, `PAN-009`):** Build `funnels`, `funnel-detail`, and `segment-funnel-drawer` with strict/any-order conversion windows, property breakdowns, and one-click drop-off cohort creation (`Product Manager`).
- **`WP-P0-3` Retention Matrices, Lifecycle Stickiness & Pathing (`PAN-012`, `PAN-013`, `PAN-014`):** Build `product-overview`, `retention-cohorts`, and `user-paths` for N-day/unbounded heat-maps, `DAU/WAU/MAU` lifecycle accounting, and forward/reverse Sankey pathing (`Product Manager` / `UX Researcher`).
- **`WP-P0-4` Behavioral Cohorts & Cross-Suite Sync (`PAN-018`, `PAN-019`):** Build `behavioral-segments` with live membership computation and Kafka event publishing to `MKT`, `CRM`, and `SUP` (`Growth PM`).

### `P1` Scale & Enterprise Governance Work Packages
- **`WP-P1-1` Schema Quarantine, Virtual Events & Source Quotas (`PAN-004`, `PAN-005`, `PAN-006`, `PAN-025`):** Home `WorkItem` quarantine reviews, virtual merged events, CIDR/bot exclusion, and GDPR subject erasure (`Data Steward` / `Privacy Admin`).
- **`WP-P1-2` UI Friction Signals, PES Quadrant, ARR Retention & Experiments (`PAN-010`, `PAN-015`, `PAN-016`, `PAN-020`, `PAN-021`, `PAN-022`):** Rage-click/error correlation, `feature-adoption` 2x2 PES quadrant, `FinSubscription` `Decimal128` ARR-weighted retention, `experiment-impact` SRM/lift readouts, `#099` zero-copy sync, and anomaly `WorkItem` alerts (`Product Manager` / `Data Scientist`).

### `P2` Adjacent & Grounded AI Work Packages
- **`WP-P2-1` Conversion Drivers, Masked Session Timelines & Churn Propensity (`PAN-011`, `PAN-017`, `PAN-023`):** Automated statistical conversion driver ranking, PII-masked session event timelines, and B2B churn/expansion propensity scoring (`Growth PM` / `CSM`).
- **`WP-P2-2` Grounded Product AI, CI/CD Release Impact & Threads (`PAN-026`, `PAN-027`, `PAN-028`):** Conversational funnel/retention builder over `projectChannelMessage`, `#102` release impact digests, and collaborative chart annotations (`All Product Roles`).

---

## 7. Roles, Permissions & Field-Level Governance

| Role | Permitted Actions | Restricted / Field-Level Boundaries |
| :--- | :--- | :--- |
| **`Product Viewer`** | View `product-overview`, shared `funnels`, `retention-cohorts`, `user-paths`, and `feature-adoption`; apply filters and post `projectChannelMessage` comments. | Cannot view individual `distinctUserId` event streams, export raw user cohort lists, or edit schemas/funnels. |
| **`Product Manager / Analyst`** | Create/edit `DatFunnelDefinition`, `DatRetentionCohort`, `DatUserPathAnalysis`, `DatBehavioralSegment`, and `DatExperimentImpactReport`; use grounded AI. | Cannot unmask PII properties, rotate SDK write keys, or execute GDPR erasure commands. |
| **`Growth / Marketing Activator`** | Configure `syncDestinations` on approved `DatBehavioralSegment`s for `MKT` and `CRM` activation. | Cannot modify `DatEventPropertySchema` PII rules or ingest raw telemetry schemas. |
| **`Product Data Steward`** | Approve/quarantine/block `DatEventPropertySchema` entries via Home `WorkItem`s, define virtual merged events, manage PII masking rules. | Cannot view unhashed SDK keys or bypass immutable audit logs. |
| **`Telemetry & Privacy Admin`** | Manage `DatTelemetrySource` keys, quotas, consent modes, and execute `dat.product.eraseSubjectTelemetry` GDPR/CCPA requests. | Every key rotation and subject erasure requires guarded command confirmation and writes a permanent compliance log. |

---

## 8. Technical APIs, Guarded Commands, Events & `requiredWorkflows`

### Guarded Commands
1. `dat.product.ingestEventBatch({ sourceKey, events, idempotencyKey, correlationId })` — Validates write key, enforces consent & PII scrubbing against `DatEventPropertySchema`, deduplicates on `(sourceId, idempotencyKey)`, and appends `DatTrackedEvent`s.
2. `dat.product.mergeIdentity({ anonymousId, distinctUserId, crmContactId, crmAccountId, idempotencyKey, correlationId })` — Deterministically links pre-auth anonymous events to known user and B2B account graphs.
3. `dat.product.governEventSchema({ schemaId, targetStatus, propertiesSchemaJson, expectedVersion, idempotencyKey, correlationId })` — Updates lexicon governance status and resolves quarantine `WorkItem`s.
4. `dat.product.recomputeSegment({ segmentId, expectedVersion, idempotencyKey, correlationId })` — Evaluates `DatBehavioralSegment` AST and emits membership delta events.
5. `dat.product.eraseSubjectTelemetry({ subjectIdentifier, regulationType, idempotencyKey, correlationId })` — Executes irreversible GDPR/CCPA erasure + suppression tombstoning (requires explicit human confirmation).

### Domain Events Published & Consumed (Kafka)
- **Published:** `dat.product.event.quarantined.v1`, `dat.product.segment.membership_changed.v1`, `dat.product.account_engagement.scored.v1`, `dat.product.funnel_anomaly.detected.v1`, `dat.product.subject.erased.v1`.
- **Consumed:** `dev.cicd.deployment.succeeded.v1` (from `#102` for release markers), `crm.account.updated.v1` (from `CRM`), `fin.subscription.changed.v1` (from `FIN` for `Decimal128` ARR cohort weighting).

### `requiredWorkflows`
- `productSchemaQuarantineWorkflow` — Opens/resolves Steward Home `WorkItem`s on unexpected event/property detection.
- `productCohortSyncWorkflow` — Incremental `DatBehavioralSegment` evaluation and downstream `MKT`/`CRM`/`SUP` event dispatch.
- `productFunnelAnomalyWorkflow` — Scheduled control-band check on critical funnels creating PM Home `WorkItem`s.
- `productSubjectErasureWorkflow` — Verifiable multi-partition GDPR/CCPA telemetry purge and audit certificate generation.

### Grounded Rapider AI Governance Rules
- `"Ask Product Analytics"` MUST map user prompts exclusively onto `APPROVED` `DatEventPropertySchema` event/property names — never fabricating uninstrumented events — and MUST require human confirmation before saving a cohort sync to `MKT` or `CRM`.

---

## 9. Security, Privacy, Compliance & Non-Functional Requirements

- **Privacy-by-Design (GDPR, CCPA, HIPAA-Safe Mode, SOC 2):** Password inputs, payment card fields, and properties tagged `isPii: true` are scrubbed prior to disk persistence; IP addresses are geo-resolved in memory and discarded when `STRICT_OPT_IN` or IP-anonymization is enabled.
- **Right-to-Erasure SLA:** Subject erasure completes across raw event partitions and behavioral segments within `<= 72 hours` while maintaining a cryptographic suppression hash so delayed SDK retries cannot re-ingest erased profiles.
- **High-Throughput & Precision:** `Decimal128` enforced on all `revenueAmount` and ARR-weighted retention metrics (e.g., `\$49,900.00` enterprise tier renewals); zero `projectId` columns on app models.
- **Accessibility & Responsive UX:** WCAG 2.2 AA accessible tabular fallbacks for Sankey/cohort heat-map visuals, `#ffffff !important` white text on all primary/danger buttons, and responsive layouts across `390px`, `768px`, and `1440px`.

---

## 10. Deterministic Acceptance Criteria (13 Testable Criteria)

1. **AC-01 (Sidebar Minimum-3 Rule):** Visible sidebar sections (`Product Insights`: 4 items, `Cohorts & Experiments`: 3 items, `Data & Governance`: 3 items) each contain `>= 3` `displayOnMenu: true` pages; `funnel-detail` and `segment-funnel-drawer` set `displayOnMenu: false`.
2. **AC-02 (SWR Clean Queries & Defensive Soft-Delete):** Every page mount uses `findSWR`/`countSWR` with zero client `isDeleted` filter predicates and filters `item.isDeleted !== true` in memory.
3. **AC-03 (Table Actions & Real Click-Through):** Zero `"View"` buttons appear in `Actions` columns; clicking a funnel name on `funnels` physically navigates to `#/funnel-detail?id=...` without `<base href>` 404 traps, `Edit` renders icon + `"Edit"` text, and `Delete` renders an icon-only button with `#rapiderConfirmOkBtn` modal confirmation.
4. **AC-04 (Active Filter Tags & Clear All):** Typing in search or toggling `APPROVED / UNEXPECTED` pills on `event-lexicon` or `funnels` renders `#activeFilterTagsBar` with HTML-escaped dismissible chips and `[Clear all]`, synchronously updating list counts.
5. **AC-05 (Drawer Lifecycle & Optimistic State):** Creating or updating a funnel or cohort in `segment-funnel-drawer` resets the submit button inside `finally`, clears SWR caches, updates parent lists optimistically, and closes the drawer via `closePageDrawer()`.
6. **AC-06 (Idempotent Ingestion & PII Scrubbing):** Re-submitting `dat.product.ingestEventBatch` with an identical `(sourceId, idempotencyKey)` creates zero duplicate `DatTrackedEvent`s, and any property marked `isPii: true` in `DatEventPropertySchema` is redacted before storage.
7. **AC-07 (Deterministic Identity Stitching):** Calling `dat.product.mergeIdentity` links prior `anonymousId` events to `distinctUserId` and `crmAccountId` so multi-step funnels spanning pre-signup landing views and post-login actions compute accurate end-to-end conversion.
8. **AC-08 (`Decimal128` ARR-Weighted Retention):** Enabling `weightByArr: true` on `DatRetentionCohort` aggregates `FinSubscription` values using exact `Decimal128` arithmetic without floating-point precision loss.
9. **AC-09 (Unexpected Schema Quarantine `WorkItem`):** Ingesting an undeclared event name automatically creates an `UNEXPECTED` `DatEventPropertySchema` entry and opens a single Rapider Home `WorkItem` for Steward review.
10. **AC-10 (Sample Ratio Mismatch Guardrail):** When an experiment's traffic split deviates beyond the chi-square SRM p-value threshold (`< 0.001`), `DatExperimentImpactReport.srmPassed` evaluates to `false` and displays a prominent warning banner blocking winner declaration.
11. **AC-11 (Cross-Suite Cohort Event Emission):** Saving a `DatBehavioralSegment` with `syncDestinations: ['MKT', 'CRM']` emits `dat.product.segment.membership_changed.v1` referencing canonical `CrmContact` / `CrmAccount` UUIDs.
12. **AC-12 (Back Navigation Filter Restoration):** Clicking `#btnGoBack` from `funnel-detail` returns to `funnels` with all prior search/category filter tags intact.
13. **AC-13 (Theme & Contrast Compliance):** All 12 pages pass automated dark/light `validate_theme` checks with `#ffffff !important` text on primary and danger buttons and zero `window.alert`/`confirm`/`prompt` usage.
