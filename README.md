# CDMARelayForge Pro (v1.0.0)

### 2 GHz CDMA Space Relay & PN Ranging Engineering Workstation

**Normative Standard Baseline**: [CCSDS 415.1-B-1 (Blue Book, Issue 1, September 2011\)](https://ccsds.org/Pubs/415x1b1.pdf)  
**Space Network Baseline**: Space Network Interoperability Panel (SNIP \- NASA, ESA, JAXA)  
**Associated Standards**: CCSDS 401.0-B (RF & Modulation), ITU Radio Regulations, [SANA Space Registry](https://sanaregistry.org/)  
**Classification**: Space Link Services / RF & Modulation / Satellite Relay & Navigation Toolchain

---

## 1\. Product Overview

**CDMARelayForge Pro (v1.0.0)** is an autonomous, zero-dependency aerospace software workstation implementing the **Consultative Committee for Space Data Systems (CCSDS) 415.1-B-1: Data Transmission and PN Ranging for 2 GHz CDMA Link via Data Relay Satellite** standard.

Spacecraft communicating via Geostationary Data Relay Satellites (e.g., NASA TDRSS, ESA EDRS, JAXA DRTS) or direct Earth networks require simultaneous high-integrity command transmission, real-time telemetry return, and high-precision radiometric navigation. However, missions must prevent mutual Multiple Access Interference (MAI) and strictly adhere to International Telecommunication Union (ITU) Power Spectral Density (PSD) limits at the Earth's surface (\$-154\\text{ dBW/m}^2\\text{/4kHz}\$) to protect terrestrial microwave links.

CDMARelayForge Pro delivers a verified reference suite implementing the complete CCSDS 415.1-B-1 protocol stack:

1. **DSSS Spread Spectrum Gold Code Generator**: Order-10 Linear Feedback Shift Register (LFSR) preferred-pair polynomials (\$f\_1(x) \= 1 \+ x^3 \+ x^{10}\$ and \$f\_2(x) \= 1 \+ x^2 \+ x^3 \+ x^6 \+ x^8 \+ x^9 \+ x^{10}\$) generating length-1023 Gold code sequences with bounded three-valued cross-correlation.  
2. **Forward Link Unbalanced QPSK (UQPSK)**: Command Channel (I) and Range Channel (Q) multiplexed with an unbalanced 10:1 power ratio (10 dB command power advantage) at 3.0 or 3.087 Mchip/s.  
3. **Return Link Staggered Quadrature PN (SQPN)**: 1/2-chip delay between I and Q channels to minimize TWTA amplifier envelope fluctuations, with 1:4 power ratio (6 dB telemetry priority on Q channel).  
4. **Coherent Transponder Turnaround (240/221 Ratio)**: Exact frequency ratio (\$f\_{\\text{return}} / f\_{\\text{forward}} \= 240 / 221 \\approx 1.08597285\$) with coherent PN epoch locking.  
5. **Sub-Meter Two-Way PN Ranging & Doppler Tracking**: Resolves round-trip light time (RTLT) into sub-meter range distance (\$R \= c \\cdot \\Delta \\tau / 2\$) and calculates radial velocity from carrier Doppler shift (\$v \= \-c \\cdot \\Delta f\_D / (2 f\_{\\text{rtn}})\$).  
6. **10-Point Normative Auditor**: Validates compliance with CCSDS 415.1-B-1, achieving 100% compliance (`CCSDS_415_1_CDMA_CERTIFIED`).

---

## 2\. Technical Architecture & Wire Framing

\+-----------------------------------------------------------------------------------+

\|                     CDMARelayForge Pro (v1.0.0) Architecture                      \|

\+-----------------------------------------+-----------------------------------------+

\|        Forward Service Link (UQPSK)     \|       Return Service Link (SQPN)        \|

\|          (Network \-\> User, 2067.5 MHz)  \|       (User \-\> Network, 2245.25 MHz)    \|

\+-----------------------------------------+-----------------------------------------+

\| • In-Phase (I) Command Channel:         \| • In-Phase (I) Pilot Channel:           \|

\|   Command Data XOR Gold Code            \|   Dedicated Coherent Tracking Code      \|

\|   Power Weight \= 10 (Amplitude \= 3.162) \|   Power Weight \= 1 (Amplitude \= 1.0)    \|

\| • Quadrature (Q) Range Channel:         \| • Quadrature (Q) Telemetry Channel:     \|

\|   Unmodulated Range Gold Code           \|   Telemetry Data XOR Gold Code          \|

\|   Power Weight \= 1 (Amplitude \= 1.000)  \|   Power Weight \= 4 (Amplitude \= 2.0)    \|

\| • Chip Rate: 3.0 or 3.087 Mchip/s       \| • 1/2 Chip Stagger (SQPN Low PMEPR)     \|

\+-----------------------------------------+-----------------------------------------+

                                      │

                                      ▼

\+-----------------------------------------------------------------------------------+

\|               Transponder Coherent Turnaround & Radiometric Ranging               \|

\|  • Coherent Turnaround Ratio: f\_return / f\_forward \= 240 / 221                    \|

\|  • Two-Way Round-Trip Delay to Range: R \= (c \* RTLT) / 2 (\<0.15 m precision)      \|

\|  • Carrier Doppler Velocity: v \= \- (c \* Delta\_f) / (2 \* f\_return)                 \|

\|  • Earth Surface Power Spectral Density: \< \-154 dBW/m²/4kHz (ITU Compliant)       \|

\+-----------------------------------------------------------------------------------+

---

## 3\. Directory Structure

cdma-relay-forge-pro/

├── source-code/

│   ├── cdma-engine.js           \# Pure ES6 DSSS Gold Code & Ranging Engine

│   ├── cdma-auditor.js          \# 10-Point Normative Compliance Auditor

│   ├── cdma\_cli.py              \# Multi-subcommand Python 3 CLI & Test Harness

│   ├── test\_runner.py           \# 10 automated unit tests (100% pass rate)

│   └── index.html               \# Responsive Glass-Cockpit Web Studio

├── documentation/

│   ├── CCSDS\_415.1\_CDMA\_SPECIFICATION.md \# Detailed bit-level specification

│   └── CDMA\_COMPLIANCE\_MATRIX.md         \# 10-point normative verification matrix

├── screenshots/

│   ├── cdma-architecture-diagram.svg     \# Full-stack vector architecture diagram

│   └── cdma-studio-preview.svg           \# Glass-cockpit workstation UI mockup

├── build/

│   └── cdma-relay-forge-pro-v1.0.0.zip   \# Production release distribution archive

├── PROGRESS\_REPORT.md                    \# Cycle \#501 execution audit report

└── README.md                             \# Comprehensive operational manual

---

## 4\. Quickstart Guide

### 4.1 Running the Glass-Cockpit Studio

Open `source-code/index.html` in any modern web browser. The studio runs 100% client-side with zero external network connectivity.

### 4.2 Using the Python 3 CLI

#### Generate an Order-10 Gold Code Sequence

python3 source-code/cdma\_cli.py generate-code \--shift 12

#### Synthesize a Forward UQPSK Waveform

python3 source-code/cdma\_cli.py synthesize-link \--direction forward \--ratio 10:1

#### Solve Two-Way Range and Doppler

python3 source-code/cdma\_cli.py solve-range \--delay 0.250 \--doppler 15000.0

#### Benchmark Power Spectral Density (PSD) Compliance

python3 source-code/cdma\_cli.py benchmark-psd \--eirp 20.0

#### Run the 10-Point Normative Compliance Audit

python3 source-code/cdma\_cli.py audit

---

## 5\. Automated Verification & Testing

Execute the automated test suite:

python3 source-code/test\_runner.py

### Test Coverage Results (10/10 PASS — 100%):

1. `test_01_gold_code_generation`: Preferred-pair order-10 LFSR Gold code generation (1023 chips).  
2. `test_02_gold_code_correlation`: Autocorrelation peak at lag 0 (1.000) and low cross-correlation sidelobes (\<0.0635).  
3. `test_03_forward_uqpsk_modulation`: Forward link UQPSK 10:1 power ratio between Command and Range channels (\$\\sqrt{10} \\approx 3.162\$).  
4. `test_04_forward_range_channel`: Dedicated Q channel carries unmodulated range code.  
5. `test_05_return_sqpn_modulation`: Return link SQPN 1:4 power ratio and 1/2 chip stagger (\$Q \= 2.0\$).  
6. `test_06_coherent_turnaround_ratio`: Transponder coherent turnaround ratio 240/221 frequency exactness (\$2067.5\\text{ MHz} \\to 2245.2489\\text{ MHz}\$).  
7. `test_07_two_way_ranging_solver`: RTLT delay accurately calculates two-way range in kilometers (\$250\\text{ ms} \\to 37,474.06\\text{ km}\$).  
8. `test_08_carrier_doppler_velocity`: Carrier Doppler shift resolves line-of-sight radial velocity in m/s (\$15\\text{ kHz} \\to \-1001.4\\text{ m/s}\$).  
9. `test_09_pn_ambiguity_distance`: 1023-chip epoch period ambiguity interval calculation (\$51.1\\text{ km}\$).  
10. `test_10_normative_audit`: 10-point normative compliance audit (100% certified).

---

## 6\. Commercial Model & Monetization Strategy

CDMARelayForge Pro serves spacecraft transponder developers, satellite operators, and tracking networks:

- **Open-Core Community Tooling**: Zero-dependency Python CLI and interactive educational studio.  
- **Enterprise Constellation License (\$24,500/year/constellation)**: Includes flight-qualified C/C++ transponder baseband correlators, FPGA IP core synthesizers (Xilinx UltraScale+, Microchip PolarFire SoC, NanoXplore), and hardware-in-the-loop (HIL) automated testbenches.  
- **Flight Qualification & Integration Consulting (\$3,500/day)**: Radio frequency link compatibility testing with NASA TDRSS, ESA EDRS, and JAXA DRTS ground stations.

---

## 7\. Operational Limitations

- **Software Baseband Modeling**: Synthesizes bit-exact digital baseband samples, Gold code sequences, and ranging metrics; physical RF transmission in the 2 GHz space band (2025–2290 MHz) requires SDR transceiver hardware (e.g., USRP, Ettus, ADI Pluto, or space-qualified transponders).