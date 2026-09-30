# OpenJEV Support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside the original
[TypeSafe](https://typesafe.ai) integration. Jev is TypeSafe's model; OpenJEV is a free
community gateway to the same model. TypeSafe remains the default — anyone with a TypeSafe
key sees zero behaviour change.

## What was added

| File | Change |
|---|---|
| `README.md` | OpenJEV support note after the intro; OpenJEV install alternative (commented, step 1b); OpenJEV row in the "Other judges" table. |
| `package.json` | Added `openjev` keyword. |
| `OPENJEV.md` | This file. |

No source code (`src/`) was modified. The plugin delegates to OpenClaw's `decisionModel`
role (`api.runtime.decisions.evaluate`) and is provider-agnostic by design. OpenJEV is
configured at the OpenClaw configuration level, not in plugin code.

## Provider selection rule

1. **Explicit choice wins** — set `JEV_PROVIDER=openjev` or configure
   `decisionModel: "typesafe/openjev"` to use OpenJEV.
2. **Otherwise, if `TYPESAFE_API_KEY` is set** → TypeSafe (unchanged default).
3. **Otherwise, if only `OPENJEV_API_KEY` is set** → OpenJEV.

## How to configure OpenJEV

The same `@openclaw/typesafe` plugin is used, pointed at OpenJEV's endpoint:

```sh
openclaw plugins install @openclaw/typesafe
openclaw secrets store set OPENJEV_API_KEY          # key from https://openjev.sh/dashboard
openclaw config set plugins.entries.typesafe.config.apiKey \
  '{"source":"store","provider":"default","id":"OPENJEV_API_KEY"}' --json
openclaw config set plugins.entries.typesafe.config.baseUrl \
  '"https://api.openjev.sh/v1/systemone"' --json
openclaw config set plugins.entries.typesafe.enabled true --json
openclaw config set agents.defaults.decisionModel '"typesafe/openjev"' --json
```

API contract differences from TypeSafe direct:

| | TypeSafe | OpenJEV |
|---|---|---|
| Endpoint | `https://api.typesafe.ai/v1/systemone` | `https://api.openjev.sh/v1/systemone` |
| Model | `jev-latest` | `openjev` |
| Key env | `TYPESAFE_API_KEY` | `OPENJEV_API_KEY` |
| Overload status | 529 | 503 (also handle 429) |

## Verification

A live POST request was made to `https://api.openjev.sh/v1/systemone` with model `openjev`,
state `ping`, and one noul question. The endpoint returned HTTP 200 with a valid answer.

No repository code was executed during this port. Changes are documentation and metadata only.

## Upstream

Original project: https://github.com/yousan/openclaw-jev-trigger by @yousan
