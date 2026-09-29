# Agent Development Skills

A personal collection of agent skills written to support the software development lifecycle and shared publicly for others to use. Personal and business-specific information has been extracted so the skills can be reused independently.

## What’s Included

### `bug-diagnoser`

Diagnoses software bugs before implementation. It searches the codebase for evidence, ranks likely causes, considers edge cases, recommends the smallest codebase-consistent change, and runs relevant checks without modifying application code.

The skill is triggered by prompts that begin with `bug:` or by an explicit request to investigate a defect. It produces a structured response covering:

- Most likely cause
- Evidence
- Other plausible causes
- Suggested change
- Edge cases
- Relevant specs and checks

## Repository Structure

```text
.
├── bug-diagnoser/
│   ├── SKILL.md              # Skill instructions and workflow
│   ├── README.md             # Skill-specific notes
│   └── agents/openai.yaml    # Display metadata and default prompt
└── README.md                 # This overview
```

Each skill lives in its own directory. `SKILL.md` is the source of truth for the skill’s behavior, while `agents/openai.yaml` provides the metadata used to present and invoke it.
