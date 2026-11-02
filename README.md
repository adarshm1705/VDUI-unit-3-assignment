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

## Repository Structure
```
├── README.md
├── .gitignore
├── unit3_ppt1_activity.ipynb   # Fully solved and executed Jupyter Notebook
└── unit3_ppt1_cctv_log.csv      # Telemetry log dataset (3,600 frames)
```
