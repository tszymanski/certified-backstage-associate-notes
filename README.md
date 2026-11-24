# Certified Backstage Associate (CBA) — Study Guide

Study material for the CNCF/Linux Foundation **Certified Backstage Associate (CBA)** exam.

> **Disclaimer:** Unofficial, personal study notes. Not affiliated with or endorsed by
> the CNCF, the Linux Foundation, or the Backstage project. Contains **no actual exam
> questions** — all practice questions are self-written from public documentation.
>
> **Last checked:** November 2026. Exam details (price, format, Node versions,
> project status) change over time — verify against the official sources below.

> Backstage is an open-source framework for building developer portals, created by
> Spotify and donated to the **CNCF** (currently an **incubation**-level project). It is
> built around a centralized **Software Catalog**.

## Exam at a glance

| Item | Detail |
|------|--------|
| Format | Online, **proctored, multiple-choice** |
| Duration | **90 minutes** |
| Level | Beginner / Associate |
| Cost | **$250** standalone ($495 bundled with THRIVE-ONE) |
| Validity | **2 years** |
| Eligibility window | 12 months to schedule & take |
| Retakes | One free retake included |
| Result | Digital badge |

There are **no formal prerequisites**, but a foundation in IT infrastructure, cloud,
DevOps, containers, TypeScript/React and YAML is strongly recommended.

## Exam domains & weights

| # | Domain | Weight | Notes file |
|---|--------|--------|-----------|
| 1 | Backstage **Development Workflow** | **24%** | [01-development-workflow.md](01-development-workflow.md) |
| 2 | Backstage **Infrastructure** | **22%** | [02-infrastructure.md](02-infrastructure.md) |
| 3 | Backstage **Catalog** | **22%** | [03-catalog.md](03-catalog.md) |
| 4 | **Customizing** Backstage | **32%** | [04-customizing.md](04-customizing.md) |

Customizing (plugins/frontend/backend) is the largest domain — prioritize it.

Each domain file opens with the **official curriculum objectives** (as checkboxes)
taken verbatim from the CNCF CBA curriculum, so your study maps 1:1 to the exam.

## Contents

- [01 — Development Workflow](01-development-workflow.md) — create-app, yarn, TypeScript, Docker
- [02 — Infrastructure](02-infrastructure.md) — architecture, config, databases, deployment
- [03 — Catalog](03-catalog.md) — entities, `catalog-info.yaml`, annotations, ingestion
- [04 — Customizing](04-customizing.md) — plugins, React, Material UI
- [Cheat Sheet](cheatsheet.md) — quick reference for last-minute review
- [Practice Questions](practice-questions.md) — self-test

## Primary sources

- **Official curriculum (PDF):** https://github.com/cncf/curriculum/blob/master/CBA_Curriculum.pdf
- Exam page: https://training.linuxfoundation.org/certification/certified-backstage-associate-cba/
- Backstage repo: https://github.com/backstage/backstage
- Docs: https://backstage.io/docs/

## License

Content licensed under [CC BY 4.0](LICENSE) — free to share and adapt with attribution.
