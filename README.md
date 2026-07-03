# Tech Interview Knowledge Base

A curated collection of self-contained HTML study guides covering cloud architecture, system design, DevOps, data engineering, AI/RAG, security, and programming — built as a searchable, single-page web app.

## Live Site

> Hosted on GitHub Pages: `https://<your-username>.github.io/interview-kb/`

## Features

- **Live search** across all guides (press `/` to focus)
- **Category filters** — Cloud & AWS, DevOps, System Design, Data Engineering, AI & RAG, Security, Programming, Interview Prep
- **Dark / Light mode** toggle, persisted across pages via `localStorage`
- **No build step** — every guide is a self-contained HTML file, open any of them directly in a browser

## Structure

```
index.html          # Main landing page with search, filters, and card grid
*.html              # 41 self-contained reference guides
.gitignore          # Excludes personal docs, PDFs, images, and temp files
```

## Categories

| Category | Topics |
|---|---|
| Cloud & AWS | Bedrock, Networking, CloudWatch, EKS, Solution Architecture |
| DevOps | Kubernetes, Terraform, CI/CD, SRE |
| System Design | Distributed systems, Supply chain, Integration patterns, TOGAF |
| Data Engineering | Snowflake, PySpark/EMR, Retail data architecture |
| AI & RAG | RAG production systems, vector stores, retrieval patterns |
| Security | CSPM, Trellix, MITRE ATT&CK |
| Programming | JavaScript, Python, TypeScript, REST APIs, DSA |
| Interview Prep | Engineering Manager, Tech Lead, behavioral questions |

## Usage

Open `index.html` in any browser — no server needed locally. For the hosted version, GitHub Pages serves it automatically.
