# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-11-11

Initial release.

### Added

- `toki` skill: a mandatory file-placement guardrail invoked before creating, adding, moving or renaming any file or directory.
- Turn plan that batches project discovery and file reads into a single shell command and writes new files together with their wiring edits.
- Routing rules: one file per unit, reuse of existing role directories, standard framework locations, and no new code files in the project root.
- Preservation rules: existing files are never rewritten, emptied, shortened or deleted; moves keep content byte-for-byte intact.
- Lean execution rules: no unrequested builds, retries of blocked commands, or extra scaffolding, and replies limited to two sentences.
- Claude Code plugin and marketplace manifests for installing via `claude plugin marketplace add BuildBias/toki`.
- Benchmark charts comparing token usage, cost and time against plain Claude Code and another plugin.

[1.0.0]: https://github.com/BuildBias/toki/releases/tag/v1.0.0
