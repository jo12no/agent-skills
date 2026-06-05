---
name: gcp-project-refresher
description: Review a Google Cloud project with read-only gcloud commands and produce a practical 101-style overview of what is running. Use when the user asks to understand, audit, inventory, check, provide an overview or refresh themselves on a GCP project.
---

# GCP Project Refresher

## Goal

Produce a practical "what is this project doing?" briefing from read-only `gcloud` inventory. Prefer concrete resource evidence over guesses. Label use-case descriptions as inferred when they come from names, schedules, logs, buckets, tables, or triggers rather than source code.

## Prerequisites

Require the `gcloud` CLI. Direct command execution requires authenticated Google Cloud access, network access to Google Cloud APIs, and permission to run read-only inventory, logging, and billing commands.

## Inputs

Require a GCP project ID. If the user gives `project=...`, use that. Otherwise inspect `gcloud config list --format=json` when possible, then ask only if the project is still ambiguous.

Use `gcloud` by default when the environment can run it. If live calls fail because auth, network, permissions, or the execution environment blocks access, report the failed command and the reason. Ask for the minimum approval, setup, or user-provided command output needed to continue. Do not fabricate inventory when command output is unavailable.

## Workflow

1. Confirm context:
   - active account and project
   - `gcloud projects describe`
   - billing linkage with `gcloud billing projects describe`
   - enabled APIs with `gcloud services list --enabled`

2. Inventory actual resources, not just enabled APIs:
   - Cloud Run services
   - Cloud Functions v1/v2
   - App Engine app/services
   - Cloud Scheduler jobs across likely regions
   - Pub/Sub topics/subscriptions
   - Cloud Storage buckets and important bucket sizes
   - Artifact Registry repositories
   - BigQuery datasets/tables via Cloud Asset inventory if direct bq commands are unavailable
   - Compute instances, disks, addresses
   - GKE, Dataproc, Cloud SQL, Firestore/Datastore, Secret Manager where APIs are enabled or worth ruling out
   - recent Cloud Build history and triggers
   - a small recent log sample for active Cloud Run/Functions paths

3. Infer use cases from evidence:
   - Resource names: `daily-report-exporter` likely exports a report on a schedule; `status-alert-checker` likely checks an external status feed and sends or records alerts.
   - Triggers/schedules: cron, Pub/Sub topics, HTTP targets, OIDC audiences, paused/enabled state.
   - Logs: phrases like "Saving to GCS", "found total jobs", "No new alerts found".
   - Data links: buckets, BigQuery datasets/tables, service accounts, source buckets.
   - Deployment metadata: runtimes, regions, source locations, build tags.

   Phrase these as "likely", "appears to", or "logs confirm" depending on confidence. Do not claim source-code behavior unless source was inspected.

4. Separate live from leftover:
   - Live: enabled schedules, recent successful logs, ready Cloud Run revisions, active functions with recent calls.
   - Dormant: paused schedulers, stopped App Engine versions, terminated VMs, old source/artifact buckets.
   - Cost residue: stopped VM disks, Artifact Registry images, large buckets, BigQuery tables, enabled but unused services.

5. Present a compact briefing:
   - project identity and billing
   - main mental model in one paragraph
   - active workloads with use-case descriptions
   - schedules and triggers
   - data/storage/messaging
   - billing/cost clues
   - inactive resources and cleanup candidates
   - limits of the review

Use [references/recommended-commands.md](references/recommended-commands.md) for the detailed checklist, command patterns, and output shape.

## Reporting Rules

- Use absolute dates when discussing recent activity.
- Keep "enabled API" distinct from "resource exists".
- Note when an API is disabled instead of enabling it. Ask to enable it if you think it's necessary.
- Mention commands that could not run and why.
- Avoid pasting secrets, tokens, request payloads, or long log bodies.
- For billing, report billing account linkage and likely cost drivers unless the user has configured billing export or grants access to billing reports.
