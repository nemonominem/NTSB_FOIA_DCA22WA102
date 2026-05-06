# FDR Observables — NTSB DCA22WA102

This file describes the ~160 Flight Data Recorder (FDR) parameters recorded in the CSV files for DCA22WA102. It covers what each parameter measures, how it is sampled, and what units it is reported in.

For CSV structure and sentinel/invalid-word handling, see [`data.md`](data.md).

---

## How FDR Sampling Works

The Boeing 737-800 FDR records parameters at their own **native sample rates** — not all at the same rate. A parameter like vertical acceleration is safety-critical and changes rapidly, so it is sampled at 16 Hz (once every 62.5 ms). A parameter like engine oil temperature changes slowly, so it is sampled at 0.25 Hz (once every 4 seconds).

The raw FDR data stream (ARINC 717 format) is a fixed-rate multiplex frame that runs at 64 words per second. Each parameter occupies one or more slots in this frame at a fixed sub-frame rate. The NTSB decommutation software extracts each parameter at its native rate into the `ExactSample` CSV (sparse rows, each parameter only present when sampled), and then resamples everything to a common 16 Hz grid in the `TableResolution` CSV by **step-hold (forward-fill)**: the last known value is repeated until the next real sample arrives.

**Consequence**: in the `TableResolution` CSV and in the EDA_1 explorer, a flat segment between two visible sample points means *no new data was received in that interval*, not that the value was constant. A 1 Hz parameter like engine N1 will be held for up to ~1 second between real samples. After an event like fuel cutoff, the displayed value may not change for up to one full sample interval even if the physical quantity has already begun to change.

### Sample rates present in this dataset

| Rate (Hz) | Interval | Parameters |
|---|---|---|
| 16 | 62.5 ms | Lateral, longitudinal, vertical acceleration |
| 8 | 125 ms | Control column/wheel forces and positions, elevator/aileron/rudder positions, roll rate, yaw rate, absolute roll rate |
| 4 | 250 ms | Pitch angle, roll angle, altitude, airspeed, heading, elevator/aileron commands, TRA, ground speed, rudder standby |
| 2 | 500 ms | Autopilot discrete flags (CMD A, AP Off, FD switches, warnings) |
| 1 | 1 s | Engine N1/N2/EGT/fuel flow/cutoff switches, FMC data, flap position, hydraulic pressures, electrical bus discretes, most autoflight mode flags |
| 0.5 | 2 s | FMC N1 Bug Drive (Eng1 & Eng2) |
| 0.25 | 4 s | Engine oil/tach/FMV, baro corrections, hydraulic quantities, FMC ground speed, high-speed buffet speed, track angle |
| 0.125 | 8 s | APU N1, max allowable airspeed, engine oil pressure (Eng1), N2 tach (Eng2), ignition bus flags, MACH trim servo brake |
| 0.02 | 50 s | AIR GROUND – SMYDC-2 (weight-on-wheels from secondary source) |

---

## Discrete vs. Continuous Parameters

**Continuous** parameters are engineering-unit measurements (degrees, knots, %RPM, g, etc.) recorded as floating-point values.

**Discrete** parameters are binary or enumerated states (ON/OFF, ENGAGED/DISENGAGED, etc.) encoded in the FDR as integer values and decoded by the NTSB software into labels. In the CSV, their encoding definitions appear in row 14 in the format `%N(0.0:0.0="OFF",1.0:1.0="ON")`. In the EDA_1 explorer these are rendered as step functions using stepped rendering.

## Invalid Words — Important Caveats

Where a parameter description says **Invalid word: X**, that value is an ARINC bus max-word constant output when the parameter is not refreshed. Several non-obvious properties:

