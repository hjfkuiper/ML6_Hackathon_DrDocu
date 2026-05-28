# DrDocu — Project Instructions

## What is DrDocu?

DrDocu is a Cowork plugin that automatically generates structured IT documentation from any GitHub repository. Point it at a repo, and it produces three dated markdown files — TLD, LD, and TO — committed directly back to that repo.

---

## Document hierarchy

```
TLD  →  Wat bouwen we, waarom, voor wie        (1 document per programma)
 └─ LD  →  Welk domein / fase / laag           (enkele documenten)
     └─ TO  →  Hoe werkt dit specifieke onderdeel  (1 per component)
```

| Document | Full name | Audience | Count |
|----------|-----------|----------|-------|
| **TLD** | Top Level Design | Managers, architects, programme leads | 1 per programme |
| **LD** | Level Design | Architects, senior engineers | Several per programme |
| **TO** | Technisch Ontwerp | Engineers, operators | 1 per component |

---

## Repo structure

```
ML6_Hackathon_DrDocu/
├── Skills/
│   ├── tld-template.skill       # Cowork skill — generates TLD
│   ├── ld-template.skill        # Cowork skill — generates LD
│   ├── to-template.skill        # Cowork skill — generates TO
│   └── Skills readme.md         # This folder's documentation
├── ExampleGit/
│   ├── 1. TLD/TLD-TEMPLATE.md   # Canonical Dutch TLD template
│   ├── 2. LD/LD-TEMPLATE.md     # Canonical Dutch LD template
│   └── 3. TO/TO-TEMPLATE.md     # Canonical Dutch TO template
├── DrDoc Agent/                 # Agent placeholder
├── INSTRUCTIONS.md              # This file
└── README.md                    # Project overview
```

---

## The plugin — repo-docs.plugin

The plugin bundles four Cowork skills and a GitHub MCP server connection.

### Skills

| Skill | Trigger phrase | What it does |
|-------|---------------|--------------|
| `generate-docs` | "Document the repo `owner/repo`" | Runs all three doc types end-to-end |
| `tld` | "Write a TLD for `owner/repo`" | Generates TLD only |
| `ld` | "Write an LD for `owner/repo`" | Generates LD only |
| `to` | "Write a TO for `owner/repo`" | Generates TO only |

### Plugin file structure

```
repo-docs.plugin (zip)
├── .claude-plugin/plugin.json   # Plugin manifest
├── .mcp.json                    # GitHub MCP server config
├── skills/
│   ├── generate-docs/SKILL.md   # Orchestration skill
│   ├── tld/SKILL.md             # TLD skill
│   ├── ld/SKILL.md              # LD skill
│   └── to/SKILL.md              # TO skill
└── README.md
```

---

## How it works — end to end

```
1. User says "Document the repo owner/repo-name" in Cowork
        │
        ▼
2. GitHub MCP reads the repo
   └─ Lists files, reads entry points, manifests, config, source code
        │
        ▼
3. generate-docs orchestrates three skills in sequence
   ├─ tld skill  → writes TLD_YYYY-MM-DD.md
   ├─ ld skill   → writes LD_YYYY-MM-DD.md
   └─ to skill   → writes TO_YYYY-MM-DD.md
        │
        ▼
4. All three files committed to the repo root via GitHub MCP
   └─ Each file gets its own commit with a docs: prefix message
```

### Output files

Each run produces three dated files in the target repo root:

```
TLD_2026-05-28.md
LD_2026-05-28.md
TO_2026-05-28.md
```

Running again on a later date creates a new versioned snapshot alongside the previous ones.

---

## Setup

### 1. Install the plugin

Open Cowork, find the `repo-docs.plugin` file, and click **Save plugin**.

### 2. Set your GitHub token

Create a Personal Access Token at [github.com/settings/tokens](https://github.com/settings/tokens).

Required scope: `repo` (for private repos) or `public_repo` (for public repos only).

Set it as an environment variable:

```bash
GITHUB_PERSONAL_ACCESS_TOKEN=ghp_your_token_here
```

> **Shared token:** Multiple people can use the same token, but all commits will be attributed to the token owner. For correct attribution, each person should use their own token.

### 3. Use it

Open a Cowork session and say:

> "Document the repo `hjfkuiper/ML6_Hackathon_DrDocu`"

---

## Document templates

All generated documents follow the Dutch-language templates in `ExampleGit/`. Each document includes:

**TLD sections:** Doel · Scope · Referentiedocumenten · Definities · Architectuuroverzicht · Ontwerpbeslissingen · Afhankelijkheden · Risico's · Wijzigingshistorie

**LD sections:** Doel · Scope · Referentiedocumenten · Definities · Gedetailleerd ontwerp · Interfaces · Configuratie · Beperkingen · Testoverwegingen · Wijzigingshistorie

**TO sections:** Doel · Toepassingsgebied · Referentiedocumenten · Vereisten vooraf · Procedure (stap-voor-stap) · Verificatie · Rollback procedure · Bekende issues · Wijzigingshistorie

---

## Token & access

| Who | Token needed | Commits attributed to |
|-----|-------------|----------------------|
| One shared token | Yes | Token owner only |
| Individual tokens (recommended) | Yes, one each | Each person correctly |
