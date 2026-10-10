---
name: dev-server-setup
description: Create or adapt a local Docker development image, Compose setup, and Makefile based on an application's language, framework, database, console, and tests. Use when setting up containerized development or scaffolding an application in an empty directory; production deployment is out of scope.
---

# Server and Makefile Setup

Build a working local development environment for the application in the current directory. Generate the files, run available verification, and report how to start the server, enter its console, and run tests. Adapt the environment to the application rather than imposing a framework or replacing useful existing setup.

## Inspect and choose

Read applicable repository instructions, manifests, lockfiles, runtime version files, framework configuration, existing Docker/Compose files, Makefiles, and relevant development documentation. Determine:

- Language, framework, runtime version, package manager, and dependency installation command.
- Development server command, listening port, and reload behavior.
- Framework console or language REPL and the test runner.
- Database adapter and required supporting services, such as a queue or cache.

Prefer executable configuration and locked versions over guesses. Preserve existing conventions and user changes. In a monorepo, use an identified application and its dependencies; ask which application to configure if the target is ambiguous. For an unfamiliar framework, consult its official documentation for uncertain commands or image requirements.

If there are no application files, ignoring repository metadata and incidental files, ask what language/framework and application type the user wants to create. Wait for the answer, scaffold that application with its standard tooling, and then configure development. Do not treat an unfamiliar existing project as an empty directory. Ask for missing information only when it blocks a sound choice.

Default to PostgreSQL when the application needs a database but has no established choice. Honor explicit choices and existing adapters, including SQLite. Do not add a database to an application that has no need for one. Match an established database version; otherwise choose a supported version compatible with the application. Configure container connections using the Compose service hostname, not localhost.

## Create or adapt the files

Produce a development Dockerfile, Compose configuration (normally `compose.yaml`), `.dockerignore`, and Makefile. Add `.env.example` when environment variables need documentation. Reuse established filenames and service names; avoid parallel, competing Compose setups. Keep unrelated files and existing useful Makefile targets intact.

The Dockerfile must install the application's actual dependencies and required system libraries. Honor runtime files, lockfiles, and the detected package manager. Account for development bind mounts hiding image-installed dependencies: use an appropriate dependency location or volume and ensure a fresh checkout can start without a host dependency installation.

Run the development server in the foreground, listening on `0.0.0.0`. Use an exec-form command or an entrypoint that ends with `exec` so shutdown signals reach the server. Do not daemonize the application inside its container. Preserve development reload behavior where supported. Expose the appropriate host port and avoid guessing additional services.

Use named volumes for database persistence. For SQLite, persist the directory containing the database when necessary. Keep normal stop/down commands from removing volumes. Backups, recovery tooling, production deployment, TLS, production tuning, and production hardening are out of scope. Accidental data loss is acceptable to the user, but routine commands should retain data.

Provide database readiness checks where needed and make first-run database preparation explicit and reliable. Follow framework conventions for database creation and migrations; avoid resetting data or running destructive setup on every start. Keep test data separate from development data. Include required supporting services only when project evidence calls for them.

## Makefile interface

Use modern `docker compose` unless the project explicitly requires another compatible invocation. Centralize the Compose command and application service where useful. Mark command targets `.PHONY`, use real recipe tabs, and make `help` the default target. Account for Make's `$` escaping in shell expressions.

| Target | Required behavior |
| --- | --- |
| `help` | Describe the available targets. |
| `build` | Build the development images. |
| `up` | Start required services with attached foreground logs; never use `-d` for this target. |
| `down` | Stop and remove containers and networks while retaining volumes. |
| `stop` | Stop services. |
| `restart` | Restart services. |
| `logs` | Follow service logs. |
| `ps` | Show service status. |
| `shell` | Open an available shell in the running application container. |
| `console` | Open an interactive application console or language REPL in the running application container. |
| `test` | Run the detected test suite in a container and propagate its exit status. |

Use `compose exec` for interactive shell and console targets, retaining interactive terminal behavior. `console` should load the application environment when supported, such as Rails console or Django shell. Otherwise use the language's REPL. Explain that these targets require a running application container. Do not substitute a shell for the REPL. If the runtime has no standard REPL, determine an appropriate project-compatible REPL or ask the user rather than inventing a command.

Make `test` work without an already-running application server, normally with `compose run --rm` and the necessary dependency services. Use the project's test command and test environment; ensure database-backed tests use a separate test database and any required preparation. For projects with no test suite, provide an actionable explanation or placeholder target that exits nonzero rather than claiming tests pass. Add `spec` as an alias when appropriate and framework targets such as `setup` or `migrate` when useful. Do not add destructive cleanup targets by default.

Examples are clues, not fixed recipes: Rails commonly uses `bin/rails server`, `bin/rails console`, and the installed RSpec or Rails test runner; Django commonly uses `manage.py runserver`, `manage.py shell`, and the configured pytest or Django runner; Node projects should follow package scripts and their lockfile-selected package manager. Discover the actual commands and required environment before writing recipes.

## Verify and hand off

Inspect the generated files and resolve variables with `docker compose config` and suitable Make dry runs. Avoid printing expanded secrets from existing environment files. When Docker is available, build the image and start the environment in a temporary detached verification run, even though the generated `up` target stays foreground. Confirm the application's endpoint or startup health, database connectivity when applicable, and first-run preparation. Check console command availability and run the test target when a suite exists. Distinguish pre-existing application/test failures from setup failures and fix failures caused by this setup.

Track which services were already running. Stop services started solely for verification when finished unless the user requested they remain running; do not stop pre-existing services or delete volumes. Do not leave attached long-running commands blocking completion.

If Docker, network access, or another prerequisite is unavailable, still generate and inspect the files. State exactly which runtime checks could not be performed; do not claim a successful build or startup without evidence. Finish with changed files, the commands to start the application, open its console, and run tests, plus any necessary first-run step and material verification limitations.
