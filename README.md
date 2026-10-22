# Captionney

**Arabic captions. Your style. Your story.**

Captionney is an Electron desktop application prototype for editing Arabic captions, applying animated styles, and rendering captioned MP4 video locally.

## Current status

This repository is an early working implementation, not a signed or production-verified installer. A `package-lock.json` could not be generated in the authoring environment because access to the npm registry timed out; the workflow uses `npm install` rather than `npm ci` until a lockfile is generated in a network-enabled environment. It includes the Electron shell, React editor, project JSON save/open, SRT export, local faster-whisper integration, ASS-based FFmpeg rendering, and a Windows x64 build workflow.

**Runtime dependencies note:** this first source release expects FFmpeg to be available as `ffmpeg` on PATH and Python with `faster-whisper` to be installed for transcription. The app reports these missing-component errors. The current installer configuration does not bundle FFmpeg, Python, or Whisper models; therefore do not distribute it as a self-contained end-user build until a packaging pass adds those runtimes. First model download needs internet access. Models are stored in the user's home cache and may use several GB for medium.

## Requirements for development

- Windows 10/11 x64 (or macOS/Linux for development)
- Node.js 20+
- npm
- FFmpeg on PATH (verify with `ffmpeg -version`)
- Python 3.10+ and `pip install faster-whisper` for transcription
- Internet access for npm packages and the first Whisper model download

## Run locally

```bash
npm install
npm test
npm run build
npm start

After dependencies install successfully, run `npm install --package-lock-only` and commit `package-lock.json` for reproducible installs.
```

For Vite hot reload in development, start Vite in one terminal with `npm run dev`, then set `VITE_DEV_SERVER_URL=http://localhost:5173` when launching Electron. A convenient two-process dev runner is not configured yet.

## Build the Windows installer

On a Windows x64 machine:

```powershell
npm install
npm test
npm run dist
```

The installer should be created under `release/` if the build succeeds. This has not been compiled in the authoring environment. The GitHub Actions workflow builds the installer on a Windows runner when triggered by a `v*` tag or manually.

## Use

1. Import a local video (MP4, MOV, MKV, WEBM, AVI).
2. Choose Arabic or English and a Whisper model.
3. Generate captions, then correct text and timings.
4. Select a preset and tune typography, highlighting, and motion.
5. Save a `.captionney.json` project or export SRT.
6. Export a burned-in MP4 through FFmpeg.

## Known limitations to resolve before production

- The renderer's preview animation is illustrative; exported rendering uses ASS subtitle effects and is not pixel-identical to the preview.
- Whisper word timestamps are used when available; segment fallback is static and doesn't invent word timings.
- Signature Kinetic renders individual word-timed events, but its current ASS output uses per-word dialogue events and should be checked with real videos for overlaps/wrapping.
- FFmpeg progress is currently exposed as diagnostic text, not a normalized percentage; cancellation is not yet implemented.
- Python and FFmpeg are external development prerequisites and are not bundled into the installer yet.
- Automatic alignment correction, undo/redo controls, robust autosave/recovery, media relinking, and thorough Windows runtime tests are not implemented yet.
- ASS filter quoting for unusual Windows paths should be validated on the target FFmpeg build.
- External Google Fonts import is only a design fallback; system fonts are used when offline, but the UI will not load those web fonts offline.

## Project format

Project files are JSON with `schemaVersion`, `title`, `sourceVideo`, `captions`, `style`, `export`, and `updatedAt`. Source media is referenced by path, not embedded.

## Publish on GitHub

1. Create an empty repository named `Captionney`.
2. Extract this ZIP and open PowerShell in the project directory.
3. Run `git init`, `git add .`, `git commit -m "Initial Captionney source"`.
4. Add the repository URL with `git remote add origin https://github.com/YOUR-USERNAME/Captionney.git`.
5. Push with `git branch -M main` and `git push -u origin main`.
6. Create and push a release tag, e.g. `git tag v0.1.0` and `git push origin v0.1.0`. GitHub Actions will build and attach the installer if the workflow succeeds.

## License

MIT. See [LICENSE](LICENSE).
