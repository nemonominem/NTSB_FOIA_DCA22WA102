# NTSB DCA22WA102 — China Eastern Airlines Flight MU5735  
## Boeing 737-800 Flight into Terrain, Wuzhou, China — March 21, 2022

This repository contains Flight Data Recorder (FDR) readout data and Freedom of Information Act (FOIA) release documents from the NTSB investigation of **DCA22WA102**: the controlled-flight-into-terrain (CFIT) of China Eastern Airlines Flight MU5735.

> **About this repository**: Prepared by **Gilles Demaneuf** with initial analysis assisted by **Claude Sonnet 4.5**. Files are presented largely as-is from the NTSB FOIA release.
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

## Documentation

| File | Contents |
|---|---|
| [`data.md`](data.md) | What are FDRs and CVRs? What data is in each file? How to read the CSVs. Start here. |
| [`analysis.md`](analysis.md) | Detailed analysis of the FOIA documents: the recorder recovery, key FDR findings, the FOIA trigger, and the `.upk` file format. |
| [`parameters.md`](parameters.md) | Full list of ~160 FDR parameters recorded in the CSV files, with key parameter significance table. |

---

## References

- [NTSB Accident Brief — DCA22WA102](https://www.ntsb.gov/investigations/Pages/DCA22WA102.aspx)
- [China Eastern Airlines Flight MU5735 — Wikipedia](https://en.wikipedia.org/wiki/China_Eastern_Airlines_Flight_5735)
- NTSB FOIA Request Portal: https://www.ntsb.gov/foia/

### Downloading the Original FOIA Files

The FOIA release documents can be downloaded directly from the NTSB Reading Room:

> **URL**: https://securefoia.ntsb.gov/app/ReadingRoom.aspx  
> **Search**: enter `*DCA22WA102*` in the **Folder Name** field

> ⚠️ **Note**: Access may require a US IP address (a US-based VPN may be needed from outside the United States).

---

## Disclaimer

This data was released publicly by the NTSB through FOIA. It is intended for research, educational, and investigative analysis purposes. The data pertains to a fatal accident; please handle it with appropriate sensitivity and respect for the victims and their families.
