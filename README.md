# Agent Development Skills

A personal collection of agent skills written to support the software development lifecycle and shared publicly for others to use. Personal and business-specific information has been extracted so the skills can be reused independently.

## Available Skills

### [`dev-server-setup`](dev-server-setup/SKILL.md)

Creates or adapts a local Docker development environment for an existing application, or scaffolds an application in an empty directory after confirming the framework. It inspects the project, generates Docker and Compose configuration plus a Makefile, and verifies the server, console, database, and tests when the required tools are available.

**Use when:** Setting up containerized local development or scaffolding an application in an empty directory. Production deployment is outside its scope.

**Example prompt:**

```text
Use $dev-server-setup to configure this application for local Docker development with a Makefile.
```

### [`bug-diagnoser`](bug-diagnoser/SKILL.md)

Diagnoses software bugs before implementation. It searches the codebase for evidence, ranks likely causes, considers edge cases, recommends the smallest codebase-consistent change, and runs relevant checks without modifying application code.

**Use when:** Investigating a defect before implementation. Invoke it explicitly or begin your prompt with `bug:`. Application code changes require a separate instruction to implement the fix.

**Example prompt:**

```text
Use $bug-diagnoser to investigate why submitting the signup form returns a 500 error. Diagnose it before making changes.
```

## Installation

These are agent instructions, with no package dependencies or standalone server to run in this repository. You need Git and an agent that supports `SKILL.md` skills. Docker and Make are needed when using `dev-server-setup` to build and run an application's environment.

Clone the collection:

```bash
git clone https://github.com/ScottRadcliff/skills.git
cd skills
```

For Codex, install either or both skills for use across your projects. Run these commands from the cloned repository:

```bash
mkdir -p "$HOME/.agents/skills"
cp -R dev-server-setup "$HOME/.agents/skills/"
cp -R bug-diagnoser "$HOME/.agents/skills/"
```

For installation in a single project, copy the desired skill directory into that project's `.agents/skills/` directory instead. Copy the whole directory so its metadata stays with `SKILL.md`. Check for an existing installation before copying; copying to an existing directory can overwrite local edits. Copies do not update automatically when this repository changes.

Codex detects installed skills automatically; restart it if a skill does not appear. These locations and discovery behavior follow the [official OpenAI skill documentation](https://learn.chatgpt.com/docs/build-skills). For other agents, use their documented skill directory and invocation syntax.

## Usage

Open the application you want to work on in your agent, then invoke a skill using its example prompt above. Each skill's linked `SKILL.md` contains its full workflow. The [development server setup guide](dev-server-setup/README.md) explains prerequisites, first-run steps, and generated Make commands.

Run development commands in the target application's directory after setup, not in this skills repository. The setup skill reports any configuration or first-run steps needed for that application.

## Repository Structure

```text
.
├── bug-diagnoser/
│   ├── SKILL.md              # Skill instructions and workflow
│   ├── README.md             # Skill-specific notes
│   └── agents/openai.yaml    # Display metadata and default prompt
├── dev-server-setup/
│   ├── SKILL.md              # Development environment workflow
│   ├── README.md             # Setup and usage guide
│   └── agents/openai.yaml    # Display metadata and default prompt
└── README.md                 # This overview
```

Each skill lives in its own directory. `SKILL.md` is the source of truth for the skill’s behavior, while `agents/openai.yaml` provides the metadata used to present and invoke it.