- **Not synchronous across parameters.** Each parameter sits at a fixed slot in the 512-word FDR subframe. Invalid words for different parameters fall at different time offsets within each 8-second cycle. Two related parameters (e.g. Eng1 N1 and Eng2 N1) will have their invalid words at slightly different times, causing brief apparent divergence when plotted together even after filtering. These are artifacts.
- **Cadence is approximately 8 seconds, not exactly.** The 512-word subframe runs at 64 words/second = 8 s/cycle, but individual parameter positions within the frame produce offsets that are not round numbers.
- **Not all samples are affected.** The fraction of invalid samples varies by parameter (13%–50%) and does not follow a uniform pattern through time.

See `data.md` for the full invalid-word table and handling guidance.

---

## Parameter Reference

### Acceleration (16 Hz)

These three parameters are sampled at the highest rate in the dataset (16 Hz = once per 62.5 ms), providing the finest time resolution available.

| Parameter | Unit | Description |
|---|---|---|
| `Accel Vert` | g | Vertical acceleration measured by the inertial reference unit. 1.0 g = normal cruise (1 g upward). Negative = downward load. During the MU5735 dive, this showed extreme negative-g excursions indicating sustained inverted or near-inverted loading. **Invalid word: 5.9996 g** (ARINC word max) — appears throughout the recording and must be rejected. |
| `Accel Long` | g | Longitudinal (fore-aft) acceleration. Positive = forward thrust or braking deceleration. |
| `Accel Lat` | g | Lateral (sideways) acceleration. |

---

### Flight Controls (8 Hz)

Sampled at 8 Hz (once per 125 ms). These are the most time-critical pilot input parameters.

| Parameter | Unit | Description |
|---|---|---|
| `Ctrl Col Force Pitch CWS Local` | lb | Force applied by the **Captain** (Local side) to the control column in the pitch axis, measured by a force sensor in the control column CWS (Control Wheel Steering) system. Positive = nose-down pressure. In MU5735, this parameter showed inputs during the dive. |
| `Ctrl Col Force Pitch CWS Foreign` | lb | Force applied by the **First Officer** (Foreign side) to the control column. The Local/Foreign distinction is relative to the active autopilot channel. |
| `Ctrl Col Force Pitch CWS` | lb | Combined or reference column force signal. |
| `Ctrl Col Pos-L` | deg | Control column position, left side. |
| `Ctrl Col Pos-R` | deg | Control column position, right side. |
| `Ctrl Whl Force Roll CWS` | lb | Force applied to the control wheel (roll axis). |
| `Ctrl Whl Pos-L` | deg | Control wheel position, left side. |
| `Ctrl Whl Pos-R` | deg | Control wheel position, right side. |
| `Elevator-L` | deg | Left elevator surface position. |
| `Elevator-R` | deg | Right elevator surface position. |
| `Aileron-L` | deg | Left aileron surface position. |
| `Aileron-R` | deg | Right aileron surface position. |
| `Rudder` | deg | Rudder surface position. |
| `Rudder Ped Pos` | deg | Rudder pedal position (pilot input). |
| `Roll Rate` | deg/s | Roll rate from the inertial reference unit. |
| `Absolute Roll Rate` | deg/s | Absolute value of roll rate. |
| `Yaw Rate` | deg/s | Yaw rate from the inertial reference unit. |

---

### Attitude & Air Data (4 Hz)

Sampled at 4 Hz (once per 250 ms).

