---
name: consent-compliance
description: Implement granular, logged, withdrawable user consent plus data export and hard-delete (DSAR), for GDPR / DPDP-style compliance. Use for any app that collects personal data.
---

# consent-compliance

A reusable consent + data-rights layer for apps handling personal data.

## Do
1. **Granular consent at signup** — separate, UNTICKED checkboxes per purpose, e.g.
   (a) Terms + Privacy [required], (b) processing of any sensitive data [required, explicit],
   (c) marketing [optional], (d) analytics [optional]. Never bundle purposes together.
2. **Consent log** — record each consent with: user id, purpose, policy version, timestamp.
   Re-prompt when the policy version changes.
3. **Easy withdrawal** — withdrawing a consent is as easy as granting it (a settings toggle).
4. **Data rights (DSAR)** — implement **export** (all of a user's data) and **hard-delete**
   (delete truly wipes all of the user's rows). Prove delete with a test.
5. **Minimise + protect** — collect the least data needed; encrypt in transit and at rest.
6. **Docs** — ship a privacy policy + terms; if relevant, complete app-store data-safety forms.

## Notes
This is a strong default for GDPR (EU), India's DPDP, and CCPA-style rules — building to this bar
covers most consumer privacy regimes. It is not legal advice; have your policies reviewed once.

## Done when
Consents are granular + logged + withdrawable; the delete test wipes all user data; export works.

## Token tactics
Scope to the consent module. Generate policy text once and reference it. Route to a frontier model
— this carries legal exposure.
