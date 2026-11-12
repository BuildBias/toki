# Benchmark methodology

This page explains how the token, cost and time numbers in the [README](../README.md#benchmark) were measured.

Every number comes from real headless Claude Code sessions. Tokens and cost are read back from each session's `.jsonl` transcript.

## 1. What was compared

Three agents got identical projects and prompts. All three ran Claude Sonnet through `claude -p`.

| Mode | Setup |
| --- | --- |
| **base** | Plain Claude Code with no skill |
| **toki** | Plain Claude Code with the Toki skill installed in the project. The prompts never mention the skill, so it only runs if Claude decides to use it. |
| **competitor** | No skill; Ponytail loaded with `--plugin-dir ponytail` |

Each session is started like this:

```bash
claude -p "<prompt>" --model sonnet --permission-mode acceptEdits \
  --setting-sources project --strict-mcp-config \
  --allowedTools Read Write Edit Glob Grep Skill "Bash(mkdir:*)" "Bash(mv:*)" "Bash(ls:*)" ... \
  --output-format json --session-id <pre-assigned uuid>
```

## 2. Test projects and prompts

The test projects are small dummy projects. All three agents get the same projects and the same prompts.

- **Stacks:** React SPA (Vite), Django, Next.js.
- **Prompts** are ordinary coding requests that never mention the skill or where files should go.
- **Runs:** each project gets 5 prompts, sent one after another in a single session. Every agent runs this 3 times per project, and the charts show the mean of the 3 runs.

The prompts used:

### React SPA (Vite)

1. Write a unit test for the API client's `fetchCouriers` (a successful response and a failed one), mocking `fetch`.
2. Add a `CourierCard` React component that renders a courier's name and a status badge, and use it in the existing courier list page instead of the inline markup.
3. Add a React hook `useCouriers` that loads the courier list from the API and exposes `items`, `loading` and `error`.
4. Add a `formatStatus` helper module that turns a courier status into a readable label (for example `archived` becomes `Archived`), and use it in the `CourierCard` component.
5. Add a `CourierDetail` page that loads one courier by id with a new `fetchCourier(id)` API function and shows its name and status.

### Django

1. Add a management command `archive_vessels` that marks every draft vessel as archived and prints how many were changed.
2. Add a service function that bulk-creates vessels from a list of names in one transaction. Don't call it anywhere yet.
3. Add a template filter `status_badge` that turns a vessel status into a coloured HTML badge.
4. Write tests for the `Vessel` model's `is_active` property and for the vessel list and detail views (including a 404).
5. Add a `ModelForm` for creating a vessel that rejects names shorter than 3 characters.

### Next.js (App Router)

1. Add a middleware that redirects signed-out users from `/dashboard` to `/login`. A user counts as signed in when the `session` cookie is set.
2. Add an API route that returns all vessels as JSON.
3. Add a `VesselCard` component that renders a vessel's name and a status badge, and use it on the vessels page instead of the inline markup.
4. Add a page that shows a single vessel at `/vessels/<id>`, and return a 404 when the id doesn't exist.
5. Add a `formatStatus` helper module that turns a vessel status into a readable label (for example `archived` becomes `Archived`), and use it in the `VesselCard` component.

## 3. How tokens and cost are measured

For every session, the measurement:

1. Finds the main transcript `~/.claude/projects/*/<session_id>.jsonl` plus any subagent transcripts in `<session_id>/subagents/*.jsonl`.
2. Reads `usage` from each assistant message. A message streamed in several blocks repeats its usage on every line, so usage is deduplicated by message ID. `<synthetic>` messages are skipped.
3. Sums `input_tokens`, `output_tokens`, `cache_creation_input_tokens` and `cache_read_input_tokens`. **"Tokens" in the charts is the sum of all four, so cache reads are included.**
4. Prices each message per million tokens. For Sonnet:

   | Token type | Price per 1M |
   | --- | --- |
   | Input | $2.00 |
   | Cache write, 5 min | $2.50 |
   | Cache write, 1 h | $4.00 |
   | Cache read | $0.10 |
   | Output | $10.00 |

   Cache writes are split into 1-hour and 5-minute using `usage.cache_creation.ephemeral_1h_input_tokens`.

**Time** is the wall clock from the first to the last transcript timestamp, so API retries and stalls are included.
