# Session 2: Operations & Day-2

90-minute hands-on lab · CTIE platform & security engineers · Standalone (no Session 1 dependency) · Same Showroom environment

---

## Session flow

| # | Exercise | Topic | Time | Status |
|---|----------|-------|------|--------|
| 1 | Vulnerability dashboard & Scanner V4 | Vulnerability Management | 15 min | Ready |
| 2 | Automated vulnerability reports (PDF/CSV) | Vulnerability Management | 15 min | Needs check |
| 3 | Scoped Access Control (SAC) | RBAC & Identity | 15 min | Ready |
| 4 | OIDC group mapping & Console Plugin | RBAC & Identity | 15 min | Blocked |
| 5 | Webhook notifier (generic webhook) | Notifiers & Integrations | 15 min | Needs check |
| 6 | Splunk HEC integration | Notifiers & Integrations | 15 min | Needs check |

**Total: 90 min · 6 exercises**

---

## Topic breakdown

### Vulnerability Management (30 min)

- **Ex 1** — Navigate the vulnerability dashboard, scan an image on demand, understand Scanner V4 Indexer/Matcher architecture, compare base OS vs language-level (Java/Quarkus) CVEs
- **Ex 2** — Create automated scheduled PDF/CSV reports scoped per namespace, replace manual CSV-by-email workflow, generate on-demand report and review output

### RBAC & Identity (30 min)

- **Ex 3** — Create custom Permission Sets, Access Scopes, and Roles to isolate tenant visibility in a shared ACS Central. Discussion: multi-tenant ACS without separate Central instances
- **Ex 4** — Map Keycloak/OIDC group claims to custom ACS roles (e.g. `ctie-payments-devs`), enable the OpenShift Console Plugin for developer self-service vulnerability inspection

### Notifiers & Integrations (30 min)

- **Ex 5** — Configure a generic webhook notifier pointing to an external receiver (webhook.site), attach it to a policy, trigger a violation, verify the payload. Discussion: mapping ACS JSON to OpenITCockpit passive check format
- **Ex 6** — Configure Splunk HEC integration (URL, token, source type), forward policy violation events, verify in Splunk. Discussion: Splunk dashboard patterns for ACS alerts

---

## Exercise details

### Exercise 1: Vulnerability dashboard & Scanner V4 (15 min) — Ready

**Objective:** Navigate the vulnerability dashboard, scan an image on demand, and understand Scanner V4 Indexer/Matcher architecture.

**Tasks:**
1. Navigate Vulnerability Management > Dashboard — review top risky images, most common CVEs, fixable vs unfixable breakdown
2. Drill into a specific image — inspect base OS vs language-level (Java/Quarkus) CVEs
3. Trigger an on-demand image scan via the GUI for a new image tag
4. Review Scanner V4 architecture: Central → Indexer (SBOM) → Matcher (NVD/RHSA correlation)
5. Compare vulnerability counts between base OS layer and application dependencies

**Prerequisites:** Bastion access, workloads pre-provisioned by Showroom

---

### Exercise 2: Automated vulnerability reports (15 min) — Needs check

**Objective:** Replace manual CSV-by-email with scheduled PDF/CSV reports scoped to specific namespaces.

**Tasks:**
1. Navigate Vulnerability Management > Reporting
2. Create a new report configuration: name, description, schedule (daily/weekly)
3. Scope the report to a specific namespace (e.g. policy-lab)
4. Select report format: PDF summary + CSV detail
5. Configure email delivery target (or download on demand)
6. Generate an on-demand report and review the PDF output

**Dependencies:** Email notifier configured (or use download-only mode)

> ⚠️ **Needs check:** Verify Showroom environment has email/SMTP notifier or if we use download-only. Check if report scheduling is available in the deployed RHACS version.

---

### Exercise 3: Scoped Access Control (15 min) — Ready

**Objective:** Create custom Permission Sets and Access Scopes to isolate team views and actions within a shared ACS Central.

**Tasks:**
1. Navigate Platform Configuration > Access Control — review default roles (Admin, Analyst, etc.)
2. Create a custom Access Scope that limits visibility to a single cluster + namespace
3. Create a custom Permission Set with read-only vulnerability data + no policy write access
4. Create a custom Role combining the new Permission Set + Access Scope
5. Discuss: how SAC enables multi-tenant ACS without separate Central instances

**Prerequisites:** Admin access to ACS Central

---

### Exercise 4: OIDC group mapping & Console Plugin (15 min) — Blocked

**Objective:** Map Keycloak/OIDC groups to ACS roles and enable the OpenShift Console Plugin for developer self-service.