| Parameter | Unit | Description |
|---|---|---|
| `Pitch Angle` | deg | Aircraft pitch attitude (positive = nose up). At cruise this was ~+3° to +5°. During the MU5735 dive it fell to approximately −80°. **Sentinels: −0.18, 0.0** (ARINC frame fill patterns). |
| `Roll Angle` | deg | Aircraft roll attitude (positive = right wing down). Range is ±180°. A discontinuous jump from ~+175° to ~−175° (or vice versa) in the data is a valid ±180° wrap, not an error. **Sentinels: −0.18, 0.0**. |
| `Altitude Press` | ft | Pressure altitude from the Air Data Computer. The FDR recording covers from cruise at ~29,000 ft down to ~26,000 ft, where the FDR lost power. **Sentinels: −1.0, 0.0** (appear as spikes at the start of the recording). |
| `Airspeed Comp` | kts | Computed (calibrated) airspeed. **Sentinels: 0.0**. |
| `Heading` | deg | Magnetic heading. |
| `Ground Spd` | kts | GPS/IRS ground speed. **Invalid word: 1023.5** (10-bit max). |
| `Aileron Actuator Pos-L` | deg | Left aileron hydraulic actuator position (surface, not column). |
| `Aileron Quadrant Pos` | deg | Aileron cable quadrant position. |
| `Aileron Roll Cmd-L` | deg | Left aileron roll command from the FCC. |
| `Elevator Actuator Pos-L` | deg | Left elevator hydraulic actuator position. |
| `Elevator Pitch Cmd-L` | deg | Left elevator pitch command from the FCC. |
| `Rudder Pos-LVDT DEMOD-STBY PCU` | deg | Rudder position measured by the standby power control unit LVDT. |
| `Rudder Servo Cmd-STBY PCU` | deg | Rudder command to the standby PCU. |
| `Eng1 TRA` / `Eng2 TRA` | deg | Thrust Resolver Angle — the mechanical position of the thrust lever (throttle) for engines 1 and 2. Maps directly to throttle position. |
| `Altitude Radio-1` / `Altitude Radio-2` | ft | Radio altimeter readings (distance above terrain). Only valid below ~2,500 ft AGL. Not meaningful at cruise altitude. |
| `Alt Baro Corr Combine` | ft | Combined barometric correction applied to pressure altitude. |

---

### Engine Parameters (1 Hz)

Sampled at 1 Hz (once per second). **Critical parameters for MU5735.**

| Parameter | Unit | Description |
|---|---|---|
| `Eng1 Cutoff SW` / `Eng2 Cutoff SW` | discrete | **Fuel cutoff switch position** for engine 1 and 2. States: `RUN` (normal operation) or `CUTOFF` (fuel flow cut). Both switches moved from RUN to CUTOFF at ~29,000 ft cruise — this is the **primary FDR finding** for the MU5735 accident. |
| `Eng1 N1` / `Eng2 N1` | %RPM | **Engine fan speed** (low-pressure compressor/fan rotor, "N1 spool") as a percentage of the maximum rated RPM. At cruise this was approximately 83–84%. N1 is the primary engine thrust indicator — throttle settings are expressed in terms of target %N1. After fuel cutoff, N1 decays as the fan spools down. **Invalid word: 127.88** (7-bit max × scale). Sampled at 1 Hz — the displayed post-cutoff lag in EDA_1 is a forward-fill artifact, not a real delay. |
| `Eng1 N2 Actual` / `Eng2 N2 Actual` | %RPM | **Engine core speed** (high-pressure compressor/turbine, "N2 spool") as a percentage of rated RPM. At cruise ~95–96%. N2 is directly coupled to the AC generators — when N2 falls below ~56%, the generator trips offline. Loss of AC generation causes the FDR to lose power. **Invalid word: 127.875**. |
| `Eng1 N1 Cmd` / `Eng2 N1 Cmd` | %RPM | N1 command from the Full Authority Digital Engine Control (FADEC). **Invalid word: 127.875**. |
| `Eng1 EGT` / `Eng2 EGT` | °C | **Exhaust Gas Temperature**. At cruise this was around 600–700°C. Falls after fuel cutoff as combustion stops. **Invalid word: 1023.0** (10-bit max). |
| `Eng1 Fuel Flow` / `Eng2 Fuel Flow` | pph | **Fuel flow rate** in pounds per hour. At cruise ~2,000–2,500 pph per engine. Drops to ~0 after cutoff switch moves. **Invalid word: 16368.0**. |
| `Eng1 FMC N1 Target` / `Eng2 FMC N1 Target` | %RPM | FMC-computed N1 target (the value the FMC wants N1 to be). **Invalid word: 127.0**. |
| `Flap Handle Pos` | deg | Flap lever position in the cockpit. 0 = flaps up (cruise configuration). |
| `Flap-L` / `Flap-R` | deg | Left and right flap surface positions. |
| `Hyd Oil Press - A` / `Hyd Oil Press - B` | psi | Hydraulic system A and B pressure. Normal operating pressure ~3,000 psi. **Invalid word: 4092.0**. |
| `Hydraulic Oil Pressure Standby` | psi | Standby hydraulic system pressure. Raw values in this file show anomalous scaling — treat with caution. |
| `Groundspeed Disp -L` | kts | Ground speed display value (left). **Invalid word: 1023.5**. |
| Various FCC autoflight mode flags | discrete | See Autopilot section below. |

