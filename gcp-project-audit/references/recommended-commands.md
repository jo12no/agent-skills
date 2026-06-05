# GCP Project Refresher - References
Overview: Provides example commands, layout guidance and workflow info.

## Commands Reference

Use this as a menu of read-only commands for direct execution or for requesting user-provided command output. Run targeted follow-up commands as needed; do not enable APIs unless the user explicitly asks, but make recommendations. This is a reference/starting point only, so run additional commands if it will help provide a better overview.

Core:

```bash
gcloud config list --format=json
gcloud projects describe PROJECT_ID --format=json
gcloud billing projects describe PROJECT_ID --format=json
gcloud services list --enabled --project PROJECT_ID "--format=value(config.name,config.title)"
```

Serverless:

```bash
gcloud run services list --platform=managed --project PROJECT_ID --format=json
gcloud functions list --project PROJECT_ID --format=json
gcloud functions list --project PROJECT_ID --v2 --format=json
gcloud app describe --project PROJECT_ID --format=json
gcloud app services list --project PROJECT_ID --format=json
```

Schedulers and messaging:

```bash
gcloud scheduler jobs list --project PROJECT_ID --location=REGION --format=json
gcloud pubsub topics list --project PROJECT_ID --format=json
gcloud pubsub subscriptions list --project PROJECT_ID --format=json
```

Storage and data:

```bash
gcloud storage buckets list --project PROJECT_ID --format=json
gcloud storage du --summarize --readable-sizes gs://BUCKET
gcloud asset search-all-resources --scope=projects/PROJECT_ID --asset-types=bigquery.googleapis.com/Dataset --format=json
gcloud asset search-all-resources --scope=projects/PROJECT_ID --asset-types=bigquery.googleapis.com/Table --format=json
gcloud datastore indexes list --project PROJECT_ID --format=json
```

Builds, images, compute:

```bash
gcloud builds list --project PROJECT_ID --region=REGION --limit=20 --sort-by=~createTime --format=json
gcloud builds triggers list --project PROJECT_ID --format=json
gcloud artifacts repositories list --project PROJECT_ID --location=all --format=json
gcloud compute instances list --project PROJECT_ID --format=json
gcloud compute disks list --project PROJECT_ID --format=json
gcloud compute addresses list --project PROJECT_ID --format=json
gcloud container clusters list --project PROJECT_ID --format=json
gcloud asset search-all-resources --scope=projects/PROJECT_ID --asset-types=dataproc.googleapis.com/Cluster --format=json
```

Recent activity:

```bash
gcloud logging read "resource.type=cloud_run_revision" --project PROJECT_ID --freshness=2d --limit=30 --format=json
gcloud logging read "resource.type=cloud_function" --project PROJECT_ID --freshness=2d --limit=30 --format=json
```

Use `--quiet` only when avoiding prompts to enable disabled APIs. If a command cannot run in the current environment, record the command, error, and impact on the review.

## Use-Case Inference Guide

For each workload, create a one-line description using this evidence order:

1. Source code, if inspected.
2. Recent logs.
3. Scheduler/Pub/Sub trigger and target.
4. Resource name, labels, service account, datasets, buckets, and source paths.

Confidence labels:

- **Confirmed:** Logs or inspected source show behavior.
- **Likely:** Name plus schedule/data links strongly imply behavior.
- **Unclear:** Resource exists but there is little evidence.
- **Dormant:** Paused/stopped/terminated or no recent activity found.

Example wording:

```text
daily-ingest-worker: Confirmed/Likely. Cloud Run service called daily by Cloud Scheduler; recent logs show it fetching external data and saving results to GCS.
budget-shutdown-handler: Likely dormant. Pub/Sub-triggered function on a budget alert topic; Billing Budgets API is disabled, so verify whether anything still publishes to the topic.
```

## Output Shape

Use this as an example format, but be flexible based on the response, unless the user asks for something different:

```text
Reviewed PROJECT_ID as of DATE.

Project
- id, number, lifecycle, created date
- billing enabled/linkage, currency if available

Main Mental Model
- one paragraph explaining the project in plain English

Active Workloads
- service/function name: region, trigger, runtime, status, use-case description

Schedules And Triggers
- enabled jobs, paused jobs, Pub/Sub topics/subscriptions

Data And Storage
- important buckets with approximate sizes
- BigQuery datasets/tables and what they probably support
- Artifact Registry/source buckets

Billing And Cost Clues
- billing linkage
- likely cost drivers
- cost residue such as disks, images, large buckets, BigQuery storage

Inactive Or Cleanup Candidates
- stopped App Engine, terminated VMs, paused jobs, old buckets/images, disabled APIs

Limits
- commands that failed or were not run
- where conclusions are inferred rather than confirmed
```

## Region Strategy

Start with regions discovered from Cloud Run, Cloud Functions, App Engine, Compute, and bucket locations. Also check common scheduler/build regions:

```text
us-central1
europe-west1
australia-southeast1
us-west1
us-east1
asia-southeast1
```

If a scheduler API is enabled but no jobs appear in those regions, say "none found in checked regions" and name the regions.
