
# Skills — DrDocu Cowork Plugin

This folder contains the three Cowork skill files used by the **DrDocu** agent to automatically generate structured IT documentation from a GitHub repository.

## What are these files?

Each `.skill` file is a self-contained Cowork skill that teaches Claude how to produce one type of document. Install them via the `repo-docs` Cowork plugin.

| File | Skill | Output document |
|------|-------|-----------------|
| `tld-template.skill` | `tld` | **TLD** — Top Level Design |
| `ld-template.skill` | `ld` | **LD** — Level Design |
| `to-template.skill` | `to` | **TO** — Technisch Ontwerp (one per component) |

---

## Document hierarchy

```
TLD  →  Wat bouwen we, waarom, voor wie        (1 document per programma)
 └─ LD  →  Welk domein / fase / laag           (enkele documenten)
     └─ TO  →  Hoe werkt dit specifieke onderdeel  (1 per component)
```

**TLD** beschrijft het volledige systeem op programmaniveau: architectuurprincipes, scope en grenzen, technologiestacks, stakeholders, risico's en een register van alle onderliggende documenten. Een TLD per programma.

**LD** werkt een specifiek domein, fase of deelgebied uit. Per programma zijn er meerdere LDs.

**TO** beschrijft EEN specifieke component volledig: requirements, interfaces, architectuurbesluiten, compliance en varianten. Een TO per component — 20 componenten = 20 TOs.

---

## Document templates

The canonical document templates live in `ExampleGit/`:

| Template | Location |
|----------|---------|
| TLD template | `ExampleGit/1. TLD/TLD-TEMPLATE.md` |
| LD template | `ExampleGit/2. LD/LD-TEMPLATE.md` |
| TO template | `ExampleGit/3. TO/TO-TEMPLATE.md` |

All generated documents follow the same section structure and are written in Dutch.

---

## Generated output

When the DrDocu agent runs, it pushes dated markdown files to the root of the target repo:

```
TLD_YYYY-MM-DD.md
LD_YYYY-MM-DD.md
TO_NGINX_YYYY-MM-DD.md
TO_RabbitMQ_YYYY-MM-DD.md
TO_API_YYYY-MM-DD.md
... (one TO per detected component)
```

Components are detected from `docker-compose.yml`, subdirectory layout, or infrastructure config.

---

## How to use

1. Install the `repo-docs.plugin` in Cowork
2. 2. Set `GITHUB_PERSONAL_ACCESS_TOKEN` in your environment (needs `repo` scope)
   3. 3. In a Cowork session, say: **"Document the repo `owner/repo-name`"**
     
      4. Individual document types:
      5. - *"Write a TLD for `owner/repo`"*
         - - *"Write an LD for `owner/repo`"*
           - - *"Write a TO for `owner/repo`"* (generates one per detected component)
            
             - ---

             ## Audience

             | Document | Primary reader |
             |----------|---------------|
             | TLD | Managers, architecten, programmamanagers |
             | LD | Architecten, senior engineers |
             | TO | Engineers, operators |
             
