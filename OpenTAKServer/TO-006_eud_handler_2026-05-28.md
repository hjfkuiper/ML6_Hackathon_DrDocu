# OpenTAKServer — Technisch Ontwerp: EUD Handler (TCP/SSL/UDP verbindingen)

| | |
|---|---|
| **Document ID** | TO-006 |
| **Versie** | 0.1 |
| **Status** | DRAFT |
| **Datum** | 2026-05-28 |
| **Auteur** | DrDocu Agent |
| **Eigenaar** | ML6 |
| **Gerelateerde TLD/LD** | TLD-001 / LD-001 |
| **Classificatie** | Intern |

---

## 1. Doel

Dit document beschrijft de configuratie van de EUD (End User Device) Handler in OpenTAKServer. De EUD Handler is verantwoordelijk voor het ontvangen en verwerken van CoT (Cursor on Target) XML-berichten van TAK-clients via drie transportprotocollen: TCP op poort 8088 (onversleuteld), SSL/TLS op poort 8089 (versleuteld met wederzijdse authenticatie) en UDP op poort 8087 (broadcast). Dit document beschrijft poortconfiguratie, firewall-instellingen, netwerkinterface-binding en verificatiemethoden.

---

## 9. Wijzigingshistorie

| Versie | Datum | Auteur | Omschrijving |
|---|---|---|---|
| 0.1 | 2026-05-28 | DrDocu Agent | Initieel document aangemaakt |
