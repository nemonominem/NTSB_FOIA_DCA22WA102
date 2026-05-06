# CLAUDE.md — Project Working Guide
## NTSB DCA22WA102 · China Eastern Airlines Flight MU5735

This file captures working conventions, architecture decisions, and maintenance rules for this repository. It applies to Claude Code CLI, GitHub Copilot, and any AI assistant working in this project.

---

## Project Purpose

Analysis of the NTSB FOIA release for DCA22WA102 — the controlled-flight-into-terrain of China Eastern Airlines Flight MU5735 (Boeing 737-800, Wuzhou, China, 21 March 2022, 132 fatalities). The repository contains FDR data, FOIA documents, and an interactive data explorer.

---

## Key Files

| File | Role |
|---|---|
| `EDA_1.html` | **Primary deliverable.** Self-contained interactive FDR explorer. Full CSV embedded as gzip+base64. Do not break the embedded data block. |
| `README.md` | Repository landing page. Keep the GitHub Pages link and documentation table up to date. |
| `data.md` | Data guide: FDR/CVR explainer, CSV structure, invalid word values, chip U2 dropout description. |
| `observables.md` | Per-parameter reference: physical definitions, sample rates, units, invalid words, analysis notes. |
| `analysis.md` | Detailed FOIA document analysis: recorder recovery, key FDR findings, FOIA timeline, `.upk` format. |
| `support/figures/` | Static PNG charts (matplotlib) referenced from `analysis.md`. |
| `parameters.md` | **Superseded** by `observables.md` — do not update, keep for legacy link compatibility only. |

---

## EDA_1.html — Architecture

### Version number
The version is embedded in two places — keep them in sync on every release:
- `<title>` tag: `MU5735 FDR Data Explorer — NTSB DCA22WA102 — vX.Y.Z`
- `<h1>` span: `vX.Y.Z`

Current version: **v1.1.0**

### Versioning convention
Use `major.minor.patch`:
- **patch** (1.0.x): bug fixes, sentinel/invalid-word list updates, cosmetic tweaks
- **minor** (1.x.0): new preset views, new chart types, new UI features
- **major** (x.0.0): data change (new CSV embedded), architectural rewrite, breaking changes

### Embedded data
Line ~503 contains `const EMBEDDED_B64 = "..."` — a gzip-compressed, base64-encoded copy of `DCA22WA102-220414-AllValidated-TableResolution.csv`. This line is ~340 KB. **Never modify it during JS/HTML patches** — always verify it is intact after any patch script by checking `'EMBEDDED_B64' in src` and that the line length is > 300,000 chars.

### Patching approach
The file is too large to edit with line-based tools. Always patch via Python `str.replace()` on the full file content, preserving the data blob. After patching, run verification checks before writing.

### JS architecture (key globals)
| Symbol | Purpose |
|---|---|
| `D` | Raw data rows (array of arrays) |
| `PN`, `PU`, `ENC_MAP` | Parameter names, units, encoding maps |
| `TX` | Time axis, shifted so T=0 = FDR power cutoff |
| `SENTINELS` | Global Set of known ARINC bus invalid-word values |
| `COL_OVERRIDES` | Per-column invalid values (cannot go in global set) |
| `COL_SENTINELS` | Statistical outlier detection per column (built at load) |
| `GAP_ROWS` | Uint8Array marking chip U2 dropout rows (built at load) |
| `SAMPLE_HZ` | Native FDR sample rate per column (built at load) |
| `c1` | Main Chart.js chart instance |
| `stackedCharts` | Array of stacked sub-chart instances |
| `sel` | Currently selected parameter indices |
| `defaultXMin` | Default x-axis left edge for Reset Zoom |

### Sync
`syncXAxis(min, max, source)` propagates x-axis zoom/pan to all charts (main + stacked), skipping the originating chart to avoid loops. Called from `onZoomComplete` / `onPanComplete` callbacks on every chart, and from `resetZoom()`.

