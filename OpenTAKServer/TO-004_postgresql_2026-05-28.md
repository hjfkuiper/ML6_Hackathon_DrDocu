# OpenTAKServer — Technisch Ontwerp: PostgreSQL Database

| | |
|---|---|
| **Document ID** | TO-004 |
| **Versie** | 0.1 |
| **Status** | DRAFT |
| **Datum** | 2026-05-28 |
| **Auteur** | DrDocu Agent |
| **Eigenaar** | ML6 |
| **Gerelateerde TLD/LD** | TLD-001 / LD-001 |
| **Classificatie** | Intern |

---

## 1. Doel

Dit document beschrijft de installatie en configuratie van PostgreSQL als relationele database voor OpenTAKServer (OTS). PostgreSQL slaat alle persistente data op: EUD-registraties, CoT-berichten, missies, gebruikersaccounts, certificaten en configuratiedata. De procedure omvat databasegebruiker aanmaken, schema-initialisatie via Flask-Migrate en verbindingsverificatie.

---

## 9. Wijzigingshistorie

| Versie | Datum | Auteur | Omschrijving |
|---|---|---|---|
| 0.1 | 2026-05-28 | DrDocu Agent | Initieel document aangemaakt |
