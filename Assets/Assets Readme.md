# Assets

This folder contains the official documentation templates for the ML6 Hackathon DrDocu project. Use these templates as a starting point for writing technical documentation.

## Contents

| File | Type | Description |
|---|---|---|
| `TLD-TEMPLATE.md` | Top Level Design | High-level technical design of a solution or system |
| `LD-TEMPLATE.md` | Level Design | Detailed technical elaboration of a TLD |
| `TO-TEMPLATE.md` | Technical Design | Description of technical choices, architecture, and implementation |

## Usage

1. Copy the desired template to the appropriate folder in your project.
2. Rename the file based on the document ID (e.g. `TLD-001.md`).
3. Fill in all fields and remove the instructional comments.
4. Set the status to `DRAFT` on creation, `REVIEW` during review, and `APPROVED` after approval.

## Document Hierarchy

```
TLD (Top Level Design)
└── LD (Level Design)
    └── TO (Technical Design)
```

A TLD provides the high-level direction, an LD elaborates on this per component, and a TO describes the concrete technical implementation.