### Invalid words vs. chip gaps
Two distinct data quality issues — do not conflate them:
- **ARINC bus invalid words**: fixed max-word constants (e.g. 127.88, 1023.0) output by the aircraft bus when a parameter word is stale. Occur on a fixed ~8s cadence. Filtered by `SENTINELS` + `COL_OVERRIDES` + `COL_SENTINELS`. UI checkbox: "Filter invalid words".
- **Chip U2 dropouts**: physical chip U2 was destroyed at impact. The NTSB substituted `0xFF`. In the CSV this produces rows where ~85%+ of continuous parameters are simultaneously empty, once every ~6.5s for ~1.3s. Detected by `buildGapMask()`, suppressed unconditionally in `getVals()` to break chart lines (not interpolated through).

### Impulse filter — two branches in getVals()
- **Continuous params**: window-3 MAD filter (Tukey k=3). Isolated samples where `|v[i] − median| > 3×MAD` with at least one normal neighbour are nulled.
- **Discrete (encoded) params**: native-sample run-length filter via `NATIVE_IDX`. Forward-fill expands a 1-sample native spike (1 Hz) into 16 filled positions, defeating a point-isolation test. Instead: on the native-rate sample sequence, any run of the minority state ≤ 5 native samples surrounded by the same state is nulled across the full corresponding filled range. 5 native samples at 1 Hz = 5 s — real mode changes in cruise last minutes. Built by `buildNativeIdx()` at load, called after `buildGapMask()`.

---

## Data Files

### CSV files (in `DCA22WA102 3_21_2022 Wuzhou, China/`)
- `DCA22WA102-220414-AllValidated-TableResolution.csv` — **use this for analysis** (16 Hz resampled grid, ~3.4 MB)
- `DCA22WA102-220414-AllValidated-ExactSample.csv` — native sample rates, sparse rows (~35 MB)

### FOIA PDF documents (in `DCA22WA102 3_21_2022 Wuzhou, China/`)
- `FOIA Release Records - DCA22WA102 (1).pdf` — **primary technical reference**: CVR/FDR Combined Download Report (33 pp., July 2022). Contains recorder damage, chip-off recovery, FDR parameter plots, CVR quality tables, validated parameter list (Appendix B).
- `FOIA Release Records - DCA22WA102 (8).pdf` — NTSB–CAAC email correspondence (FOIA trigger document).
- Files (2)–(7) — other investigation documents (not fully reviewed).

### OCR'd PDFs (uploads directory)
The original FOIA PDFs are image-based (scanned) and not text-searchable. OCR-compressed versions are stored in the uploads directory used by Claude sessions:
- `FOIA Release Records - DCA22WA102 (1)-ocr-compressed.pdf` — OCR version of PDF(1), text-extractable via `pdftotext` or `pdfminer`.

If you need to re-extract text from the PDFs, use `pdfminer.six` (installed) rather than `pdftotext` (returns empty for image PDFs) or `pypdf` (also returns empty).

### `.upk` files
NTSB CIDER proprietary binary format. Not directly readable without NTSB software. See `analysis.md §6` for format details and options.

---

## GitHub Pages

The repository is published at:
**`https://nemonominem.github.io/NTSB_FOIA_DCA22WA102/EDA_1.html`**

GitHub Pages is enabled on the `main` branch. After any commit that modifies `EDA_1.html`, push to `main` and the live page updates automatically (typically within ~60 seconds).

The NTSB FOIA reading room requires a US IP address. A US VPN may be needed to download the original source files from `https://securefoia.ntsb.gov/app/ReadingRoom.aspx` (search `*DCA22WA102*`).

---

## Terminology

Use these terms consistently across all files:

| Term | Meaning | Do NOT use |
|---|---|---|
| **Invalid word** | ARINC bus max-word value (stale/unavailable parameter) | "sentinel" (informal; not used in NTSB report) |
| **Chip U2 dropout** | ~1.3s data gap every ~6.5s from destroyed flash chip | "sentinel gap", "missing data spike" |
| **Forward-fill** | Step-hold resampling from native rate to 16 Hz grid | "interpolation" (it is not interpolated) |
| **FDR cutoff (T=0)** | Time reference point: when FDR lost power (~26,000 ft) | "crash time", "impact time" (impact was ~10 min later) |

