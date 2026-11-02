# VDUI Unit 3 Assignment: Multimedia and Interactive Visualization
**Course:** UE24CS342AA9 — Visualization of Data and User Interfaces (VDUI)  
**Institution:** PES University  

This repository contains solutions, visual analytics implementations, and reports for Unit 3 assignments and activities.

---

## Exercise 1: The Parking-Lot Incident Review (`unit3_ppt1_activity.ipynb`)

### Incident Scenario
The security camera over Parking Lot B recorded an incident between $t = 0$ s and $t = 120$ s. The visual analytics intern was tasked with performing a comprehensive forensic review integrating video telemetry (`unit3_ppt1_cctv_log.csv`), night camera stills, and 10 seconds of synchronized acoustic data ($t = 70$–$80$ s).

### Summary of Key Tasks & Findings

1. **Storage Reality Check (Task 1):**
   - **One uncompressed 1080p RGB frame (8-bit):** 5.93 MB ($1920 \times 1080 \times 3$ bytes / $1024^2$).
   - **Frames per camera per day (30 fps):** 2,592,000 frames.
   - **400-camera fleet daily uncompressed storage (1080p, 30 fps):** 5,866.0 TB/day.
   - **Downsampled fleet (360p, 10 fps):** 217.3 TB/day (retaining only 11.11% of spatial pixels per frame).
   - **Trade-offs:** Dropping to 360p sacrifices crucial spatial recognition evidence (license plates, facial geometry), while dropping to 10 fps introduces the temporal correspondence problem and skips fast transient actions.

2. **Night Camera Image Diagnostics & Enhancement (Task 2):**
   - **Diagnosis:** Murky appearance is caused by a severely narrow, unimodal intensity histogram spanning only intensities 18 to 73 (Michelson contrast $C = 0.609$).
   - **Remedy:** Linear contrast stretching mapping 1st–99th percentiles to $[0, 255]$ boosted Michelson contrast to $C = 1.000$, restoring perceptual readability without fabricating data.

3. **Event Timeline & Content-Based Keyframes (Task 3):**
   - **Motion Analysis:** Computed velocity using a 1-second rolling difference (`diff(FPS)`).
   - **Suspect Loitering/Standing:** Identified pauses adjacent to the target car ($t \approx 68.7$ s to $78.1$ s, speed $< 3$ px/s).
   - **Acoustic Event:** First audio level spike above $-20$ dB detected at $t = 75.0$ s (frame 2250, $-4.6$ dB).
   - **Suspect Flight:** High-speed sprint detected fleeing the scene ($t = 78.6$ s to $86.0$ s, speed $> 25$ px/s, mean $35.6$ px/s).
   - **Perimeter Floodlights:** Instantaneous luminance jump ($+86$ brightness) detected at $t = 100.0$ s (frame 3000).
   - **Keyframes:** Generated content-based representative frames for each discrete state transition.

4. **Acoustic Event Classification: Gunshot vs. Breaking Glass (Task 4):**
   - **Analysis:** Compared mystery audio ($t = 74.5$–$76.5$ s) against reference gunshot and glass break signatures using Short-Time Fourier Transform (STFT) spectrograms ($N_{\text{perseg}} = 512$, $N_{\text{overlap}} = 384$).
   - **High-Frequency Energy Metric:** Post-impact ($0.2$–$1.2$ s window) energy fraction $> 3$ kHz was **87.92%** for the incident sound, closely matching reference glass break (**99.04%**) and decisively refuting a gunshot (**0.31%**).
   - **Verdict:** **Breaking Glass**.

5. **Synchronized Multimodal Briefing Dashboard (Task 5):**
   - Integrated left-hand video keyframe at $t = 75.0$ s with top-right 120 s synchronized motion/audio timeline and bottom-right acoustic spectrogram ($70$–$80$ s) linked to common incident time marker.

6. **Executive Briefing & Storage Policy (Task 6):**
   - Recommended an event-driven hybrid storage model preserving full 30 fps temporal fidelity during detected anomaly triggers.

---

## Exercise 4: Spatial & Multi-Attribute Fraud Analytics (`unit3_ppt7_activity.ipynb`)

### Overview
For PayKart Fraud Operations, this visual analytics investigation triages 5,520 card transactions across 30 days to diagnose an unexpected fraud surge, steer algorithmic detector parameters, test hypotheses against confirmation bias, and design interactive tools for investigators.

