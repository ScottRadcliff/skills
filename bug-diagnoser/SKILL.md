---
name: bug-diagnoser
description: "Diagnose software bugs before implementation. Use when the user writes bug: ... or asks to investigate a defect, identify likely causes, suggest codebase-consistent fixes, consider edge cases, and run relevant tests without making code changes until explicitly told to implement."
---

# Bug Diagnoser

## Overview

Use this skill as a read-only diagnostic workflow for software bugs. The goal is to move from a bug report to a ranked, evidence-backed likely cause and a proposed fix plan while preserving the current style and conventions of the codebase.

## Trigger Form

Treat prompts that start with `bug:` as requests to diagnose only. Do not edit code, create migrations, update fixtures, or modify specs unless the user later gives an explicit implementation instruction such as "implement it", "fix it", or "make the change".

## Things to Avoid

Do not make code or configuration changes during diagnosis. Do not connect to external systems unless the user explicitly requests it and the connection is necessary to gather evidence.

## Diagnostic Workflow

1. Restate the observed bug in concrete terms, including the likely user flow, input, output, and failure mode.
2. Search the codebase with `rg` for domain terms, route/controller names, model names, service names, jobs, serializers, and error text from the bug report.
3. Read the smallest useful set of files that define the behavior. Prefer production code, request/controller/model/service specs, factories, and fixtures that illuminate the behavior.
4. Ignore view specs. Do not use `spec/views`, files ending in `_view_spec.rb`, or view-spec failures as evidence unless the user explicitly asks for view-spec work.
5. Form several plausible causes, then rank them by fit with the evidence. Name the most likely cause and explain why alternatives are weaker.
6. Consider edge cases before suggesting a fix: nil/blank values, permissions, tenant/account scoping, background jobs, state transitions, persistence-layer query behavior, time zones, retries, duplicate records, and feature flags.
7. Suggest the smallest codebase-consistent change that would address the cause. Match existing patterns for naming, control flow, persistence, error handling, and test style in nearby files.
8. Run only relevant, non-mutating tests or checks when they are available and safe; report commands attempted, results, and blockers. Do not run broad or environment-dependent test commands without a clear reason.

## Output Shape

Respond with:

- `Most likely cause`: one concise, evidence-backed cause.
- `Evidence`: code references, logs, spec behavior, or data-shape clues that support the diagnosis.
- `Other plausible causes`: lower-ranked possibilities and what would confirm or rule them out.
- `Suggested change`: the smallest implementation approach, without applying it.
- `Edge cases`: cases the implementation should cover.
- `Specs`: relevant specs to run, commands attempted, and results or blockers.

Keep the tone direct and practical. If the diagnosis is uncertain, say what evidence would reduce uncertainty.

