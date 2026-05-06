# Data Guide — NTSB DCA22WA102

This document explains the flight recorders involved, what data is in each file in this repository, and how to work with the data.

---

## Flight Recorders — What Are They?

Commercial aircraft are required to carry two "black boxes" (they are actually bright orange to aid recovery): the **Flight Data Recorder (FDR)** and the **Cockpit Voice Recorder (CVR)**. Both are housed in crash-survivable enclosures designed to withstand extreme impact forces, heat, and water pressure.

### Flight Data Recorder (FDR)

The FDR continuously records hundreds of aircraft parameters — altitude, airspeed, heading, pitch and roll attitude, engine thrust, control surface positions, autopilot status, fuel switch positions, and many more — typically at rates of once per second to 16 times per second per parameter. In modern aircraft, FDRs must retain at least 25 hours of data. When the loop fills, older data is overwritten.

On the Boeing 737-800, the FDR is powered by the aircraft's **AC electrical buses**, which are driven by the engine-mounted generators. **It has no independent battery backup.** This means that if both engines lose power and the generators go offline, the FDR stops recording.

### Cockpit Voice Recorder (CVR)

The CVR records audio from 4 channels: the Captain's headset/microphone, the First Officer's headset/microphone, a third crew observer position, and a cockpit area microphone (CAM) that picks up ambient sounds — switch clicks, warnings, conversations, and external noise. Modern CVRs retain at least 2 hours of audio.

Unlike the FDR, the CVR is powered from the aircraft's **Hot Battery Bus** — a direct connection to the aircraft battery that remains live even when all generators are offline. **It has battery backup of at least 10 minutes.** This means the CVR continues recording even after total electrical failure from engine shutdown.

### Why This Matters for MU5735

In the MU5735 accident both engines were cut off at cruise altitude. As the engines wound down, the AC generators went offline, and **the FDR stopped recording at approximately 26,000 ft** — well before the aircraft impacted terrain. The CVR, running on battery power, **continued recording for at least 10 more minutes**, capturing the full dive and impact sequence. The CVR audio was provided exclusively to the Chinese investigation authority (CAAC) and is not included in this FOIA release.

The FDR data in this repository therefore covers only the period from cruise through the initial dive to ~26,000 ft (~12–13 minutes total).

---

## Repository Contents — Data Overview

