# NTSB DCA22WA102 — China Eastern Airlines Flight MU5735  
## Boeing 737-800 Flight into Terrain, Wuzhou, China — March 21, 2022

This repository contains Flight Data Recorder (FDR) readout data and Freedom of Information Act (FOIA) release documents from the NTSB investigation of **DCA22WA102**: the controlled-flight-into-terrain (CFIT) of China Eastern Airlines Flight MU5735.

> **About this repository**: Prepared by **Gilles Demaneuf** with initial analysis, assisted by **Claude Sonnet 4.6** (paying), and **DeepSeek V4 Flash** (free), in VSCode via Cline. Files are presented largely as-is from the NTSB FOIA release.
>
> **Fork welcome** — anyone is welcome to fork, analyse the FDR data, and share findings. Start with [`data.md`](data.md) for orientation.

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

On March 21, 2022, China Eastern Airlines Flight MU5735 departed Kunming (KMG) bound for Guangzhou (CAN). At cruising altitude (~29,000 ft), the aircraft entered a sudden near-vertical dive and impacted mountainous terrain near Wuzhou. There were no survivors.

The U.S. National Transportation Safety Board (NTSB) participated in the investigation under ICAO Annex 13, as Boeing is a U.S. entity. The Chinese Civil Aviation Administration of China (CAAC) led the investigation.

---

## Interactive FDR Explorer

**[→ Open EDA_1.html in browser](https://nemonominem.github.io/NTSB_FOIA_DCA22WA102/EDA_1.html)**

A self-contained interactive explorer for the FDR time-series data. The full CSV is embedded — no server required. Includes preset views for engine cutoff, flight path, crew inputs, attitude rates, electrical, hydraulic, and autopilot systems. Zoom and pan, select parameters, and explore the last 13 minutes of recorded data.

---

## Documentation

| File | Contents |
|---|---|
| [`data.md`](data.md) | What are FDRs and CVRs? What data is in each file? How to read the CSVs. Start here. |
| [`analysis.md`](analysis.md) | Detailed analysis of the FOIA documents: the recorder recovery, key FDR findings, the FOIA trigger, and the `.upk` file format. |
| [`analysis.pdf`](analysis.pdf) | Same as above, converted to a pdf using a generic Python script saved in the repo. |
| [`observables.md`](observables.md) | Full list of ~160 FDR parameters: physical definitions, sample rates, units, discrete vs. continuous classification, and key analysis notes. |
| [`EDA_1.html`](EDA_1.html) | Self-contained interactive FDR data explorer (CSV embedded). |

---

## References

- [Wall Street Journal article about the FOIA request — archived (3 May 2026)](https://archive.ph/ysqJH#selection-1823.0-1823.271)
- [China Eastern Airlines Flight MU5735 — Wikipedia](https://en.wikipedia.org/wiki/China_Eastern_Airlines_Flight_5735)

### Downloading the Original FOIA Files

The FOIA release documents can be downloaded directly from the NTSB Reading Room:

> **URL**: https://securefoia.ntsb.gov/app/ReadingRoom.aspx  
> **Search**: enter `*DCA22WA102*` in the **Folder Name** field

> **Note**: Access may require a US IP address (a US-based VPN may be needed from outside the United States).

---

## Disclaimer

This data was released publicly by the NTSB through FOIA. It is intended for research, educational, and investigative analysis purposes. The data pertains to a fatal accident; please handle it with appropriate sensitivity and respect for the victims and their families.