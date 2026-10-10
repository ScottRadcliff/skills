# Development Server Setup

An agent skill that creates or adapts a local Docker development environment with a consistent Makefile interface. It discovers the application's language, framework, dependencies, database, server, console, and test commands before generating configuration.

## Prerequisites and Installation

Install the skill using the [main README's instructions](../README.md#installation), then open the target application in your agent.

To build and run the generated environment, the host needs:

- Docker with a running engine and the `docker compose` command.
- Make.
- Network access to download container images and application dependencies on the first build.
- An available host port for the development server.

Check the host tools with:

```bash
docker info
docker compose version
make --version
```

An existing application's dependencies are installed in containers; a host runtime installation should not be required to start it from a fresh checkout. Scaffolding an empty directory may require additional framework tooling, which the agent determines after you choose a framework.

## Invoke the Skill

For an existing application:

```text
Use $dev-server-setup to configure this application for local Docker development with a Makefile.
```

To adapt existing configuration:

```text
Use $dev-server-setup to update this application's existing Docker, Compose, and Makefile setup. Follow the project's runtime versions and database configuration.
```

For an empty directory, provide the framework and application type:

```text
Use $dev-server-setup to scaffold a Django web application in this empty directory and configure local Docker development with PostgreSQL and a Makefile.
```

If you omit the framework for an empty directory, the agent asks before scaffolding. In a monorepo, identify the application to configure. Include any required database choice or host port in your prompt.

## What It Creates

The skill creates or adapts:

- A development Dockerfile with the application's runtime and dependencies.
- Compose configuration, normally `compose.yaml`, with the application and required supporting services.
- `.dockerignore`.
- A Makefile with the commands below.
- `.env.example` when environment variables need documentation.

It preserves useful existing configuration and Makefile targets. Database selection follows the application's adapter or your explicit choice; PostgreSQL is the default only when a database is needed and no choice exists. Database volumes persist through normal stop and down commands, and database-backed tests use separate test data.

## Start and Use the Environment

Run these commands in the target application's directory after the skill finishes. Follow its handoff for required environment variables and first-run database preparation; a project may include additional `setup` or `migrate` targets.

```bash
make help
make build
# Complete the application's reported configuration and first-run steps.
make up
```

`make up` keeps the server and logs attached in the foreground. Open the URL reported by the agent. While it runs, use another terminal in the same application directory:

```bash
make console
make test
```

The console and shell require a running application container. The test target works without an already-running application server and starts required dependency services as needed.

| Command | Purpose |
| --- | --- |
| `make` or `make help` | Show available commands. |
| `make build` | Build development images. |
| `make up` | Start services with foreground logs. |
| `make down` | Remove containers and networks, retaining volumes. |
| `make stop` | Stop services. |
| `make restart` | Restart services. |
| `make logs` | Follow service logs. |
| `make ps` | Show service status. |
| `make shell` | Open a shell in the running application container. |
| `make console` | Open the application's console or language REPL. |
| `make test` | Run the project's test suite in a container. |

To shut down the environment, run `make down` from another terminal. If the application has no test suite, `make test` reports what is missing and exits nonzero.

## Verification and Scope

The agent checks Compose configuration and Make recipes. When Docker is available, it builds and temporarily starts the environment, checks startup and database connectivity where applicable, checks console command availability, and runs existing tests. It stops services started solely for verification unless you request that they remain running, and leaves pre-existing services alone.

If a prerequisite prevents runtime verification, the agent still generates and inspects the files and reports the checks it could not perform. Its handoff includes changed files, startup commands, first-run steps, and verification results.

This skill covers local development. Production deployment, TLS, production tuning, and backup or recovery tooling are outside its scope. See [SKILL.md](SKILL.md) for the full workflow and [agents/openai.yaml](agents/openai.yaml) for display metadata and the default prompt.