---

### Engine Parameters (0.5 Hz)

| Parameter | Unit | Description |
|---|---|---|
| `Eng1 FMC N1 Bug Drive` / `Eng2 FMC N1 Bug Drive` | %RPM | FMC N1 bug (reference marker) drive value. Sampled every 2 seconds. **Invalid word: 127.0**. |

---

### Engine Parameters (0.25 Hz)

Sampled once every 4 seconds.

| Parameter | Unit | Description |
|---|---|---|
| `Eng1 N1 Tach` / `Eng2 N1 Tach` | %RPM | N1 tachometer signal (independent channel from `Eng1 N1`). **Invalid word: 127.875**. |
| `Eng1 N2 Tach` | %RPM | N2 tachometer signal. **Invalid word: 127.875**. |
| `Eng1 N1 Ref` / `Eng2 N1 Ref` | %RPM | N1 reference value. |
| `Eng1 FMV Pos` / `Eng2 FMV Pos` | % | Fuel Metering Valve position. 0% = closed (no fuel). |
| `Eng1 Oil Qty` / `Eng2 Oil Qty` | qt | Engine oil quantity in quarts. |
| `Eng1 Oil Temp` / `Eng2 Oil Temp` | °C | Engine oil temperature. |
| `Eng2 Oil Press` | psi | Engine 2 oil pressure. |
| `Hyd Oil Qty - A` / `Hyd Oil Qty - B` | % | Hydraulic fluid quantity (A and B systems) as % of nominal. |
| `Groundspeed FMC` | kts | FMC-computed ground speed. |
| `High Speed Buffet Speed` | kts | Speed at which high-speed buffet (aerodynamic) is predicted; computed by FMC from current altitude/weight. |
| `Track Angle True FMC` | deg | True track angle over ground, from FMC. |
| `Alt 3 Baro Corr` / `Alt 4 Baro Corr` | ft | Additional barometric correction channels. |

---

### Engine Parameters (0.125 Hz)

Sampled once every 8 seconds.

| Parameter | Unit | Description |
|---|---|---|
| `APU N1` | %RPM | Auxiliary Power Unit speed. APU was off during the accident flight. **Invalid word: 127.5**. |
| `Eng1 Oil Press` | psi | Engine 1 oil pressure. **Invalid word: 1023.0**. |
| `Eng2 N2 Tach` | %RPM | Engine 2 N2 tachometer (0.125 Hz channel). |
| `Airspeed Max Allowable` | kts | Maximum allowable airspeed (VMO) computed by the Air Data Computer. **Invalid word: 1023.0**. |

---

### Autopilot / FCC Discrete Flags (2 Hz)

Sampled at 2 Hz (once per 500 ms). All discrete (ON/OFF).

| Parameter | Description |
|---|---|
| `CMD A - FCC` | Autopilot Command A engaged |
| `CMD A Light - FCC` / `CMD B Light - FCC` | AP CMD A/B annunciator lights |
| `AP Off - FCC` | Autopilot disconnect warning |
| `AP-1 Warn` / `AP-2 Warn` | AP warning flags |
| `FD-A Switch - FCC` / `FD-B Switch - FCC` | Flight Director A/B switch positions |
| `LOCAL LIMITED MASTER FCC-L` / `FCC-R` | Which FCC is the limited local master |
| `Eng1 Fire` / `Eng2 Fire` | Engine fire detection discrete |

