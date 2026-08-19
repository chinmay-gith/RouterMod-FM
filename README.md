# 📡 RouterMod FM
### *An analog RF broadcast system engineered inside the corpse of a dead Wi-Fi router*

---

> Somewhere between a teardown, a signal-chain design exercise, and an act of electronic necromancy — **RouterMod FM** takes a piece of e-waste and turns it into a fully functional, dual-input analog FM broadcast station. No microcontrollers. No DSP. No digital-to-analog conversion anywhere in the chain. Just transistors, inductors, and Maxwell's equations doing what they've done since the 1930s.

---

## 🧪 Abstract

Consumer Wi-Fi routers are, mechanically speaking, just injection-molded RF enclosures with good ventilation and a convenient power input. This project exploits that fact: the dead internals of a router are removed entirely and replaced with a purpose-built **analog audio mixing stage** cascaded into a **VHF FM transmitter stage**, repurposing the original chassis, top-shell switches, and rear I/O cutouts as the new system's control surface.

The result is a self-contained broadcast unit that takes two simultaneous audio sources — a line-level laptop feed and a live dynamic microphone — sums them in the analog domain, modulates a free-running RF oscillator with the composite waveform, and radiates the signal to be received by any standard FM receiver within range.

---

## 🔬 System Architecture

```text
 ┌──────────────────┐
 │  Laptop / PC      │  Line-level, ~1 Vrms
 │  (3.5mm AUX out)  │──────────────┐
 └──────────────────┘              │
                                    ▼
 ┌──────────────────┐      ┌───────────────────────┐      ┌────────────────────┐      ┌────────────────────┐
 │ Dynamic Mic       │      │  PREAMP + MIXER STAGE  │      │  TOP-PANEL POTS     │      │  FM TRANSMITTER      │
 │ (µV–mV level,     │─────▶│  CD6283CS-based mixer, │─────▶│  Mic gain, master   │─────▶│  RF oscillator →      │
 │  high-Z input)    │      │  bias/coupling network │      │  vol adjust         │      │  Class-C RF PA →      │
 └──────────────────┘      └───────────────────────┘      └────────────────────┘      │  matching network     │
                                                                                         └─────────┬──────────┘
                                                                                                     │
                                                                                                     ▼
                                                                                            ┌──────────────────┐
                                                                                            │     Antenna       │──▶ (VHF, ~88–108 MHz)
                                                                                            └──────────────────┘
                                                                                                     │
                                                                                                     ▼
                                                                                            ┌──────────────────┐
                                                                                            │  5-Band FM Radio  │
                                                                                            │  (demodulator +   │
                                                                                            │   speaker output) │
                                                                                            └──────────────────┘
```

---

## ⚙️ Signal Chain — Stage by Stage

### 1. Dual Signal Acquisition
Two physically and electrically dissimilar sources are captured simultaneously through dedicated jacks mounted into the router's rear panel — clearly labeled **MIC IN** and **AUX IN** on the modified chassis:
- **Line-level audio** (laptop, ~1 V RMS, low output impedance) via the AUX IN jack
- **Dynamic microphone** (µV–mV level, high source impedance, no phantom power) via the MIC IN jack

These represent roughly **60 dB of level mismatch** at the input — the core problem the preamp stage exists to solve.

### 2. Preamplification & Mixing
The mic and line signals are fed into a dedicated preamp/mixer board built around a **CD6283CS** audio amplifier IC, with input traces silk-screened `L-IN`, `E`, `R-IN`. Coupling capacitors and filtering resistors condition and blend the two sources so the mic signal is boosted without clipping the aux path or being buried under the music bed.

### 3. Passive Level Control
The mixed signal is routed to two potentiometers mounted through the top shell — the original layout for the router's button row — now repurposed as live **mic gain** and **master volume** controls, hand-labeled on masking tape for quick reference during use.

### 4. RF Carrier Generation & Frequency Modulation
The composite audio signal modulates the frequency of a free-running RF oscillator on the dedicated RF board. In FM, information is encoded in the carrier's *instantaneous frequency deviation*, not its amplitude — which is why clean gain staging upstream matters: any noise or DC offset in the audio directly perturbs the carrier.

### 5. RF Power Amplification & Matching
The RF board carries a **Class-C RF power transistor** stage bonded to an aluminum heatsink, flanked by hand-wound tuning coils and trimmer capacitors (marked `TRM1`, `TRM2`), boosting the modulated carrier before it reaches the antenna terminal (silkscreened `ANTINA`).

