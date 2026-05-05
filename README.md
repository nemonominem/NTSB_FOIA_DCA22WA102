# NTSB DCA22WA102 — China Eastern Airlines Flight MU5735  
## Boeing 737-800 Flight into Terrain, Wuzhou, China — March 21, 2022

This repository contains Flight Data Recorder (FDR) readout data and Freedom of Information Act (FOIA) release documents related to the NTSB investigation of case **DCA22WA102**: the in-flight breakup and controlled-flight-into-terrain (CFIT) of China Eastern Airlines Flight MU5735.

> **About this repository**: This is a quick extract prepared by **Gilles Demaneuf** in a few hours, with minimal preparation of the data and some initial analysis done with the assistance of **Claude Sonnet 4.5**. The files are presented largely as-is from the NTSB FOIA release. Analysis notes are in [`analysis.md`](analysis.md).
>
> **Fork welcome**: Anyone is welcome to fork this repository, run their own analysis on the FDR data, and share findings. The CSV files are the most accessible starting point.

---

## Accident Summary

| Field | Details |
|---|---|
| **NTSB Case Number** | DCA22WA102 |
| **Date of Event** | March 21, 2022 |
| **Operator** | China Eastern Airlines (CES) |
| **Flight** | MU5735 |
| **Aircraft Type** | Boeing 737-800 |
| **Registration** | B-1791 |
| **Location** | Near Wuzhou, Guangxi, China |
| **Phase of Flight** | Cruise / Descent |
| **Outcome** | Fatal — all 132 occupants (123 passengers, 9 crew) perished |

On March 21, 2022, China Eastern Airlines Flight MU5735 — a Boeing 737-800 — departed Kunming Changshui International Airport (KMG) bound for Guangzhou Baiyun International Airport (CAN). At cruising altitude (~29,000 ft), the aircraft entered a sudden, near-vertical dive and impacted mountainous terrain near Wuzhou, Guangxi, China. There were no survivors.