| File | Size | Recorder | Data type | Time coverage | Quality / Notes |
|---|---|---|---|---|---|
| `DCA22WA102-220414-AllValidated-ExactSample.csv` | 35 MB | **FDR** | ~160 engineering-unit parameters in CSV (text) | Last ~13 min of **FDR recording** — from cruise (~29,000 ft) until FDR lost power (~26,000 ft). Does **not** cover the final ~10 min of the flight. | Native sample rates (1–16 Hz per param); sparse rows. **Best for preserving timing fidelity.** |
| `DCA22WA102-220414-AllValidated-TableResolution.csv` | 3.4 MB | **FDR** | Same parameters, resampled to 16 Hz | Same ~13 min of FDR recording | Uniform 16 Hz grid — empty cells filled in. **Best for plotting and analysis.** Redundant with ExactSample. |
| `DCA22WA102-Final12minutes.upk` | 4.1 MB | **FDR** | NTSB CIDER binary (proprietary) | Last ~12 min of **FDR recording** (cruise to ~26,000 ft); **time-corrected**. The aircraft continued flying for ~10 more minutes after the FDR stopped. | Same data as the two CSV files, in CIDER binary format. Not directly readable without NTSB software or reverse-engineering. |
| `Last2Flights(final).upk` | 9.4 MB | **FDR** | NTSB CIDER binary (proprietary) | FDR-recorded portion of accident flight + **preceding flight**. Stops where the accident flight FDR recording stops (~26,000 ft). | Has ~1.3 s gaps every ~6.5 s (from missing memory chip U2). Provides broader context but needs gap-aware handling. |
| `PreviousLanding+LastFlight_PartiallyCorrected(AsOf220414).upk` | 7.0 MB | **FDR** | NTSB CIDER binary (proprietary) | FDR-recorded portion of previous landing + accident flight; partial timing correction | Intermediate work file as of 14 April 2022. Less reliable timing than Final12minutes. |
| `FOIA Release Records - DCA22WA102 (1).pdf` | 50 MB | **CVR + FDR** (report) | NTSB Combined CVR/FDR Download Report (July 2022, 33 pp.) | FDR: plots cover final 90 sec to power loss at ~26,000 ft. CVR: describes recovery process (no audio included). | **Primary technical reference.** Covers recorder damage, chip-off recovery process, FDR parameter plots, CVR quality tables, parameter validation appendix. CVR audio not included — provided to CAAC only. |
| `FOIA Release Records - DCA22WA102 (2).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (3).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (4).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (5).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (6).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (7).pdf` | 38 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (8).pdf` | 31 MB | Neither (emails) | NTSB–CAAC email correspondence, April 2022 – March 2024 | Covers investigation coordination period + the March 2024 2-year FOIA trigger | **Key document for legal/FOIA context.** See [`analysis.md`](analysis.md). |

> **CVR audio** (cockpit voice recordings): recovered at Excellent quality on all 4 channels by NTSB, but provided exclusively to CAAC. **Not included in this FOIA release and not accessible to the public.** The CVR is the only recorder that captured the full final dive and impact.

> **For data analysis**, start with `DCA22WA102-220414-AllValidated-TableResolution.csv` — it is the most accessible, well-formatted file and is directly usable with pandas, Excel, or any standard data tool.
>
> **Interactive browser**: [EDA_1.html](https://nemonominem.github.io/NTSB_FOIA_DCA22WA102/EDA_1.html) — self-contained FDR explorer with the full CSV embedded. No setup required.

---

## CSV File Details

Both CSV files were created on **April 14, 2022** (file version `007-ParameterValidation`) and are labelled as "All Validated" data under project title:

> `B737-800_CES_DCA22WA102_GuangzhouChina_FlightIntoTerrain`

### `DCA22WA102-220414-AllValidated-ExactSample.csv`
- **Size**: ~35 MB
- **Rows**: ~198,035 (excluding headers)
- **Format**: Each parameter is recorded at its **exact native sample rate** from the FDR, resulting in sparse rows (most cells empty at any given timestamp)
- **Time Range**: ~288,200 – 288,977 seconds (~777 seconds / ~13 minutes of flight data)
- **Parameters**: ~160 FDR parameters (see [`observables.md`](observables.md))

### `DCA22WA102-220414-AllValidated-TableResolution.csv`
- **Size**: ~3.4 MB
- **Rows**: ~12,414
- **Format**: The same FDR parameters resampled to a **common table resolution** (every 0.0625 seconds = 16 Hz), making it suitable for direct time-aligned analysis and plotting
- **Time Range**: Same as above (~288,200 – 288,978 seconds)
- **Parameters**: Identical parameter set to ExactSample

> **Which file to use?**  
> Use `TableResolution` for analysis, visualisation, and direct time-series work. Use `ExactSample` if you need to preserve the original sample rates and timing fidelity of each parameter.

### CSV Header Structure

The CSV files use a multi-row header before the time-series data begins:

| Row | Content |
|---|---|
| Row 1 | Document metadata (`NTSB DCA22WA102, Attachment...`) |
| Row 2 | Event metadata (Operator, Registration, Aircraft type, Date, Location) |
| Rows 3–10 | Project history fields (project title, scratch version, creation date) |
| Row 11 | `DATA` marker |
| Row 12 | **Parameter names** |
| Row 13 | **Parameter units** (e.g., `deg`, `kts`, `ft`, `%RPM`, `pph`, `g`, `deg/s`) |
| Row 14 | **Encoding definitions** (numeric encoding for discrete/Boolean parameters) |
| Row 15+ | **Time-series data** (first column is time in seconds) |

### Data Notes

- **Time** is recorded in seconds (absolute, consistent with FDR internal clock)
- The data covers approximately **13 minutes** of flight — from cruise at 29,000 ft through the initial dive, **ending when the FDR lost power at ~26,000 ft**. The CVR continued recording for at least 10 more minutes on battery power.
- Many cells are empty in the `ExactSample` file — this is normal; each parameter is only written at its own sample interval
- Discrete (on/off, engaged/not engaged) parameters are encoded as `0.0` / `1.0` with text labels defined in row 14
- The `ExactSample` file is approximately 10× larger than `TableResolution` due to sparse encoding

### Invalid-Word Values (chip U2 dropouts)

Two distinct types of bad values appear in the CSV:

**1. Chip U2 dropouts.** Per the NTSB Combined Download Report (§2.2), FDR flash memory chip U2 was physically destroyed by the impact. The data stream is striped across 6 chips at ~1.3 s per chip per cycle. With U2 missing, a ~1.3 s gap appears every ~6.5 s throughout the recording. The NTSB substituted `0xFF` bytes for the missing chip and time-corrected the stream. In the CSV, these dropout intervals appear as rows where most continuous parameters are simultaneously empty — the EDA_1 explorer detects and gaps these rows to avoid bridging across them.

**2. ARINC bus max-word values ("invalid words").** A separate issue: for some parameters, individual samples are replaced by the ARINC/BCD bus maximum value — a fixed constant the bus outputs when a parameter word has not been refreshed or is otherwise unavailable. They are **not** related to chip U2; they originate in the aircraft bus before the FDR even records them. In the ARINC 717 standard these are called "invalid words." The NTSB's own FDR plots exclude them.

**Key characteristics — read before interpreting plots:**

- **Not synchronous across parameters.** Each parameter occupies a fixed slot within the FDR's 512-word subframe and receives an invalid word at its own phase offset. Eng1 N1 may be invalid at T=−32.0s while Eng2 N1 is valid, and vice versa. When multiple parameters are plotted together, filtered gaps in one series will not align with filtered gaps in another, producing brief apparent divergences between otherwise correlated parameters (e.g. Eng1 N1 vs Eng2 N1 appearing to separate momentarily). These are artifacts.
- **Approximate 8-second cadence, not exact.** The repeat interval corresponds to the 512-word FDR subframe cycle at 64 words/second (= 8 s), but the precise timing depends on each parameter's position within the frame and the frame's relationship to the original recording clock. Do not treat the 8 s figure as a hard period.
- **Not every sample in a parameter is affected.** The invalid-word fraction varies widely across parameters (13%–50% of samples for the most affected) and is not uniformly distributed through time.

The invalid words correspond to saturated ARINC 429 / BCD bus words at common word widths:

| Invalid word value | Word width / pattern |
|---|---|
| 127.0, 127.875, 127.88 | 7-bit max (`0x7F`) at various scale factors |
| 127.5 | 7-bit max, half-step resolution |
| 255.0 | 8-bit max (`0xFF`) |
| 1023.0, 1023.5 | 10-bit max (`0x3FF`) |
| 4092.0, 4095.0 | 12-bit max (`0xFFF`) |
| 16368.0 | 14-bit near-max (fuel flow word) |
| 65520.0, 65535.0 | 16-bit max (`0xFFFF`) |

Affected columns and invalid-word frequency:

| Parameter | Invalid word | Affected samples |
|---|---|---|
| Eng1 N1 | 127.88 | 175 / 775 (22%) |
| Eng2 N1 | 127.88 | 98 / 775 (13%) |
| Eng1 N1 Cmd | 127.875 | 175 / 775 (22%) |
| Eng2 N1 Cmd | 127.875 | 98 / 775 (13%) |
| Eng1 N1 Tach | 127.875 | 80 / 195 (41%) |
| Eng2 N1 Tach | 127.875 | 98 / 195 (50%) |
| Eng1 N2 Actual | 127.875 | 114 / 775 (15%) |
| Eng2 N2 Actual | 127.875 | 98 / 774 (13%) |
| Eng1 FMC N1 Target | 127.0 | 175 / 775 (22%) |
| Eng2 FMC N1 Target | 127.0 | 98 / 775 (13%) |
| Eng1 FMC N1 Bug Drive | 127.0 | 80 / 389 (21%) |
| Eng2 FMC N1 Bug Drive | 127.0 | 95 / 386 (25%) |
| APU N1 | 127.5 | 98 / 195 (50%) |
| Eng1 EGT | 1023.0 | 175 / 775 (22%) |
| Eng2 EGT | 1023.0 | 114 / 775 (15%) |
| Eng1 Oil Press | 1023.0 | 95 / 192 (49%) |
| Airspeed Max Allowable | 1023.0 | 98 / 195 (50%) |
| Ground Spd | 1023.5 | 579 / 3100 (19%) |
| Groundspeed Disp -L | 1023.5 | 192 / 775 (25%) |
| Hyd Oil Press - A | 4092.0 | 175 / 775 (22%) |
| Hyd Oil Press - B | 4092.0 | 114 / 775 (15%) |
| Eng1 Fuel Flow | 16368.0 | 175 / 775 (22%) |
| Eng2 Fuel Flow | 16368.0 | 98 / 775 (13%) |
| FMC Selected Altitude | 65520.0 | 175 / 775 (22%) |
| Selected Altitude FCC | 65520.0 | 98 / 775 (13%) |

**Handling**: reject any value in the invalid-word set before computing statistics, plotting, or fitting. Do not forward-fill through them — treat them as missing. The EDA_1 explorer applies this filter automatically (checkbox: "Filter invalid words"). Note that even with filtering enabled, brief apparent divergences between correlated parameters will remain due to the non-synchronous phase offsets described above — these are artifacts, not real events.

---

## `.upk` File Details

See [`analysis.md §6`](analysis.md#6-the-upk-files--ntsb-unpacked-fdr-data) for a full explanation of the CIDER binary format, what each file contains, and options for reading them.

---

## FDR Parameter List

See [`observables.md`](observables.md) for the full parameter reference: physical definitions, sample rates, units, discrete vs. continuous classification, and analysis notes.