---

### Autoflight Mode Flags (1 Hz)

All discrete (engaged/not engaged). Sampled at 1 Hz.

| Parameter | Description |
|---|---|
| `ALT ACQ Engaged - FCC` | Altitude Acquire mode |
| `ALT HOLD Engaged - FCC` | Altitude Hold mode |
| `LNAV Engaged - FCC` | Lateral Navigation mode |
| `VNAV PATH Engaged - FCC` | Vertical Nav — Path mode |
| `VNAV SPD Engaged - FCC` | Vertical Nav — Speed mode |
| `GS Engaged - FCC` / `GP Engage - FCC` | Glide Slope / Glide Path engaged |
| `LOC Engaged - FCC` | Localizer mode |
| `HDG SELECT - FCC` / `HDG SEL Light - FCC` | Heading Select mode |
| `LVL Change Light - FCC` | Level Change mode |
| `TOGA Engaged - FCC` | Takeoff/Go-around mode |
| `N1 Light - FCC` | N1 reference mode |
| `AT FMC SPD Engaged` | Autothrottle FMC speed mode |
| `FAC Engage - FCC` | Flight Augmentation Computer engaged |
| `Single Channel - FCC` | Single-channel AP mode |
| `SPEED INTERVENTION ACTIVE - FCC` | Speed intervention active |
| `FMC Valid` | FMC navigation solution valid |
| `Active Altitude Ref - FCC` | Whether MCP Alt or FMC Alt is the active reference |
| `G/S Dev Warn - FCC` | Glide slope deviation warning |
| `IAS Display - FCC` | IAS display flag |
| `VISUAL ALTITUDE ALERT - FCC` | Visual altitude alert |
| `VNAV Light On - FCC` | VNAV annunciator |
| `SPD Light On - FCC` | Speed annunciator |
| `LNAV Light - FCC` | LNAV annunciator |

---

### Electrical Bus Discretes (1 Hz)

All discrete (powered/unpowered). Sampled at 1 Hz. These showed the AC buses going offline as the engines wound down.

| Parameter | Description |
|---|---|
| `115 VAC Stdby Bus Sect 2` | 115V AC Standby Bus (section 2) — powered status |
| `115 VAC XFR Bus 2` | 115V AC Transfer Bus 2 — powered status |
| `28 VDC BAT BUS SECT 2` | 28V DC Battery Bus section 2 |
| `28 VDC BUS 1 SECT 2` / `28 VDC BUS 2 SECT 3` | 28V DC Bus 1/2 |
| `28 VDC HOT BAT BUS` | 28V DC Hot Battery Bus — directly connected to battery, always live |
| `28 VDC STDBY BUS SECT 2` | 28V DC Standby Bus |
| `28 VDC SW HOT BAT BUS S1` | 28V DC Switched Hot Battery Bus |
| `Hydraulic System A ELEC` / `B ELEC` | Electric hydraulic pump (A/B system) status |
| `Hydraulic System A Eng 1` / `B Eng 2` | Engine-driven hydraulic pump (A/B system) status |
| `Hydraulic System Standby` | Standby hydraulic system status |

---

### Air-Ground / Weight-on-Wheels (multiple rates)

| Parameter | Rate (Hz) | Description |
|---|---|---|
| `Air-Ground` | 4 | Primary air/ground discrete from the squat switch logic |
| `Air Gnd On Gnd` | 0.25 | Secondary air/ground source |
| `AIR GROUND - SMYDC-2` | 0.02 | Weight-on-wheels from SMYDC-2 (Spoiler/Mixer/Yaw Damper Computer). Sampled only once every 50 seconds — essentially a static flag during flight. |

---

### FMC / Navigation (1 Hz unless noted)

