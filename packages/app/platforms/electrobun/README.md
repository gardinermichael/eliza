# @elizaos/electrobun

Native desktop shell for the elizaOS app on macOS, Windows, and Linux.

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