**Tasks:**
1. Review existing auth providers in Platform Configuration > Access Control > Auth Providers
2. Add or inspect OIDC (Keycloak) auth provider configuration
3. Map Keycloak group claims to the custom role created in Exercise 3
4. Enable the OpenShift Console Plugin via Platform Configuration > Clusters > Manage
5. Demonstrate developer self-service: view vulnerabilities from within the OpenShift Console

**Dependencies:** Keycloak/OIDC provider configured and reachable from ACS Central

> 🔴 **Blocked:** Need to verify Keycloak availability in Showroom. Fallback: skip OIDC mapping, focus on Console Plugin only. Console Plugin enablement is independent of OIDC.

---

### Exercise 5: Webhook notifier (15 min) — Needs check

**Objective:** Configure a generic webhook notifier to forward policy violations to an external receiver (OpenITCockpit/Nagios pattern).

**Tasks:**
1. Navigate Platform Configuration > Integrations > Notifiers
2. Create a Generic Webhook notifier pointing to an external receiver (e.g. webhook.site)
3. Configure headers, TLS settings, and audit logging toggle
4. Attach the webhook to an existing policy (e.g. a deploy-enforcement policy)
5. Trigger a violation and verify the webhook payload arrives at the receiver
6. Discuss: mapping ACS JSON payload to OpenITCockpit passive check format

**Dependencies:** External webhook receiver (webhook.site or similar)

> ⚠️ **Needs check:** Verify outbound HTTPS from Showroom cluster is not blocked. OpenITCockpit mapping is discussion-only (no live instance).

---

### Exercise 6: Splunk integration (15 min) — Needs check

**Objective:** Forward policy violation alerts to Splunk for SIEM correlation and operational dashboards.

**Tasks:**
1. Navigate Platform Configuration > Integrations > Notifiers > Splunk
2. Configure the Splunk HEC (HTTP Event Collector) integration: URL, token, source type
3. Select which alert types to forward (policy violations, audit events)
4. Attach the Splunk notifier to a critical policy
5. Trigger a violation and verify the event appears in Splunk
6. Discuss: Splunk dashboard patterns for ACS alerts (sourcetype=stackrox)

**Dependencies:** Splunk instance with HEC endpoint and token

> ⚠️ **Needs check:** Splunk is deployed per user. Need to locate the HEC URL and token in the Showroom environment.

---

## Open questions

| Impact | Area | Question |
|--------|------|----------|
| **HIGH** | Exercise 4 | Is Keycloak deployed and reachable in the Showroom environment? If not, Exercise 4 falls back to Console Plugin only (~10 min shorter). |
| **HIGH** | Exercise 6 | Need Splunk HEC endpoint URL and token from the Showroom environment. Without it, Exercise 6 cannot proceed hands-on. |
| MEDIUM | Exercise 5 | Can the Showroom cluster reach webhook.site (outbound HTTPS 443)? If blocked, we need an in-cluster receiver pod instead. |
| MEDIUM | Exercise 2 | Verify that vulnerability reporting (scheduled PDF/CSV) is available in the deployed RHACS version. GA since 4.3. |
| LOW | Exercise 4 | Verify the ACS Console Plugin is installable on the Showroom OpenShift version (requires OCP 4.12+). |

---

## Antora file plan

| File | Type | Description |
|------|------|-------------|
| `pages/operations-day2.adoc` | Overview | Session 2 overview page (mirrors policy-engine.adoc) |
| `pages/s2-01-prerequisites.adoc` | Prereqs | Bastion access, CLI verify, git clone (standalone) |
| `pages/s2-02-vuln-dashboard.adoc` | Exercise | Vulnerability dashboard & Scanner V4 deep-dive |
| `pages/s2-03-vuln-reports.adoc` | Exercise | Automated vulnerability reports (PDF/CSV) |
| `pages/s2-04-sac.adoc` | Exercise | Scoped Access Control: Permission Sets, Access Scopes, Roles |
| `pages/s2-05-oidc-plugin.adoc` | Exercise | OIDC group mapping & Console Plugin |
| `pages/s2-06-webhook.adoc` | Exercise | Webhook notifier (generic webhook to external receiver) |
| `pages/s2-07-splunk.adoc` | Exercise | Splunk HEC integration for SIEM |
| `pages/s2-08-summary.adoc` | Summary | Session 2 lab summary |
| `nav.adoc` | Update | Add Operations & Day-2 section after Policy Engine |
| `pages/index.adoc` | Update | Add Session 2 row to the Topics table |