| Parameter | Unit | Description |
|---|---|---|
| `FMC Selected Altitude` | ft | Altitude target selected in the FMC. **Invalid word: 65520.0** (16-bit max). |
| `FMC Selected Airspeed` | kts | Speed target from FMC. |
| `FMC Selected Mach` | mach | Mach target from FMC. |
| `Selected Altitude FCC` | ft | Altitude selected on the MCP (autopilot control panel). **Invalid word: 65520.0**. |
| `Selected Airspeed FCC` | kts | Speed selected on the MCP. |
| `Selected Mach FCC` | mach | Mach selected on the MCP. |
| `Selected Vertical Speed FCC` | fpm | Vertical speed selected on the MCP. |
| `Selected Course Local FCC` / `Foreign FCC` | deg | Course selected for local/foreign FCC. |
| `Heading Selected FCC` | deg | Heading bug position on the MCP. |
| `Airspeed Target FCC` | kts | FCC airspeed target. |
| `Drift Angle - FMC` | deg | Drift angle (difference between track and heading) from FMC. |
| `Track Angle True FMC` | deg | True track angle from FMC (0.25 Hz). |
| `Wind Direction True - FMC` | deg | Wind direction from FMC. |
| `Wind Speed - FMC` | kts | Wind speed from FMC. |
| `Groundspeed FMC` | kts | Ground speed from FMC (0.25 Hz). |
| `APU On` | discrete | APU master switch status. |
| `APU Ready To Load` | discrete | APU ready to accept electrical load. |
| `FCC-L In Command of MACH Trim` / `FCC-R` | discrete | Which FCC is commanding mach trim. |
| `MACH Trim Servo Brake Status - FCC-L` | discrete | Mach trim servo brake status (0.125 Hz). |
| `115VAC UNAVAIL TO L IGN 1/2` / `R IGN 1/2` | discrete | 115V AC unavailable to left/right igniter 1/2 (0.25 Hz for 3 channels, 0.125 Hz for one channel). |
| `Alt 1/2/3/4 Baro Corr` | ft | Barometric corrections for altimeter channels 1–4. |

---

## Key Parameter Notes for MU5735 Analysis

### Why the FDR stops mid-dive

The FDR is powered from the aircraft's AC electrical buses. These buses are driven by generators mounted on each engine's accessory gearbox. When N2 falls below ~56%, the generator field collapses and the generator trips offline. With both engines cut off at cruise, both generators went offline within ~10–12 seconds, cutting FDR power. **The FDR stopped recording at approximately 26,000 ft, roughly 10+ minutes before impact.**

### N1 sample rate and the post-cutoff display delay

`Eng1 N1` and `Eng2 N1` are sampled at **1 Hz**. After the cutoff switch moves (itself also a 1 Hz parameter), the next valid N1 sample arrives up to ~2.625 seconds later (worst case: cutoff at the start of a 1 Hz N1 cycle + 16 Hz grid forward-fill). The apparent flat segment in EDA_1 immediately after cutoff is **entirely a forward-fill display artifact** — N1 decay begins immediately on fuel cutoff. Raw sample inspection confirms that the first post-cutoff N1 reading (at T ≈ −16.75 s) already showed 48.25%RPM, consistent with ~2.6 s of spool-down.

### Control column force and the dual-input question

`Ctrl Col Force Pitch CWS Local` (Captain) and `Ctrl Col Force Pitch CWS Foreign` (First Officer) are recorded separately at 8 Hz, giving 125 ms resolution. These are the highest-resolution pilot-input parameters in the dataset and are the primary basis for determining whether there was pilot corrective action during the upset.

### The ±180° roll wrap

`Roll Angle` spans ±180°. A discontinuous jump from approximately +175° to −175° (or vice versa) in the data around T ≈ −11 s (relative to FDR cutoff) is a **valid coordinate wrap**, not a data error. The aircraft was near-inverted at that point in the dive.
