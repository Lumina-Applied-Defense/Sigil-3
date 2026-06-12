# Lumina Applied Defense

## Program SIGIL-3 — X-Band Phased Array Radar Demonstrator

### Engineering Build Plan & Budget — Rev A, June 2026

---

## 1. Executive Summary

Lumina Applied Defense will construct a functional X-band (10.5 GHz) pulse-LFM phased array radar demonstrator. The program de-risks cost and schedule by building the **3 km class** variant in two array increments: a 4-channel subarray that validates the complete signal chain, followed by expansion to the full 16-channel array.

- **Total hardware budget:** $7,000–8,500 USD (full 16-channel unit), plus 15% contingency
- **Interim deliverable at ~$5,000 spend:** working 4-channel radar proving the entire architecture
- **Estimated schedule:** 6–8 months at one dedicated engineer

The dominant cost driver is the RF front end: 16× ADTR1107 (LNA/PA FEM) at ~$228/unit ≈ $3,650 — more than all other silicon combined. The two-increment array strategy defers 75% of this exposure until after system validation.

## 2. Program Objectives

| # | Objective | Verified by |
|---|---|---|
| O1 | Demonstrate end-to-end pulse-LFM radar chain (chirp → upconvert → beamform → receive → pulse compression → Doppler/CFAR → display) | Phase 4 gate |
| O2 | Demonstrate ±45° electronic beam steering on 16 elements | Phase 5 gate |
| O3 | Detect and track a cooperative target (corner reflector / vehicle) under authorized transmission | Phase 6 gate |
| O4 | Establish in-house X-band assembly, bring-up, and test capability | Continuous |

## 3. Phase Plan

Each phase ends in a gate review. **No procurement for phase N+1 is released until gate N passes.** Bring-up procedures follow the upstream project's documented checklist (redacted), including its abort criteria.

### Phase 0 — Facility, Tooling & Compliance Initiation (Weeks 1–2, $300–600)

- Toolchain install: Vivado WebPACK, STM32CubeIDE, Python 3.8+, Eagle/Fusion 360.
- Lab equipment purchase (only items not already on hand): current-limited bench supply ($70), hot-air rework station ($80), inspection microscope ($30), TinySA Ultra+ spectrum analyzer ($240), ST-Link programmer ($10), assembly consumables ($50).
- **Open the spectrum authorization workstream immediately** (Section 7) — licensing lead time can exceed hardware lead time.
- Optional, recommended: $80–130 Artix-7 (XC7A50T) development board so FPGA firmware regression runs in parallel with all hardware phases.
- **Gate 0:** toolchain builds the tracked firmware/bitstream baselines; license application filed; test-equipment calibration check done.

### Phase 1 — Power Management Board (Weeks 2–4, $150–200)

- Fabricate 2-layer PCB, qty 5 (~$30); hand-assemble (21× TPS562208, 6× ADM7151, 2× TPS7A8300, 5× LM2662, 0603/0805 passives, ~$120).
- Validate every rail for voltage, ripple, and load regulation against the sequencing spec in (redacted).
- **Gate 1:** all rails within tolerance under dummy load; sequencing plan reviewed.

### Phase 2 — Frequency Synthesis Board (Weeks 4–8, $550–700)

- 6-layer controlled-impedance PCB (~$150–250); key parts: 2× ADF4382A (~$170), AD9523-1 (~$45), 2× CVHD-950 VCXO (~$100), ECOC-2522 OCXO (~$45), Mini-Circuits/passives/SMA (~$140).
- Stencil + reflow assembly (0201 passives — no hand-iron placement).
- Drive SPI configuration from a Nucleo-F746 ($25) running the repo's STM32 code; confirm PLL lock GPIOs; survey outputs on spectrum analyzer.
- **Gate 2:** both LO synthesizers and clock tree lock reliably across power cycles.

### Phase 3 — Main Board, Digital & IF Section (Weeks 8–16, $1,700–2,200)

- 10-layer hybrid-stackup PCB per (redacted) — RF laminate is mandatory, qty 5, $400–700. Quote PCBWay (existing project sponsor) and one alternate.
- **Contract SMT assembly** for passives/QFN/BGA (~$300–500) using the provided CPL; consign high-value ICs. Hand assembly of ~600 0201 components is a program risk, not a cost saving.
- Populate all silicon **except** the 16 ADTR1107 FEMs and 3 of 4 ADAR1000s: XC7A50T FPGA ($100), STM32F746 ($20), AD9484 ADC ($80), AD9708 DAC + AD8352 amps ($30), 2× LTC5552 mixers ($45), 1× ADAR1000 ($90), 17× M3SWA2-34DR+ switches ($110), support ICs ($150), connectors/passives ($180–320).
- Bring-up strictly per upstream checklist: JTAG/DONE → clocking → LO → beamformer readback → ADC/DDC/matched-filter/USB via ILA. Validate the full DSP chain in IF loopback — **a complete radar transceiver with no radiating hardware fitted.**
- Action item: the BOM lists two band-pass filters as placeholder `BPF2` with no MPN. Resolve from the schematic or upstream maintainer **before** the parts order.
- **Gate 3:** loopback chirp → pulse-compressed return visible at the host GUI; current draw and thermals within the bring-up plan's envelope.

### Phase 4 — RF Front End, Increment 1 (Weeks 16–20, ~$1,000)

