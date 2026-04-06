# Analog-CC-CV-Battery-Charger-for-Lead-Acid-Batteries

> A fully analog, microcontroller-free smart battery charger for 12V lead-acid batteries — automatically manages Constant Current, Constant Voltage, and Trickle Charge phases using only op-amps, transistors, and MOSFETs.

**Course:** EE322 — Analog and Mixed Signal Circuits  
**Team Name:** The Semicons  
**Institute:** Indian Institute of Technology Gandhinagar


---

## 📌 Project Overview

This project implements a **low-dropout CC-CV charger** with automatic trickle charge mode — entirely in analog. No microcontrollers, no firmware, no bugs. A **P-channel MOSFET (IRF9540)** acts as the main power-pass element, with analog feedback loops handling all stage transitions automatically.

### Charging Stages

```
[Discharged Battery]
        │
        ▼
┌─────────────────┐
│  Constant       │  ← Bulk charge: fixed current, voltage rising
│  Current (CC)   │
└────────┬────────┘
         │ Vbatt reaches 14.4V
         ▼
┌─────────────────┐
│  Constant       │  ← Voltage held at 14.4V, current tapers
│  Voltage (CV)   │
└────────┬────────┘
         │ Current drops to maintenance level
         ▼
┌─────────────────┐
│  Trickle        │  ← Tiny maintenance current, prevents self-discharge
│  Charge         │
└─────────────────┘
        │
        ▼
 [Fully Charged Battery ]
```

---

## 🔧 System Architecture  

The circuit is divided into **7 functional blocks**:

| S. No. | Block Name                          |
|--------|-----------------------------------|
| 1      | AC to DC Converter                |
| 2      | Voltage Reference & Regulation    |
| 3      | Constant Current Block            |
| 4      | Switching & Output Stage          |
| 5      | Battery Simulation & Sensing      |
| 6      | Charge Indicator Block            |
| 7      | Trickle Charge Indicator Block    |
### Block Descriptions

**Block 1 — AC to DC Converter**  
Converts AC mains input to a stable ~15V DC rail using a GBU406 bridge rectifier and a 2200µF filter capacitor.

**Block 2 — Voltage Reference & Regulation**  
Uses a TL431A programmable shunt reference + 2N3904 transistor to monitor battery voltage and prevent overcharging above 14.4V.

**Block 3 — Constant Current Block**  
Limits bulk charging current to a safe preset level using TL431A, 2N3906, and 2N3904 transistors with current-sense feedback.

**Block 4 — Switching & Output Stage**  
The IRF9540 P-channel MOSFET acts as an electronic valve, throttled by control signals from the CC and CV feedback loops.

**Block 5 — Battery Simulation & Sensing**  
A 1Ω high-power shunt resistor (Rlim) senses charging current and feeds back into the CC control block.

**Blocks 6 & 7 — Charge & Trickle Indicators**  
2N7000 N-channel MOSFETs drive LEDs to visually indicate the current charging phase (CC/CV vs. Trickle).

---

## 🔩 Key Components

| Component | Role |
|---|---|
| GBU406 Bridge Rectifier | AC → DC conversion |
| 2200µF Filter Capacitor | Smoothing DC rail |
| TL431A (×2) | Programmable voltage reference (CV + CC control) |
| IRF9540 P-channel MOSFET | Main power-pass element |
| 2N3904 / 2N3906 Transistors | Control logic & feedback |
| 2N7000 N-channel MOSFETs | LED indicator drivers |
| 1Ω High-Power Resistor (Rlim) | Current sensing shunt |
| Indicator LEDs | Charge phase visual feedback |

---

## 🔌 How the Control Loop Works

The CC and CV loops **compete** to control the gate of the main MOSFET:

- **CC loop** (via matched transistor pair in U2): detects excess current → pulls MOSFET gate to throttle power
- **CV loop** (via TL431A + Q1): detects voltage exceeding 14.4V → pulls MOSFET gate to throttle power
- **Whichever limit is hit first takes control** — this is the core of the analog CC-CV switching mechanism

```
         ┌─────────────┐
         │ CC Feedback │──────┐
         │  (U2, Q3)   │      │
         └─────────────┘      ▼
                         [Q3 Base] → [Gate of IRF9540 M1] → Output to Battery
         ┌─────────────┐      ▲
         │ CV Feedback │──────┘
         │ (TL431, Q1) │
         └─────────────┘
```

---

## 🧪 Integration & Testing Plan

1. **AC-DC Block** — Verify stable ~15V DC output
2. **Voltage Reference + CC Block** — Breadboard test with lab supply to dial in setpoints
3. **Switching Stage** — Verify MOSFET gate operation in isolation
4. **CC/CV Integration** — Test with high-power dummy load; confirm stable regulation in both modes
5. **LED Indicators** — Simulate each charging phase; verify correct LEDs light up
6. **Full Battery Test** — Connect to a partially discharged 12V lead-acid battery; verify automatic CC → CV → Trickle transitions

---

## ✅ Advantages Over Alternatives

| Feature | This Design | Simple Trickle Charger | MCU-Based Charger |
|---|---|---|---|
| No firmware bugs | ✅ | ✅ | ❌ |
| Auto CC/CV/Trickle | ✅ | ❌ | ✅ |
| Overcharge protection | ✅ | ❌ | ✅ |
| Low dropout design | ✅ | ❌ | Varies |
| Fast charging | ✅ | ❌ | ✅ |
| Low complexity | ✅ | ✅ | ❌ |
| Temp stable (matched pairs) | ✅ | ❌ | ❌ |

---

## 📁 Repository Structure

```
analog-cc-cv-battery-charger/
├── README.md
├── .gitignore
├── proposal/
│   └── The_Semicons_Project_Proposal_AMSC.pdf
├── schematics/
│   └── (KiCad / LTSpice files — to be added)
└── assets/
    └── (circuit photos, PCB renders — to be added)
```

---

## 📄 Proposal

The full project proposal (with block diagram, circuit diagram, and step-by-step build plan) is available in the [`proposal/`](./proposal/) folder.

---

## 🔮 Future Work

- Temperature-compensated charging curves for better battery longevity
- Adjustable reference to support 6V / 24V lead-acid batteries
- Add analog current/voltage meter readout
- Solar panel input integration (original planned scope)

---

## 📚 References

1. [A Low Dropout CC-CV 12V Lead Acid Battery Charger — EE World Online](https://www.eeworldonline.com/a-low-dropout-cc-cv-12v-lead-acid-battery-charger-with-auto-trickle-charge/)
2. [Low Dropout Battery Charger Design Techniques — EE Times](https://www.eetimes.com/low-dropout-battery-charger-design-techniques/)
3. [Lead Acid Battery Charger Reference Design — Texas Instruments SLVA576A](https://www.ti.com/lit/an/slva576a/slva576a.pdf)
4. [Battery Charging Tutorial — Maxim Integrated App Note 6101](https://www.maximintegrated.com/en/design/technical-documents/app-notes/6/6101.html)