### Summary of Tasks & Findings
- **Task 1 — Overview First (Shneiderman's Mantra & Bertin's Reorderable Matrix):**
  - Plotted whole-month transaction volume against confirmed fraud, identifying an anomalous fraud surge clustered around Days 18 to 20.
  - Constructed a city $\times$ day matrix of confirmed fraud counts and reordered rows and columns by marginal totals to make the pattern pop out visually.
  - Pinpointed an isolated fraud hotspot in **Pune** on Days 18 (13 frauds), 19 (17 frauds), and 20 (10 frauds).
- **Task 2 — Linked Views for "Night-Time, Far-From-Home" Intuition:**
  - Constructed a four-view coordinated layout linked by brushing: (a) hour vs. distance scatter (log scale), (b) merchant category horizontal bars, (c) city counts, and (d) transaction amount histogram ($\log_{10}$).
  - Evaluated investigator rule of thumb (`night == True` and `distance > 100 km`): while the brush has high precision (**86.89%** fraud rate vs. 2.17% baseline), it achieves only **44.17% recall** (capturing 53 out of 120 frauds), leaving **55.83% false negatives**.
- **Task 3 — Dynamic Queries & Response-Time Budgets:**
  - Measured vectorized filtering throughput on a replicated 1,004,640-row table ($14.1$ ms $\to 0.0141\ \mu$s/row), enabling a maximum of $\approx 7.1$M rows within Card & Robertson's hard **0.1-second perceptual immediacy budget**.
  - Demonstrated that querying 50M rows in PostgreSQL takes $\approx 0.70$ s, requiring pre-aggregated Data Cubes (e.g. imMens) or in-memory progressive client subsampling (skipping $\approx 85.8\%$ of raw rows during interactive dragging).
- **Task 4 — Human-in-the-Loop Threshold Steering:**
  - Evaluated automated detector thresholds from 0.05 to 0.95 against investigator workload capacity (maximum 150 reviews/month).
  - Selected capacity-constrained threshold **0.80**, yielding **144 alerts/month**, **70.0% recall**, and **58.3% precision** (vs. unconstrained best $F_1 = 0.637$ at threshold 0.85 with 128 alerts).
  - Demonstrated why visual analytics insists on keeping human domain experts in the loop to steer trade-offs against institutional constraints.
- **Task 5 — Interactive Triage Tools & Hierarchical Drill-Down:**
  - Built an interactive Plotly scatter view with lasso/box selection and rich details-on-demand hover tooltips (cardholder, city, merchant category, distance, detector score) for flagged cases (score $\ge 0.30$).
  - Constructed a two-level hierarchical sunburst diagram (City $\to$ Merchant Category) colored by fraud rate %, revealing heavy fraud concentration in Pune's electronics and gift card segments.
- **Task 6 — Hypothesis Testing & Counter-Example Controls:**
  - Formulated the "Fraud Ring in Pune on Days 18–20" hypothesis ($37.38\%$ fraud rate vs. $1.48\%$ everywhere else; $\chi^2 = 619.29$, $p = 1.06 \times 10^{-136}$).
  - Conducted explicit counter-example controls to guard against confirmation bias:
    1. *Pune on other days:* $1.85\%$ fraud rate (normal baseline $\to$ refutes chronic local bias).
    2. *Other cities on Days 18–20:* $0.44\%$ fraud rate (normal baseline $\to$ refutes nationwide temporal shock).
  - Statistically confirmed the localized fraud ring hypothesis.
- **Task 7 — Executive Decision & Closed-Loop Architecture:**
  - Provided a formal executive memorandum recommending threshold 0.80, prioritizing coordinated linked brushing, and instituting an active learning feedback loop (Slide 51).

---

## Repository Structure
```
├── README.md
├── .gitignore
├── unit3_ppt1_activity.ipynb             # Exercise 1: Parking-Lot Incident Review
├── unit3_ppt1_cctv_log.csv                # Exercise 1: CCTV log dataset
├── unit3_ppt3_activity.ipynb             # Exercise 2: Tumour-Board Briefing
├── unit3_ppt3_head_ct_phantom.csv        # Exercise 2: CT scan phantom dataset
├── Unit3_PPT5_Temporal_Activity.ipynb    # Exercise 3: I-94 Traffic Pulse Temporal Analysis
├── Metro_Interstate_Traffic_Volume.csv   # Exercise 3: Metro Interstate Traffic Volume dataset
├── unit3_ppt7_activity.ipynb             # Exercise 4: Fraud-Ops War Room Analytics
└── unit3_ppt7_card_transactions.csv      # Exercise 4: Card transactions dataset (5,520 rows)
```