The U.S. National Transportation Safety Board (NTSB) participated in the investigation under ICAO Annex 13, as Boeing (manufacturer) is a U.S. entity. The Chinese Civil Aviation Administration of China (CAAC) led the investigation.

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
| `DCA22WA102-220414-AllValidated-ExactSample.csv` | 35 MB | **FDR** | ~160 engineering-unit parameters in CSV (text) | Final ~13 min of accident flight — from cruise (29,000 ft) to FDR power loss (~26,000 ft) | Native sample rates (1–16 Hz per param); sparse rows. **Best for preserving timing fidelity.** |
| `DCA22WA102-220414-AllValidated-TableResolution.csv` | 3.4 MB | **FDR** | Same parameters, resampled to 16 Hz | Same ~13 min | Uniform 16 Hz grid — empty cells filled in. **Best for plotting and analysis.** Redundant with ExactSample. |
| `DCA22WA102-Final12minutes.upk` | 4.1 MB | **FDR** | NTSB CIDER binary (proprietary) | Final ~12 min of accident flight; **time-corrected** | Same data as the two CSV files, in CIDER binary format. Not directly readable without NTSB software or reverse-engineering. |
| `Last2Flights(final).upk` | 9.4 MB | **FDR** | NTSB CIDER binary (proprietary) | Accident flight + **preceding flight** | Has ~1.3 s gaps every ~6.5 s (from missing memory chip U2). Provides broader context but needs gap-aware handling. |
| `PreviousLanding+LastFlight_PartiallyCorrected(AsOf220414).upk` | 7.0 MB | **FDR** | NTSB CIDER binary (proprietary) | Previous landing + accident flight; partial timing correction | Intermediate work file as of 14 April 2022. Less reliable timing than Final12minutes. |
| `FOIA Release Records - DCA22WA102 (1).pdf` | 50 MB | **CVR + FDR** (report) | NTSB Combined CVR/FDR Download Report (July 2022, 33 pp.) | FDR: plots cover final 90 sec to power loss at ~26,000 ft. CVR: describes recovery process (no audio included). | **Primary technical reference.** Covers recorder damage, chip-off recovery process, FDR parameter plots (Figs 11–13), CVR quality tables, parameter validation appendix. CVR audio not included — provided to CAAC only. |
| `FOIA Release Records - DCA22WA102 (2).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (3).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (4).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (5).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (6).pdf` | 50 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (7).pdf` | 38 MB | Unknown | Investigation document (not reviewed in detail) | — | Part of 8-volume FOIA release |
| `FOIA Release Records - DCA22WA102 (8).pdf` | 31 MB | Neither (emails) | NTSB–CAAC email correspondence, April 2022 – March 2024 | Covers investigation coordination period + the March 2024 2-year FOIA trigger | **Key document for legal/FOIA context.** See `analysis.md §1–2`. |

> **CVR audio** (cockpit voice recordings): recovered at Excellent quality on all 4 channels by NTSB, but provided exclusively to CAAC. **Not included in this FOIA release and not accessible to the public.** The CVR is the only recorder that captured the full final dive and impact (see [Flight Recorders section](#flight-recorders--what-are-they) above).

> **For data analysis**, start with `DCA22WA102-220414-AllValidated-TableResolution.csv` — it is the most accessible, well-formatted file and is directly usable with pandas, Excel, or any standard data tool.

---

## File Descriptions

### Flight Data Recorder (FDR) — CSV Files

Both CSV files were created on **April 14, 2022** (file version `007-ParameterValidation`) and are labelled as "All Validated" data under project title:

> `B737-800_CES_DCA22WA102_GuangzhouChina_FlightIntoTerrain`

#### `DCA22WA102-220414-AllValidated-ExactSample.csv`
- **Size**: ~35 MB  
- **Rows**: ~198,035 (excluding headers)  
- **Format**: Each parameter is recorded at its **exact native sample rate** from the FDR, resulting in sparse rows (most cells empty at any given timestamp)
- **Time Range**: ~288,200 – 288,977 seconds (~777 seconds / ~13 minutes of flight data)
- **Parameters**: ~160 FDR parameters (see [Parameter List](#fdr-parameter-list) below)

#### `DCA22WA102-220414-AllValidated-TableResolution.csv`
- **Size**: ~3.4 MB  
- **Rows**: ~12,414  
- **Format**: The same FDR parameters resampled to a **common table resolution** (every 0.0625 seconds = 16 Hz), making it suitable for direct time-aligned analysis and plotting
- **Time Range**: Same as above (~288,200 – 288,978 seconds)
- **Parameters**: Identical parameter set to ExactSample

> **Which file to use?**  
> Use `TableResolution` for analysis, visualisation, and direct time-series work. Use `ExactSample` if you need to preserve the original sample rates and timing fidelity of each parameter.

### FDR Readout Package Files (`.upk`)

These are **NTSB CIDER "unpacked" binary files** — proprietary output from the NTSB's internal FDR analysis software (CIDER: Custom Interactive Data Editing and Replay). They contain decoded, time-tagged, engineering-unit FDR parameter data. The format is not publicly documented but can in principle be reverse-engineered. For most analysis purposes the CSV files are equivalent and far more accessible.

| File | Size | Description |
|---|---|---|
| `DCA22WA102-Final12minutes.upk` | 4.1 MB | **Time-corrected** final ~12 minutes of the accident flight — the analytically reliable portion |
| `Last2Flights(final).upk` | 9.4 MB | Last two flights recorded by the FDR (accident flight + preceding flight); contains ~1.3 s data gaps every ~6.5 s due to missing chip U2 |
| `PreviousLanding+LastFlight_PartiallyCorrected(AsOf220414).upk` | 7.0 MB | Previous landing and accident flight, partially timing-corrected as of April 14, 2022 |

See [`analysis.md § 6`](analysis.md#6-the-upk-files--ntsb-unpacked-fdr-data) for a full explanation of the `.upk` format and what each file contains.

### FOIA Release Records (PDFs)

Eight PDF documents (~388 MB total) released by the NTSB under the Freedom of Information Act. These are image-based scanned documents containing investigation materials such as FDR readout reports, engineering analysis, maintenance records, and other investigative attachments.

| File | Size |
|---|---|
| `FOIA Release Records - DCA22WA102 (1).pdf` | 50 MB |
| `FOIA Release Records - DCA22WA102 (2).pdf` | 50 MB |
| `FOIA Release Records - DCA22WA102 (3).pdf` | 50 MB |
| `FOIA Release Records - DCA22WA102 (4).pdf` | 50 MB |
| `FOIA Release Records - DCA22WA102 (5).pdf` | 50 MB |
| `FOIA Release Records - DCA22WA102 (6).pdf` | 50 MB |
| `FOIA Release Records - DCA22WA102 (7).pdf` | 38 MB |
| `FOIA Release Records - DCA22WA102 (8).pdf` | 31 MB |

---

## FDR Parameter List

The CSV files contain approximately **160 parameters** spanning electrical systems, flight dynamics, air data, engine parameters, flight controls, autopilot/FCC modes, FMC/navigation, and air-ground logic.

➜ See **[`parameters.md`](parameters.md)** for the full categorised parameter list and key parameter significance table.

---

## CSV Header Structure

The CSV files use a **4-row header**:

| Row | Content |
|---|---|
| Row 1 | Document metadata (`NTSB DCA22WA102, Attachment...`) |
| Row 2 | Event metadata (Operator, Registration, Aircraft type, Date, Location) |
| Row 3–10 | Project history fields (project title, scratch version, creation date) |
| Row 11 | `DATA` marker |
| Row 12 | **Parameter names** |
| Row 13 | **Parameter units** (e.g., `deg`, `kts`, `ft`, `%RPM`, `pph`, `g`, `deg/s`) |
| Row 14 | **Encoding definitions** (numeric encoding for discrete/Boolean parameters) |
| Row 15+ | **Time-series data** (first column is time in seconds) |

---

## Data Notes

- **Time** is recorded in seconds (absolute seconds, consistent with FDR internal clock)
- The data covers approximately **13 minutes** of flight — from cruise at 29,000 ft through the initial dive, **ending when the FDR lost power at ~26,000 ft** (both engine N2 values dropped below generator cutoff speed; the FDR has no battery backup). The CVR continued recording for at least 10 more minutes on battery power. See [`analysis.md §6`](analysis.md#6-the-upk-files--ntsb-unpacked-fdr-data).
- Many cells are empty in the `ExactSample` file — this is normal, as each parameter is only written at its own sample interval
- Discrete (on/off, engaged/not engaged) parameters are encoded as `0.0` / `1.0` with text labels defined in row 14
- The `ExactSample` file is approximately 10× larger than `TableResolution` due to the sparse encoding

---

## References

- [NTSB Accident Brief — DCA22WA102](https://www.ntsb.gov/investigations/Pages/DCA22WA102.aspx)
- [CAAC Preliminary Report (April 2022)](http://www.caac.gov.cn/)
- [China Eastern Airlines Flight MU5735 — Wikipedia](https://en.wikipedia.org/wiki/China_Eastern_Airlines_Flight_5735)
- NTSB FOIA Request Portal: https://www.ntsb.gov/foia/

---

## Disclaimer

This data was released publicly by the NTSB through FOIA. It is intended for research, educational, and investigative analysis purposes. The data pertains to a fatal accident; please handle it with appropriate sensitivity and respect for the victims and their families.
