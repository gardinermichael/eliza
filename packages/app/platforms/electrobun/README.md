# @elizaos/electrobun

Native desktop shell for the elizaOS app on macOS, Windows, and Linux.

## Launch the desktop app

On an Apple Silicon Mac, open an existing development build from the repository
root:

```bash
open packages/app/platforms/electrobun/build/dev-macos-arm64/Eliza-dev.app
```

You can also double-click `Eliza-dev.app` in Finder. If the app bundle is missing
or you changed the source, build it first:

```bash
bun run --cwd packages/app/platforms/electrobun build
```

For a fresh checkout, use Bun 1.4.2 and Node.js 24.15.0, then run `bun install`
from the repository root before building. See the signing workaround below if
the macOS build fails with `No macOS .app bundle found`.

To run the desktop shell in development mode:

```bash
bun run --cwd packages/app/platforms/electrobun dev
```

To run the app UI and API through the repository's source launcher instead,
run `bun run dev` from the root. Use `bun run dev:local` for local-agent or
direct-provider setup without Cloud sign-in. Follow the terminal's startup
instructions and keep that terminal open; press Ctrl+C to stop it.

## Development

Install dependencies with `bun install` at the repository root. Run from that root:

```bash
bun run --cwd packages/app/platforms/electrobun build  # build
bun run --cwd packages/app/platforms/electrobun test   # tests
```

Set `ELIZA_DESKTOP_BUILD_CONCURRENCY=1` for memory-constrained desktop builds.
The runtime dependency build accepts 1–32 simultaneous jobs and defaults to 8.

Linux x64 packaging source-builds the pinned CEF helper with a fork-safe entry
point. The first build needs `curl`, `tar`, `patch`, `cmake`, and `g++`, and downloads
the verified CEF SDK; subsequent builds reuse its checked cache. Set
`ELIZA_DESKTOP_CEF_HELPER_CACHE` to select that build cache. The helper wrapper
compiles with one job; no download or compilation occurs when the app starts.

On macOS, if the Electrobun 1.18.1 CLI is killed at launch and the build later
fails with `No macOS .app bundle found`, check the installed CLI's code signature.
For a trusted installation with an invalid signature, re-sign both installed
copies ad hoc (without a signing certificate), then build again. Run from the
repository root. Repeat after a reinstall if the signature failure returns:

```bash
for f in node_modules/.bun/electrobun@*/node_modules/electrobun/{bin,.cache}/electrobun; do
  [ -f "$f" ] && codesign --force --sign - "$f"
done
```
