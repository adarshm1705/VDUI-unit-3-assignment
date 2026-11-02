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

## Exercise 3: The I-94 "Traffic Pulse" Temporal Visualization (`Unit3_PPT5_Temporal_Activity.ipynb`)

### Overview
For the Minnesota Department of Transportation (MnDOT), this exercise delivers a temporal visual analytics study on hourly westbound traffic along Interstate 94 between Minneapolis and St. Paul (2012–2018; 48,204 rows).

### Summary of Tasks & Findings
- **Part A — Temporal Data Structure & Gap Analysis:**
  - Resolved duplicate timestamps and constructed a monotonic hourly time series index.
  - Calculated the modal sampling interval ($\Delta t = 1$ hour) and identified a major sensor outage gap of 308 days in 2014–2015.
  - Distinguishes time as a Key attribute (independent indexing coordinate) vs. Value attribute (quantitative duration/interval).
- **Part B — Vendor Sampling Proposal & Nyquist Aliasing:**
  - Proved that the vendor's proposal to sample once every 25 hours violates the Nyquist criterion ($f_s > 2f$, $\Delta t_{\text{Nyquist}} < 12$ hours) for the fundamental 24-hour traffic cycle.
  - Demonstrated that 25-hour sampling causes stroboscopic aliasing, folding the diurnal commuter peak into a deceptive phantom 25-day cycle ($f_{\text{alias}} = |1/24 - 1/25| = 1/600\text{ h}^{-1}$).
- **Part C — Aggregation and Smoothing Dynamics:**
  - Compared 7-day centred vs. 7-day trailing moving averages for 2017 daily traffic totals.
  - Proved that trailing moving averages introduce an asymmetric phase delay of 3 days ($(k-1)/2$), shifting holiday anomalies (e.g., 4th of July dip) to July 7th.
  - Analyzed visual resolution and display capacity: 8,760 hourly points mapped across 1,000 horizontal pixels forces $\approx 8.7$ points per pixel, necessitating aggregation or filtering.
- **Part D — Filtering and Highway Lane Closure Scheduling:**
  - Filtered Mon–Fri non-holiday rush-hour baselines from holiday/weekend patterns.
  - Identified the optimal daytime lane-closure maintenance window between 9:00 and 15:00: **10:00 AM** is the quietest weekday start hour with minimal commuter disruption.
- **Part E — Animated "Traffic Pulse" & Perception Principles:**
  - Calculated frame budget ($33.33$ ms at 30 fps), smoothstep easing trajectory ($s(u) = 3u^2 - 2u^3$), animation duration ($18.0$ s for 24 hourly states), and temporal compression ($4800:1$).
  - Constructed Plotly animated bar chart across months with fixed y-axis scale ($[0, 7500]$), preserving Heer & Robertson's principle of **Congruence**.
  - Compared animation vs. small multiples: verified Colin Ware's principle that *"eyes beat memory"*, demonstrating why faceted small multiples enable rapid, accurate visual comparisons without working memory fatigue.
- **Part F — Recommendations for Engineering & Public Lobby Displays:**
  - Road-works engineers: static small multiples and quantile/confidence interval profiles.
  - Public lobby display: fixed scales, smoothstep easing transitions, prominent clock glyphs, and visual cues to mitigate change blindness.

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
└── Metro_Interstate_Traffic_Volume.csv   # Exercise 3: Metro Interstate Traffic Volume dataset
```