- Fit 4× ADTR1107 on the populated ADAR1000 quad. Verify negative-gate-bias sequencing before drain enable on first power-up (irreversible-damage risk).
- Per-channel TX power into calibrated attenuators; RX gain/noise via loopback switches; phase-shifter sweeps verified per channel.
- **Gate 4 (Key Decision Point):** 4-channel subarray meets per-channel power/phase targets → release the remaining ~$3,000 RF procurement (12× ADTR1107, 3× ADAR1000). Re-quote at volume breaks (qty 10/25) across DigiKey, Mouser, Arrow, Avnet; verify ADAR1000 lead time, which was supply-constrained at plan date.

### Phase 5 — Full Array, Antenna & Integration (Weeks 20–28, $3,400–3,600)

- Remaining front-end silicon (~$3,000); 8×16 patch array PCB on RF laminate ($150–300) with 16× Würth SMP connectors (~$100); 16× **equal-length** SMP-SMA jumpers (~$200) — length matching is a hard requirement for beamforming.
- Integrate, run array calibration, verify electronic steering against a bench target.
- **Gate 5:** measured beam steering across ±45° consistent with the simulation reports in (redacted).

### Phase 6 — Mechanical Scan, Peripherals & Field Demonstration (Weeks 28–34, $200–300)

- GPS, IMU, barometer (~$25), stepper + driver (~$40), slip ring (~$50), fans/temp sensors (~$25), 12 V/15 A supply ($30), enclosure per (redacted) (~$100).
- Field demonstration against cooperative targets **under the authorization obtained in Phase 0**.
- **Gate 6 / Program close-out:** objective O3 demonstrated and logged; lessons-learned report; go/no-go recommendation on the Extended-variant follow-on.

## 4. Procurement Strategy

- **Two-increment RF buy** (4 + 12 channels) defers ~$2,700 past system validation.
- **Long-lead watch list:** ADAR1000 (constrained), ADTR1107 (high-value — order against a confirmed gate only), hybrid-stackup PCB fab (2–3 week lead).
- Passives/connectors from LCSC; generic SMA in place of the $5.40 Cinch parts where the connector is DC-control or IF-only (saves ~$250 across the program); name-brand connectors retained on 10.5 GHz paths.
- No brokers or gray market for the high-value RF silicon — counterfeit risk exceeds any discount.

## 5. Budget Summary

| Phase | Scope | Budget (USD) |
|---|---|---|
| 0 | Tooling & compliance | 300–600 |
| 1 | Power board | 150–200 |
| 2 | Frequency synthesis | 550–700 |
| 3 | Main board digital/IF | 1,700–2,200 |
| 4 | RF increment 1 (4-ch) | ~1,000 |
| 5 | RF increment 2 + antenna | 3,400–3,600 |
| 6 | Mechanical & field demo | 200–300 |
| | **Subtotal** | **7,300–8,600** |
| | Contingency (15%) | ~1,100–1,300 |
| | **Program total** | **~$8,400–9,900** |

Labor is assumed in-house and is excluded. Prices are mid-2026 single-unit distributor figures; each gate review re-quotes the next phase's BOM.

Key pricing references (as of June 2026):

- [ADTR1107ACCZ — DigiKey](https://www.digikey.com/en/products/detail/analog-devices-inc/ADTR1107ACCZ/12419087) (~$228/unit)
- [ADAR1000ACCZN — DigiKey](https://www.digikey.com/en/products/detail/analog-devices-inc/ADAR1000ACCZN/15991987) (~$90/unit, supply-constrained)
- [ADF4382ABCCZ — DigiKey](https://www.digikey.com/en/products/detail/analog-devices-inc/ADF4382ABCCZ/24613258) (~$85/unit)
- [XC7A50T-2FTG256I — DigiKey](https://www.digikey.com/en/products/detail/amd/XC7A50T-2FTG256I/5039082) ($100.33/unit)

## 6. Risk Register (top items)

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| ADAR1000 supply constraint stalls Phase 4/5 | M | H | Confirm lead time in Phase 0; buy all 4 early if quote window is favorable |
| ADTR1107 damaged by bias-sequencing fault | M | H | 4-channel increment; sequencing review at Gate 3; current-limited bring-up |
| Hybrid-stackup fab quality (RF laminate) | M | M | Use fab with documented stackup match; order qty 5; impedance coupons |
| 0201 hand-assembly defects | H | M | Contracted SMT assembly for main board (decided) |
| Inadequate 10.5 GHz test capability in-house | H | M | TinySA + design's built-in loopback/coupled paths; rent or partner for VNA time before Gate 5 |
| Spectrum authorization delayed | M | H | File in Phase 0; all testing to Gate 5 is conducted/loopback into attenuators |
| Unidentified `BPF2` filter part | H | L | Resolve from schematic/upstream before Phase 3 order |

## 7. Compliance & Legal

- **Spectrum:** As a commercial entity, Lumina cannot transmit under an amateur allocation. An experimental/test authorization from the national regulator (e.g., FCC Part 5 in the US, or the local equivalent) is required before any radiated testing; until granted, all RF testing is conducted into shielded loads. This is a Phase 0 deliverable with the longest non-hardware lead time in the program.
- **Export control:** Phased array radar hardware and beamforming components can fall under export-control regimes (e.g., EAR Category 6 or national equivalents) regardless of the design being open source. Obtain a classification opinion before any cross-border shipment, publication of modifications, or foreign-national involvement. *Not legal advice — engage counsel.*
- **RF safety:** 16×1 W at a directive aperture requires an RF exposure assessment and keep-out distance for field testing.
