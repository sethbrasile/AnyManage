---
entity_type: client
entity_type_plural: clients
entity_profile_doc: ENTITY_PROFILE
entity_roadmap_doc: ENTITY_ROADMAP
---

# AnyManage Configuration

This system manages **clients** for your organization.

## What is an "Entity"?

An entity is whatever you manage with this system. By default, it's configured for "clients" (like a marketing agency managing client accounts), but you can customize it for any use case:

| Use Case | entity_type | entity_type_plural |
|----------|-------------|-------------------|
| Marketing Agency | client | clients |
| Software Company | project | projects |
| Product Team | product | products |
| Healthcare | patient | patients |
| Consulting | engagement | engagements |

To change your entity type, edit the frontmatter at the top of this file.

## Current Configuration

- **Entity Type (Singular):** client
- **Entity Type (Plural):** clients
- **Profile Document:** ENTITY_PROFILE.md
- **Roadmap Document:** ENTITY_ROADMAP.md

## Folder Structure

```
project-root/
├── entities/              <- All your [clients] live here
│   └── [Entity Name]/     <- One folder per [client]
│       ├── ENTITY_PROFILE.md
│       ├── ENTITY_ROADMAP.md
│       └── [subfolders]
├── templates/             <- Reusable document templates
│   ├── ENTITY_PROFILE_TEMPLATE.md
│   └── ENTITY_ROADMAP_TEMPLATE.md
├── ops/                   <- Your team's operations
├── CONFIG.md              <- This file
└── ROADMAP.md             <- Master dashboard
```

## Document Naming Convention

Core documents use the `ENTITY_` prefix to indicate they come from templates:

- `ENTITY_PROFILE.md` - Entity information and context
- `ENTITY_ROADMAP.md` - Entity-specific work tracking

Templates in the `templates/` folder use the `_TEMPLATE` suffix:

- `ENTITY_PROFILE_TEMPLATE.md` - Template for new profiles
- `ENTITY_ROADMAP_TEMPLATE.md` - Template for new roadmaps

## How It Works

1. **Create an entity:** Make a new folder in `entities/` with the entity's name
2. **Copy templates:** Copy template files from `templates/` to the new entity folder
3. **Fill in details:** Replace placeholder text with actual information
4. **Track on dashboard:** Add the entity to `ROADMAP.md` for status tracking

The AI assistant handles all of this for you - just tell it what you want to do!
