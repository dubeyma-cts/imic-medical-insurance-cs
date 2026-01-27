
# IMIC – Architecture Documentation Index

Welcome! This folder hosts all architecture artifacts using a **docs-as-code** approach.

## Navigation
- **Governance** → `00-governance/` (standards, templates, checklists, decision log)
- **Context & Domain** → `10-context/`, `30-domain/`
- **Architecture Views (C4)** → `20-views/`
- **Data** → `40-data/`
- **Security** → `50-security/`
- **Quality & NFRs** → `60-quality/`
- **Integration** → `70-integration/`
- **Infrastructure & Environments** → `80-infra/`
- **Diagrams** → `90-diagrams/`
- **ADRs** → `99-adr/`

## Conventions
- **Format**: Markdown (`.md`), PlantUML (`.puml`), Mermaid (`.mmd`), OpenAPI (`.yaml`)
- **IDs**: Use stable IDs for ADRs: `NNNN-title.md`
- **Reviews**: Architecture PRs require architect approval + security review for changes in `50-security/`
- **Updates**: Any significant structural or technology change must include an ADR
