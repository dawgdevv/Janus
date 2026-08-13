# Blastline

Blastline is a graph-native supply-chain incident tool for JavaScript and TypeScript repositories.

It connects arbitrary repositories into a provider-independent security workspace, records dependency snapshots over time, and helps answer:

- Which repositories resolved a compromised package version?
- Is the dependency direct or transitive?
- What dependency path introduced it?
- Which snapshots were affected during the incident window?
- What is the reverse-dependency blast radius?
- Which shared dependency is creating the most exposure across repositories?

## Why

Tools like CVE Lite CLI are strong at analyzing a single repository.

Blastline starts where single-repository scanning stops.

A workspace can contain repositories from anywhere:

```text
Workspace
├── GitHub repo
├── GitLab repo
├── local repo
└── private repo
```

Repositories are linked using stable workspace and repository IDs rather than relying on a GitHub Organization.

## How it works

```text
Repository
   ↓
Blastline CLI
   ↓
CVE Lite + Git metadata
   ↓
Immutable Security Snapshot
   ↓
HydraDB
   ↓
Cross-repo / ecosystem graph traversal
   ↓
Incident investigation
```

Blastline uses:

- **CVE Lite CLI** for repository-level dependency, vulnerability, maintenance-risk, and remediation intelligence.
- **Git / CI** to identify repository state and trigger new snapshots.
- **HydraDB** for graph relationships and blast-radius traversal.
- **SQLite** for local application and workspace metadata.

## Core concepts

### Workspace

A logical security boundary identified by a UUID.

A workspace can contain repositories from different providers.

### Repository

A repository gets its own stable Blastline ID.

Its GitHub/GitLab/local location is metadata, not identity.

### Security Snapshot

Each scan records an immutable view of dependency state for a repository at a specific commit and time.

### Incident

A package version can be marked as affected for a known time window.

Blastline then classifies tracked repositories as:

- **Affected**
- **Clean**
- **Unknown**

## Planned CLI

```bash
blastline workspace create engineering

cd ./my-repo
blastline link <workspace-id>

blastline scan

blastline investigate <package>@<version>
```

## MVP scope

- JavaScript / TypeScript repositories
- npm, pnpm, Yarn, and Bun lockfiles through CVE Lite
- provider-independent workspaces
- stable repository identity
- immutable dependency snapshots
- cross-repository package lookup
- dependency path explanation
- manual malicious-package incidents
- incident-window analysis
- reverse dependency traversal in HydraDB
- common exposure root analysis
- cross-repo remediation grouping
- maintenance-risk aggregation

## Out of scope for the first build

- deployment/runtime provenance
- Kubernetes or cloud integrations
- SAST
- secret scanning
- malware sandboxing
- container scanning
- AI chat
- automatic remediation PRs
- PyPI support

## Architecture

```text
apps/
├── web
├── api
├── worker
└── cli

packages/
├── cve-lite
├── git
├── graph
├── npm
├── snapshots
├── incidents
├── remediation
├── db
├── shared
└── types
```

Runtime:

```text
CLI / CI
   ↓
Fastify API
   ↓
Worker
   ↓
CVE Lite + Git
   ↓
HydraDB + SQLite
   ↓
Next.js dashboard
```

## Status

Blastline is currently being built for HackHydra Track 2A: Supply Chain Blast Radius.

## License

TBD