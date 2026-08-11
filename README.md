# everyharness-container

A shared container image preinstalled with ~17 coding-agent CLIs — Claude Code,
Codex, Gemini CLI, opencode, GitHub Copilot CLI, Factory (droid), Qwen Code,
Kimi Code, Kilo Code, OpenClaw, Amp, Cline, Cursor Agent, pi-coding-agent,
Goose, Hermes, Mimo, Google Antigravity (agy), mini-swe-agent, trae-agent, and
SWE-agent — plus the base toolchains they need (Node, bun, uv, Rust, mise,
Python, Go, Ruby).

The Dockerfile is extracted from `prime-radiant-inc/superpowers-evals`'
`container/` directory, which has run this exact set of harness-CLI installs
in production evals for months. This repo keeps only the reusable
harness-install layer — no evals-specific tooling.

## Consumers

- **`everyharness test`** — mounts a generated plugin into this image and
  runs offline, per-harness install checks against it.
- **`superpowers-evals`** — plans to migrate its own container build onto
  this image, reusing the harness-CLI layer instead of maintaining a
  duplicate copy (future work, tracked in that repo).

## Pull

```
docker pull ghcr.io/prime-radiant-inc/everyharness-container:latest
```

Images are also tagged with the git commit SHA they were built from:
`ghcr.io/prime-radiant-inc/everyharness-container:<sha>`.

## `harness-versions`

The image ships `harness-versions` on `PATH`. It prints the resolved version
of every core tool and agent CLI baked into the image (or `missing` if a
tool isn't present):

```
docker run --rm ghcr.io/prime-radiant-inc/everyharness-container:latest harness-versions
```

## Version-pin policy

Every harness CLI is pinned to an exact version (npm `@scope/pkg@x.y.z`,
`GOOSE_VERSION`, `HERMES_COMMIT`, etc.) rather than tracking `latest`. Pins
are deliberate: they reproduce a known-working configuration and keep image
rebuilds from silently picking up a breaking upstream release.

Bump pins via PR, one CLI per PR. Include the CLI's own changelog/release
notes in the PR description and confirm the image still builds before
merging.

## Provenance

Extracted 2026-08-11 from `prime-radiant-inc/superpowers-evals`'
`container/` directory (Dockerfile, `bin/evals-tool-versions`). Evals-only
pieces (serf, gauntlet, quorum, the evals workdir) were dropped; the
harness-CLI install layer was kept verbatim, including version pins,
environment variables, and the `TARGETARCH` plumbing used for goose's
architecture-specific download.

## License

MIT — see [LICENSE](LICENSE).
