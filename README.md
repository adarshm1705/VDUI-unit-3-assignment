# VDUI Unit 3 Assignment: Multimedia and Interactive Visualization
**Course:** UE24CS342AA9 — Visualization of Data and User Interfaces (VDUI)  
**Institution:** PES University  
**Student Name:** Adarsh Shivanand Madli  
**Student SRN:** PES2UG24CS028  

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

## Exercise 2: The Tumour-Board Briefing (`unit3_ppt3_activity.ipynb`)

### Clinical Scenario
As visualization engineer in the 3-D Imaging Lab at City General Hospital, prepared the diagnostic briefing for the neuro-oncology tumor board (neurosurgeon, radiologist, oncologist) reviewing a patient head CT phantom (`unit3_ppt3_head_ct_phantom.csv`).

### Summary of Key Tasks & Findings
1. **IT Department Numbers & PACS Budgeting (Task 1):**
   - **Voxel Count ($512 \times 512 \times 300$):** $78,643,200$ voxels ($78.6\text{ Mvoxels}$).
   - **Storage Size (16 bits/voxel):** **$150.0\text{ MB}$** ($78,643,200 \times 2 / 1024^2$).
   - **Physical Extent ($0.5 \times 0.5 \times 1.0\text{ mm}$):** $256.0\text{ mm} \times 256.0\text{ mm} \times 300.0\text{ mm}$, with voxel volume $= 0.25\text{ mm}^3$.
   - **Ray-Casting Computation:** 1 front-view frame ($512 \times 300$ rays $\times 512$ samples) requires **$78,643,200\text{ samples}$**; a 120-frame $360^\circ$ rotation video costs **$9,437,184,000\text{ samples}$** ($9.44\text{ billion}$).
   - **Early Ray Termination (ERT):** Terminating rays after 35% of samples on average cuts the video computation down to **$3,303,014,400\text{ samples}$** (saving 65% computational work).
   - **Voxel Anisotropy Reflection:** Failing to correct for anisotropic $z$-spacing ($1.0\text{ mm}$ vs $0.5\text{ mm}$) squashes coronal and sagittal views by 50% ($2:1$ vertical distortion) and causes a $2\times$ measurement underestimation and boundary stepping artifacts.

2. **Lesion Localization & Multiplanar Reconstruction (Task 2):**
   - **Autonomous Localization:** Stripped skull with binary hole filling, applied 3 iterations of binary erosion to eliminate skull-soft tissue partial-volume contamination, and filtered for $55 < \text{HU} < 90$ inside the parenchyma.
   - **Centroid Coordinates:** Computed integer centroid at **$z=28, y=40, x=41$** (discrete error $\le 1$ voxel, continuous sub-voxel error $= 0.24\text{ mm}$ against ground-truth label 5).
   - **MPR Display:** Generated linked axial, coronal, and sagittal orthogonal slice views with calibrated brain window ($W = 80, L = 40$), localized cross-hairs, and aspect ratio correction ($\text{ASPECT} = 4.0 / 3.0 = 1.33$ for coronal and sagittal planes).

3. **Tumour Volume & Threshold Sensitivity Analysis (Task 3):**
   - Evaluated isosurface segmentation thresholds $c \in [45, 50, 55, 60, 65, 70]\text{ HU}$:
     - $c = 45\text{ HU}$: 535 voxels $\rightarrow 19.26\text{ mL}$ (Dice = 0.9289)
     - **$c = 50\text{ HU}$ (Optimal):** 464 voxels $\rightarrow \mathbf{16.70\text{ mL}}$ (Dice = **0.9935**)
     - $c = 55\text{ HU}$: 431 voxels $\rightarrow 15.52\text{ mL}$ (Dice = 0.9631)
     - $c = 60\text{ HU}$: 350 voxels $\rightarrow 12.60\text{ mL}$ (Dice = 0.8600)
     - $c = 65\text{ HU}$: 273 voxels $\rightarrow 9.83\text{ mL}$ (Dice = 0.7408)
     - $c = 70\text{ HU}$: 170 voxels $\rightarrow 6.12\text{ mL}$ (Dice = 0.5363)
   - **Volume Sensitivity:** Volume drops $> 3\times$ across this narrow range. Reported clinical volume range is **$15.5\text{ mL}$ to $19.3\text{ mL}$** (nominal $16.7\text{ mL}$).
   - **Uncertainty Reflection:** In practice without ground truth, $c$ is justified via spatial gradient inflection points ($|\nabla V|$) and tissue density baselines, and visualized using multi-contour uncertainty shells and confidence interval reporting.

4. **3-D Direct Volume Rendering with Designed Transfer Function (Task 4):**
   - **Limitation of Bone Preset:** Opaque bone ($\alpha \ge 0.3$) completely occludes internal brain and tumor structures from front and lateral viewpoints.
   - **Custom Multi-Material Transfer Function:**
     - Air/Scalp/Brain: Fully transparent ($\alpha = 0$).
     - Skull Bone ($400\text{–}1500\text{ HU}$): Subtle translucent white context ($\alpha = 0.005 \le 0.01$).
     - CSF / Ventricles ($5\text{–}20\text{ HU}$): Faint blue ($\alpha = 0.04$).
     - Tumour Core ($62\text{–}80\text{ HU}$): Bright red ($\alpha = 0.12$).
   - **Multi-View Rendering:** Rendered side-by-side comparison: Bone Front View, Tumour Front View ($y$-axis ray casting), and Tumour Top/Axial View ($z$-axis ray casting), revealing the lesion's lateral spatial relation to the lateral ventricles.
   - **Partial-Volume Reflection:** The faint red rim along the inner skull is caused by 1-D intensity overlap at bone interfaces; it cannot be removed by a 1-D TF alone without hollowing the tumor, but is resolved using anatomical segmentation masking or 2-D transfer functions incorporating gradient magnitude ($|\nabla s|$).

5. **Tumour Board Executive Recommendation (Task 5):**
   - Formulated a 5-sentence clinical briefing detailing tumor coordinates, volume uncertainty range ($15.5\text{–}19.3\text{ mL}$), and recommending opening with MPR and contour overlay to eliminate depth occlusion and preserve metric accuracy, while using 3-D rendering strictly for surgical trajectory and ventricular orientation.

---

## Repository Structure
```
├── README.md
├── .gitignore
├── unit3_ppt1_activity.ipynb        # Exercise 1: Solved and executed notebook
├── unit3_ppt1_cctv_log.csv           # Exercise 1: 3,600-frame CCTV telemetry log
├── unit3_ppt3_activity.ipynb        # Exercise 2: Solved and executed notebook
└── unit3_ppt3_head_ct_phantom.csv   # Exercise 2: 196,608-voxel Head CT dataset
```
