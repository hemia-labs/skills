# Extraction Rules

Read all provided specification files before drafting the roadmap.

Extract:

- Source file name and one-line purpose.
- Project or product name.
- Core rule: what the system owns and what it must not own.
- Systems, vendors, products, and boundaries.
- Functional requirements.
- Technical requirements.
- Data model requirements.
- Endpoints, events, jobs, integrations, or CLIs.
- Security, compliance, audit, legal, privacy, and operational requirements.
- Seeds, migrations, config, env vars, and deployment needs.
- Known risks, constraints, edge cases, and open questions.

Classify each item as:

- Explicit: stated directly in the spec.
- Inferred: needed to implement the spec safely.
- Decision: project choice needed before or during implementation.
- Question: missing information that should not be guessed.

Do not include unrelated nice-to-have work unless the spec implies it or the user asks.

