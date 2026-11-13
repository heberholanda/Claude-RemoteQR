# Remote QR: rules for every change and release

This repository is a Claude Code plugin whose code is a hooks module (a mod: `hooks/register.tsx`, `hooks/qr.ts`). Every change must keep it valid for `claude plugin validate`, `claude plugin test` and the plugin directory's checks at https://claude.ai/directory/manage. These rules come from that directory's findings; break none of them.

## Directory status

The directory refused v0.1.2 (75918be): it accepts mods, and code files such as `hooks/qr.ts`, only from Verified organizations, and this account's organization is not one (no way to apply yet). Keep the code clean anyway: it is what a Verified listing, and the install from this repo's own marketplace, rely on.

## Code rules (hooks module)

- Never hook `tool.check`, `agent.spawn`, `agent.register`, `config.set` or `fs.write`.
- Every hook passes its event on unchanged: `return next(e)`, or `const r = await next(e)` and `return r`. Never change `e`.
- `$` appears only as `$.noun.method(...)` or as one whole argument to a top-level function; helpers that need `$` are closures inside the hook.
- Never declare `h`, `Fragment`, `jsx_*`, `jsxs_*`, `Fragment_*`. Never declare a variable named `e` or `next`.
- The mod runs no programs and makes no network requests. If that ever changes: programs by name with fixed arguments (no `cmd /c`, `sh -c`, `curl`, `npx`), network only through `$.http.fetch` to a literal host, and the README updated in the same change.
- No credential words (token, key, secret, password) near a URL; read no token or env credential.
- New logic gets a test under `tests/`.

## README rules

- The section **What each hook does** names every hooked event in backticks, the files and environment variables the mod reads, and anything it would send. Update it with the code.
- No download-and-run text: no `curl … | sh`, and no `git clone` followed by a run command in one code block.

## Manifest rules (`.claude-plugin/plugin.json`)

Keep `name`, `displayName`, `version`, `description`, `author`, `license`, `homepage`, `repository`, `documentationUrl`, `supportUrl`, `privacyPolicyUrl`, `keywords` and `icon` (`.claude-plugin/icon.png`, square PNG, 512–2048 px, under 2 MB). The directory keeps the first icon it sees.

## Release checklist

1. Bump `version` in `.claude-plugin/plugin.json` (semver: feature = minor, fix = patch).
2. `bash scripts/release-check.sh` prints `PASS`. It runs validate and tests and checks the rules above that can be checked mechanically.
3. Push, then re-validate the repository at https://claude.ai/directory/manage/new/plugin: no **Blocking** finding other than the Verified-organization ones; anything new is explained in the README.
4. `gh release create vX.Y.Z` with notes in the format of the previous releases.
5. Commit messages, PR bodies and release notes carry no Claude or Co-Authored-By attribution.
