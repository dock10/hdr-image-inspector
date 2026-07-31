# Media City Studios HDR Image Inspector

A browser-based tool that checks whether an image file is genuinely HDR or just claims to be. Drop a file in and it tells you straight away whether it is true HDR with real extra brightness, an HDR-format file with no actual headroom, PQ/HLG only, or plain SDR.

**Status:** Live (since 10 June 2026)
**Owner:** Andy Elliott
**Live URL:** https://dock10.github.io/hdr-image-inspector/
**AI involvement:** Development only. Claude was used as a coding assistant. The deployed tool makes no AI calls and no network calls of any kind.

## Why it exists

A lot of files carry HDR-sounding metadata or file extensions without any real HDR content, which causes confusion when checking source material or client deliverables. This tool gives a quick, reliable verdict on what a file actually contains. Everything runs locally in the browser, so no files ever leave the machine.

## How it works

The tool reads the raw bytes of the dropped file entirely in the browser (via the FileReader API) and applies deterministic checks. There is no server, no upload, no external API, and no machine learning.

1. **Format detection.** Identifies the file type from magic numbers rather than trusting the extension. Supports JPEG, PNG, GIF, WebP, AVIF, HEIC and TGA.
2. **Gain-map detection.** Scans the byte stream for known gain-map markers: Adobe/ISO `hdrgm`, Google `GContainer`, Apple `HDRGainMap`, MPF, and the ISO 21496-1 URN.
3. **Capacity extraction.** Pulls `HDRCapacityMax` out of any embedded XMP metadata using a regex.
4. **Verdict logic.**
   - Gain map present *and* a valid capacity value: **True HDR**, with a computed number of stops and a brightness multiplier.
   - Gain map present but no valid capacity: **HDR wrapper** (HDR format, no real headroom).
   - Strong PQ/HLG transfer markers alone: **PQ/HLG**.
   - None of the above: **SDR**.

## Repository layout

```
index.html    Entire application: HTML, CSS and JavaScript in one self-contained file.
```

That is the whole project. There are no build steps, no dependencies, no package manager, and no configuration.

## Running it locally

Because it is a single static file with no dependencies, any of these work:

- Open `index.html` directly in a browser (double-click), or
- Serve the folder with any static server, for example:
  ```
  python -m http.server 8000
  ```
  then visit `http://localhost:8000`.

No install, no environment variables, no config files.

## Deployment

Hosted on **GitHub Pages** from this repository. Pushing to the default branch publishes the site automatically. There is nothing else to deploy and nothing to restart. To point Pages at the repo (if it is ever re-created): Settings > Pages > deploy from branch, root of the default branch.

## Picking up development

The entire app is in `index.html`. The `<style>` block at the top holds all styling (CSS custom properties in `:root` control the colour theme). The `<script>` block holds the detection logic. To extend it:

- **Add a new file format:** extend the magic-number detection.
- **Add a new gain-map signature:** add the byte marker to the gain-map scan.
- **Adjust a verdict:** the verdict logic is the decision tree described above; the thresholds and messages live in the script block.

Because there is no build, editing the file and refreshing the browser is the full development loop.

## Known limitations and gotchas

- Detection is signature and metadata based, not a full pixel-level HDR analysis. A file with unusual or non-standard metadata may be classified conservatively.
- Capacity is read from embedded XMP; files that omit `HDRCapacityMax` will fall back to the "HDR wrapper" verdict even if they carry a gain map.
- Runs entirely client-side, so very large files are limited by the browser's memory.

## Next steps

None currently planned. The tool is considered complete and in active use.

## Contact

Andy Elliott, Lead Developer, dock10 (andy.elliott@dock10.co.uk).