### 6. Radiation & Reception
The signal exits through a rear-panel antenna jack (labeled **ANT**, reusing one of the router's original antenna mount points) and propagates over the air. A **5-Band Portable Radio**, used purely as the receive-side reference, tunes to the frequency and performs conventional FM demodulation to recover the composite audio.

---

## 🧵 Why the Router Chassis?

Beyond the novelty, the router enclosure is a genuinely good engineering choice — and the build leans into it rather than fighting it:
- **Original antenna mounts** repurposed directly as the RF output connector
- **Rear I/O panel cutouts** reused for MIC IN, AUX IN, POWER, and ANT jacks — no visible drilling needed
- **Top-shell button row** repurposed as the mounting point for the gain/volume potentiometers
- **Ventilated plastic shell** doubles as passive airflow for the RF PA's heatsink
- **Zero suspicion factor** — from the outside, it still just looks like a router

It's a case study in **adaptive reuse**: the mechanical design constraints of a mass-produced consumer product turn out to be nearly ideal for a completely unrelated RF application.

---

## 🔧 Hardware Summary

| Subsystem | Function |
|---|---|
| **Chassis** | Gutted router shell — houses all boards, wiring harness, and top-panel controls |
| **Preamp & Mixer Board** | CD6283CS audio IC + coupling/filtering network — blends mic + aux (`L-IN`/`E`/`R-IN`) |
| **RF Oscillator / PA Board** | RF oscillator, hand-wound tuning coils, trimmer caps (`TRM1`/`TRM2`), Class-C RF power transistor + heatsink, antenna output (`ANTINA`) |
| **Top-Shell Controls** | Two potentiometers — mic gain adjust and master/AUX level adjust |
| **Rear Panel I/O** | Labeled **MIC IN**, **AUX IN**, **POWER**, **ANT** — reusing the router's original port cutouts |
| **Reference Receiver** | 5-Band Portable Radio — used for tuning verification and range testing, not part of the transmitter itself |

---

## 📷 Build Gallery

| Internal wiring & board stack | Preamp/mixer + RF board detail | Rear panel labeling (MIC IN / AUX IN / POWER / ANT) |
|:---:|:---:|:---:|
| ![Internal wiring](routermod-fm-internal.jpg) | ![Board detail](routermod-fm-build.jpg) | ![Rear panel](routermod-fm-assembled.jpg) |

**🎥 Live demo:** a short clip shows the full signal chain running end-to-end — music playing from the laptop is routed into the box's AUX IN, a handheld dynamic mic is spoken into live, and the ABARNA 5-Band Portable Radio (tuned to the transmit frequency) is heard picking up the mixed broadcast in real time, with the top-panel pots being adjusted mid-demo to balance mic gain against music level. Add the clip to the repo (e.g. `demo.mp4`) and link it here, or upload to YouTube/an mp4 link and embed a thumbnail — GitHub READMEs can't autoplay video directly.

---

## 📈 Key Engineering Properties

- **Zero-latency signal path** — fully analog from mic capsule to speaker cone; no sampling, no buffering, no codec delay
- **Real-time dual-source mixing** — live speech can be overlaid on background media with no software mixing layer
- **Discrete/IC-hybrid RF design** — CD6283CS-based audio mixing feeding a discrete-transistor RF oscillator and PA, keeping the RF side fully inspectable and hand-tunable

---

## ⚠️ Regulatory Note

FM transmission power and range are regulated in most jurisdictions (e.g., low-power/unlicensed limits under Part 15 in the US, or equivalent local rules elsewhere). This build is intended for **short-range, local, educational/hobbyist use only**. Check your local spectrum regulations before operating — and don't run it anywhere near licensed broadcast frequencies.

---

## 🧭 Future Work

- [ ] Add RF shielding around the PA stage to suppress internal interference with nearby electronics
- [ ] Replace fixed-tuning trimmers with a varactor + digital frequency readout for precise, repeatable tuning
- [ ] Add a low-pass filter after the PA to clean up harmonic emissions
- [ ] Battery-powered variant for fully portable operation
- [ ] Tidy internal wiring harness (currently point-to-point hand-wired)

---

## 📄 License

*(MIT is a solid default for an open hobbyist hardware repo — add a LICENSE file if you'd like)*

---

<p align="center"><i>Built from a dead router, some transistors, and an unreasonable amount of curiosity.</i></p>
