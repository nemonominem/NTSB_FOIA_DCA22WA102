# FDR Parameter List — NTSB DCA22WA102

The CSV files (`DCA22WA102-220414-AllValidated-ExactSample.csv` and `DCA22WA102-220414-AllValidated-TableResolution.csv`) contain approximately **160 FDR parameters** across the following categories.

For CSV header structure and data notes, see [`README.md`](README.md).

---

## Electrical Systems
- 115 VAC Standby Bus, Transfer Bus
- 28 VDC Bus (multiple), Hot Battery Bus, Standby Bus, Switched Hot Battery Bus
- APU N1, APU On, APU Ready to Load
- Hydraulic System A/B (Electric & Engine-driven pumps), Standby Hydraulic

## Flight Dynamics
- **Pitch Angle**, **Roll Angle**, **Yaw Rate**, **Roll Rate** (absolute)
- **Acceleration**: Lateral, Longitudinal, Vertical
- Drift Angle (FMC)
- Track Angle True (FMC)

## Air Data
- **Airspeed** (Computed, Max Allowable, Target FCC, IAS Display, FMC Selected)
- **Altitude** (Pressure, Radio DEU, Radio-1, Radio-2, FMC Selected)
- Baro Corrections (Alt 1–4)
- Ground Speed (multiple sources)
- High Speed Buffet Speed
- Mach (Selected FCC, FMC Selected)

## Engine Parameters (Engine 1 & 2)
- N1 (%RPM): Actual, Command, Reference, Tach, FMC Bug Drive, FMC Target
- N2 (%RPM): Actual, Tach
- EGT (°C)
- Fuel Flow (pph)
- FMV Position (%)
- Oil Pressure (psi), Oil Quantity (qt), Oil Temperature (°C)
- TRA (Thrust Resolver Angle, deg)
- **Cutoff Switch status** ← key parameter: moved to CUTOFF at cruise
- Fire Detection

## Flight Controls
- **Aileron**: Actuator Position (L), Quadrant Position, Roll Command (L), L & R positions
- **Elevator**: Actuator Position (L), Pitch Command (L), L & R positions
- **Rudder**: Position (LVDT Demod Standby PCU), Servo Command (Standby PCU), Pedal Position
- **Flap**: Handle Position, L & R surface positions
- Control Column: Force (Pitch CWS — Foreign/Local), Position L & R
- Control Wheel: Force (Roll CWS), Position L & R

## Autopilot / Flight Management (FCC)
- CMD A (engaged), CMD A/B Lights
- AP Off warning, AP-1/AP-2 Warnings
- TOGA Engaged
- Single Channel Engaged
- FAC Engage
- MACH Trim (FCC-L and FCC-R in command), Servo Brake Status
- Flight Director switches (FD-A, FD-B)
- Local/Foreign Limited Master (FCC-L, FCC-R)

## Autoflight Modes
- ALT ACQ, ALT HOLD, LNAV, VNAV PATH, VNAV SPD, G/S (Glide Slope), LOC, HDG SELECT, HDG SEL Light, LVL Change, TOGA, N1 Mode, SPD Light, SPEED INTERVENTION
- GS Engaged, GP Engage
- AT FMC Speed Engaged
- Visual Altitude Alert

## FMC / Navigation
- FMC Valid
- FMC Selected Altitude, Airspeed, Mach
- Selected Altitude/Airspeed/Mach/Vertical Speed/Course (FCC — Local & Foreign)
- Active Altitude Reference (MCP Alt / FMC Alt)
- Wind Direction True, Wind Speed (FMC)
- Heading, Heading Selected (FCC)
- G/S Deviation Warning

## Air-Ground Logic
- Air-Ground (multiple: Air Gnd On Gnd, AIR GROUND SMYDC-2, Air-Ground)

---

## Validated Parameter Details (Appendix B)

The full validated parameter list from the NTSB Combined Download Report (PDF(1), Appendix B) includes precise engineering unit definitions, word/bit assignments, and scaling for each parameter in the ARINC 717 data frame. That appendix is the authoritative reference for decoding the raw FDR data stream.

Key parameters noted during NTSB analysis as significant:

| Parameter | Significance |
|---|---|
| `Eng1 Cutoff SW` / `Eng2 Cutoff SW` | Both moved RUN → CUTOFF at 29,000 ft cruise — **primary FDR finding** |
| `Eng1 N1` / `Eng2 N1` | Engine speed decay following fuel cutoff |
| `Eng1 N2 Actual` / `Eng2 N2 Actual` | N2 drop below generator cutoff speed → loss of AC generation |
| `Pitch Angle` | Rapid pitch-down from cruise to −80° |
| `Roll Angle` | Extreme roll approaching ±90° |
| `Altitude Press` | Rapid descent from ~29,000 ft to impact |
| `Ctrl Col Force Pitch CWS Local` / `Foreign` | Control inputs (or absence thereof) during upset |
| `Ctrl Whl Force Roll CWS` | Roll inputs during upset |
