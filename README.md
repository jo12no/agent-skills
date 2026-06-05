# Agent Skills

Skills for agentic reusable, task-specific workflows. 

## Included Skills

### GCP Project Refresher

Path: [`gcp-project-audit/`](gcp-project-audit/)

Use it when you want your agent to understand, audit, inventory, or refresh you on a GCP project.

Speeds up the time it takes to navigate and understand a GCP project. 

Reviews a Google Cloud project with read-only `gcloud` commands and produces a practical "what is running here?" briefing. The skill inventories serverless workloads, schedulers, messaging, storage, data resources, build history, logs, billing linkage, and cleanup candidates.

Example prompt:

```text
Review this GCP project with gcloud and give me a 101 refresher: project=PROJECT_ID
```

## Repository Layout

```text
.
+-- gcp-project-audit/
|   +-- SKILL.md
|   +-- references/
|       +-- recommended-commands.md
+-- .gitignore
+-- README.md
```

## Requirements

- Agent harness (eg. Codex) with local skills support.
- Google Cloud CLI (`gcloud`) for the GCP Project Refresher skill.
- Authenticated Google Cloud access with read-only permissions for the target project when running live inventory commands.

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

This is free software: you are free to redistribute and/or modify it under the terms of the GPL v3. This program comes with ABSOLUTELY NO WARRANTY.