---

## Analysis Notes

- **CVR**: recovered at Excellent quality on all 4 channels. Provided exclusively to CAAC. Not in this FOIA release. The NTSB stated it retained no CVR audio files — technically implausible given the multi-step chip-off recovery workflow that necessarily produces intermediate files (see `analysis.md` for the full argument).
- **FDR stops at ~26,000 ft**: both engine N2 speeds dropped below generator cutoff threshold as engines spooled down after fuel cutoff. FDR has no battery backup. Aircraft continued ~10 more minutes before impact.
- **Fuel cutoff**: both `Eng1 Cutoff SW` and `Eng2 Cutoff SW` moved from RUN → CUTOFF at cruise altitude (~29,000 ft). This is the primary FDR finding.
- **N1 display lag**: apparent ~1–2s flat line in N1 after cutoff is a forward-fill display artifact, not a real delay. N1 decay begins immediately; the 1 Hz sampling means the next valid post-cutoff sample arrives up to ~2.6s later.
- **Roll wrap**: `Roll Angle` spans ±180°. A jump from ~+175° to ~−175° around T=−11s is a valid coordinate wrap (aircraft near-inverted), not a data error.

---

## Python Figure Lessons — Correct Display of FDR Data

### Forward-fill before plotting continuous lines

The `TableResolution.csv` is a 16 Hz grid but parameters are recorded at native rates (1–4 Hz). Most rows are NaN for any given parameter — typically 75% empty. **Plotting without forward-filling produces an invisible or near-invisible line** because matplotlib only connects the sparse non-null points with no fill between them.

**Rule**: always forward-fill (step-hold) before plotting any parameter as a continuous line. Use raw (no fill) only for dot/scatter plots of native samples.

```python
def get_num_filled(col, bad=None):
    raw = pd.to_numeric(df[col][mask], errors='coerce').values.copy().astype(float)
    if bad:
        for b in bad: raw[raw == b] = np.nan
    last = np.nan
    for i in range(len(raw)):
        if not np.isnan(raw[i]): last = raw[i]
        elif not np.isnan(last): raw[i] = last
    return raw
```

### Y-axis range must match actual data range

If the axis range is set much wider than the data (e.g. 0–32,000 ft for altitude that only spans 25,000–29,000 ft), the line appears as a thin band and is effectively invisible. Always set y-limits from the data:

```python
av = alt[~np.isnan(alt)]
ax.set_ylim(av.min() - 800, av.max() + 800)
```

### Shared y-axis hides data when ranges differ by orders of magnitude

Plotting pitch (−36° to +2.5°) and roll (−177° to +176°) on the same axes causes pitch to appear as a flat hairline near zero. **Use separate sub-panels** for parameters whose ranges differ significantly — one panel per parameter is always safer than twinx for attitude data.

### Invalid sentinel values for this dataset

Key sentinels to mask before plotting:

| Parameter | Invalid value | Reason |
|---|---|---|
| `Altitude Press` | −1.0, 0.0 | ARINC invalid / chip dropout |
| `Airspeed Comp` | 511.75 | 10-bit max (1023.5) scaled by 0.5 |
| `Eng1/2 N1` | 127.88, 127.875 | 7-bit max ARINC word |
| `Eng1/2 Fuel Flow` | 16368.0 | 14-bit near-max fuel flow word |
| `Pitch/Roll Angle` | −0.18 (in cruise only) | ARINC invalid word — use run-length filter, not global mask (value is legitimate during dive) |

### Dark theme colours that are visible against `#161b22` background

| Use | Colour |
|---|---|
| Blue series | `#58a6ff` |
| Orange series | `#ffa657` |
| Green series | `#56d364` |
| Purple series | `#d2a8ff` |
| Amber/accel | `#e3b341` |
| Grid lines | `#30363d` |
| Text | `#c9d1d9` |
| Cutoff line | `#ff6b6b` |
