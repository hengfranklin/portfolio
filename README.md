<h1 align="center">Hi, I'm Franklin 👋</h1>

<p align="center">
  <img src="imgs/banner_photo_2.jpg" alt="banner" width="100%"/>
</p>

<p align="center">
  <b>CV and ML engineer based in NYC, building 0 to 1 systems across <i>healthcare</i>, <i>climate</i>, and <i>robotics</i>.</b>
</p>

<p align="center">
  👉 My most up to date project portfolio lives at <a href="https://hengfranklin.github.io/projects/"><b>hengfranklin.github.io/projects</b></a>.
</p>

<p align="center">
  <a href="https://github.com/hengfranklin/portfolio/raw/main/franklin_heng_resume.pdf">Resume</a> ·
  <a href="https://www.linkedin.com/in/franklinheng/">LinkedIn</a> ·
  <a href="https://scholar.google.com/citations?user=toxljCEAAAAJ">Google Scholar</a> ·
  <a href="mailto:heng.franklin@gmail.com">heng.franklin@gmail.com</a>
</p>

---

## Computer Vision and ML

### Running Tide: computer vision for ocean carbon removal
> *Built the CV stack that verified biomaterial sinking, shellfish growth, and macroalgae biomass at sea.*

<table>
<tr>
<td width="50%" valign="top">

**🌲 Offshore sink-rate tracking**

<img src="imgs/portfolio/p03_8.png" width="100%" alt="Offshore substrate detection with bounding boxes"/>

Verifying ocean carbon removal requires an empirical sinking curve for each batch of biomaterial deployed at sea. The pipeline pulls imagery from custom ocean buoy cameras, filters noisy frames (fog, occlusion, bubbles) through an **AutoML CNN**, then segments wood chips with **Mask R-CNN**, chosen for per pixel accuracy on our ~2,000 image dataset. Pixel area over time is fit to an **exponential decay sink rate curve** that feeds the carbon verification dashboard.

- **Stack:** Mask R-CNN, AutoML CNN, Cloud Composer / Airflow, Docker
- **Eval:** IoU and mAP on segmentation. Sink rate curves reviewed by the oceanography team.

</td>
<td width="50%" valign="top">

**🦪 Robotic shellfish counter**

<img src="imgs/portfolio/shelfish.jpg" width="100%" alt="Robotic shellfish counter oyster detection"/>

Each image from our robotic harvesting platform held ~1,000 shellfish, some as small as 2×2 px. **Faster R-CNN** was chosen because its two stage design allowed anchor box scales to be tuned specifically for that small object regime. A custom post processing pass refines the smallest detections before they hit the counter.

- **Performance:** >90% accuracy at 0.125 s/image
- **Eval:** classification cross entropy, bounding box L1/L2, absolute counting error against human counts

</td>
</tr>
<tr>
<td width="50%" valign="top">

**🌿 Kelp 2D phenotyping (V1)**

<img src="imgs/portfolio/v1_frcnn_detections.png" width="100%" alt="Faster R-CNN per blade detections on an overhead image of a whole kelp plant"/>

Overhead DSLR imaging of whole kelp plants harvested from offshore buoys, with automated per plant measurement of blade count, length, width, and area. The initial classical pipeline (adaptive thresholding, `skimage.skeletonize`, branch point detection, per blade ellipse fitting) was upgraded to **Faster R-CNN (ResNet-101)** after classical methods struggled with overlapping blades and varying lighting.

**Pipeline:**
- Faster R-CNN blade detector on the full overhead image
- Per detection crop, then classical segmentation for area and arc length
- Ruler in every frame for pixel to centimeter calibration
- Dataset wide CSV export, one row per blade with buoy, date, and farm depth

**Hardware (Kelp Photobooth):**
- Shipping container converted into a controlled imaging booth with a white sheet background
- DSLR camera on a **linear rail** mounted to the interior roof, traversing the length of the table
- Controlled interior lighting
- Ruler visible in every frame for pixel to centimeter calibration

📂 [Repo](https://github.com/hengfranklin/kelp-phenotyping)

</td>
<td width="50%" valign="top">

**🌿 Kelp 3D phenotyping (V2)**

<img src="imgs/portfolio/v2_pointcloud.png" width="100%" alt="3D point cloud of a single kelp blade reconstructed with COLMAP"/>

V1 produced per blade 2D statistics, but kelp blades are not flat and 2D projections underestimate biomass. V2 (internally named "Polar Bear") reconstructs individual blades in 3D with absolute metric scale, recovering volume and dimensions in real world units. **Structure from Motion** was chosen because the controlled rig produced the image overlap and repeatability SfM needs, at a fraction of the hardware cost of active depth sensing.

**Pipeline:**
- **COLMAP** for sparse SfM from overlapping multi view stills
- **Agisoft Metashape** for dense photorealistic cloud and background removal
- **Open3D** for statistical outlier removal, voxelization, convex hull volume, and oriented bounding box
- **ArUco marker** of known physical size to recover absolute metric scale (cm, cm³)

**Eval:** bundle adjustment reprojection error and number of registered images for reconstruction quality. Biological metrics validated against hand measurements of the same physical blade.

**Hardware (Polar Bear rig):**

<img src="imgs/portfolio/p10_1.png" width="100%" alt="Polar Bear rig with LUCID industrial RGB camera, linear rail, color calibration card, ArUco target, and rotating sample fixture"/>

- **LUCID Vision Labs TRI162S-C** industrial RGB camera (GigE) on a **linear rail**
- **24 patch color calibration card** in every frame for cross session colorimetry
- **ArUco marker board** in the scene for scale and pose reference
- Controlled lighting to minimize specular highlights on wet blades
- Rotating sample fixture with tunable speed to adjust SfM overlap
- Automatic upload from rig to GCS to reconstruction worker

📂 [Repo](https://github.com/hengfranklin/kelp-phenotyping) · 📄 [NAPPN 2022](https://www.authorea.com/users/510851/articles/588338-nappn-annual-conference-abstract-computer-vision-based-phenotyping-approaches-in-the-brown-macroalgae-saccharina-latissima)

</td>
</tr>
</table>

---

### UCSF: clinical computer vision (Fahy, Abbasi, Sohn Labs)

<table>
<tr>
<td width="33%" valign="top">

**🫁 Mucus plug segmentation**

<img src="imgs/portfolio/p13_1.png" width="100%" alt="Mucus plug segmentation in chest CT"/>

Mucus plugs in lung airways are a key driver of airflow limitation in asthma, but counting and measuring them from CT had been a manual radiology task. I built an automated pipeline that detects, segments, and measures plugs in 3D, then maps each one back to its airway generation.

**Pipeline:**
- Intensity threshold lung segmentation with region growing to isolate airways
- **GK fuzzy clustering** for plug isolation, chosen over hard thresholding because it handles partial volume effects at tissue boundaries
- **Mask R-CNN** for per plug instance segmentation so each plug can be counted individually
- **Marching cubes** 3D mesh, then **PCA cylinder fitting** for length and width measurement

Plugs were classified **stubby (≤12 mm) vs. stringy (>12 mm)** and used to derive a novel airway resistance score that correlated with FEV₁ decline across **580+ patients**. Segmentation evaluated on IoU and mAP. Outputs reviewed by the radiology team.

📄 [*JCI Insight* (2023)](https://insight.jci.org/articles/view/174124)

</td>
<td width="33%" valign="top">

**🧠 Brain age prediction**

<img src="imgs/portfolio/brain_age_p3.png" width="100%" alt="Brain age regional segmentation pipeline"/>

The gap between a patient's predicted "brain age" and their actual age can be a signal of underlying systemic disease. I trained a custom **3D CNN** on brain CT scans to predict brain age, then analyzed the prediction gap against a panel of systemic conditions.

- **Model:** 3D CNN with volumetric regional segmentation, 8 regions per scan
- **Analysis:** GLM correlating predicted brain age gap against the systemic disease panel

</td>
<td width="33%" valign="top">

**⚡ EEG engagement decoding**

<img src="imgs/portfolio/nmf_eeg_p16.png" width="100%" alt="NMF coefficient analysis with scalp topography maps"/>

Decoding attentional engagement from raw EEG needed a representation that stayed interpretable across subjects. **NMF** was chosen because its additive decomposition produces coefficient patterns that map directly onto the scalp.

- **Preprocessing:** Morlet wavelet spectrograms from 64 channel EEG
- **Decomposition:** NMF across trials
- **Findings:** coefficient analysis reveals distinct scalp topography patterns between engaged and disengaged states

</td>
</tr>
</table>

---

### Breathily: contactless lung function for ALS patients
> *🥈 2nd place, UC Launch · Funded by UCSF Catalyst · NSF I-Corps · 📜 [US Patent](https://patents.google.com/patent/US20240090795A1/en)*

<p align="center">
  <a href="https://www.youtube.com/watch?v=MaBf3D1GvQA">
    <img src="https://img.youtube.com/vi/MaBf3D1GvQA/maxresdefault.jpg" width="850" alt="Breathily demo video"/>
  </a><br/>
  <sub>▶️ <a href="https://www.youtube.com/watch?v=MaBf3D1GvQA">Watch the demo</a></sub>
</p>

<p align="center">
  <img src="imgs/portfolio/breathily_pipeline.png" width="850" alt="Clinical study data collection pipeline: depth image → skeleton tracking → chest region → segmented regions → VISION vs. spirometry curves"/><br/>
  <sub>Clinical study data collection pipeline: depth and color → skeleton tracking → chest region segmentation → segment wise depth signals → Breathily vs. spirometer curves</sub>
</p>

Patients with ALS and other neuromuscular diseases often cannot form a seal around a traditional spirometer mouthpiece, which makes standard pulmonary function testing impossible for them. We co-founded Breathily to make spirometry work without a mouthpiece. **Intel RealSense depth cameras** capture chest wall movement, and computer vision converts that movement into the full PFT panel (FVC, FEV1, PEF).

**How it works:**
- **Cubemos and MediaPipe** for real time pose estimation, used to locate the chest wall ROI on each frame
- **RealSense SDK** for depth to metric conversion, native to the sensor so no external calibration is needed
- A scaling factor (reference spirometer volume ÷ raw chest displacement) converts the 1D breathing waveform into L and L/s

**Stack:** Intel RealSense, Cubemos, OpenCV, SciPy, Pixel2Mesh, CNN+LSTM (Keras)

**Eval:** correlation coefficient, RMSE, and MAE against a paired spirometer in an **IRB approved patient study at UCSF Pulmonary Function Lab**.

**Hardware Setup**

<table>
<tr>
<td width="50%" valign="top">

<img src="imgs/portfolio/breathily_hardware_sketch.png" width="100%" alt="Initial hardware design sketches and component photos"/>

<sub><b>Design</b>: hand drawn layout with Intel NUC and Arduino components.</sub>

</td>
<td width="50%" valign="top">

<img src="imgs/portfolio/breathily_3d_printed.png" width="100%" alt="Final 3D printed Breathily enclosure"/>

<sub><b>Build</b>: final 3D printed enclosure with camera post and screen recess.</sub>

</td>
</tr>
</table>

The final system is a custom 3D printed enclosure housing the **Intel RealSense** camera on an adjustable post, an **Intel NUC** mini PC for vision processing, and an **Arduino driven touchscreen** for technician control, with compartments for battery and storage. The rig was deployed at the UCSF Pulmonary Function Lab for the clinical study, seated alongside the reference spirometer.

📂 [Repo](https://github.com/hengfranklin/Breathily)

---

### UC Berkeley: agricultural robotics CV

<p align="center">
  <img src="imgs/portfolio/p06_2.png" width="850" alt="Sorghum stem width estimation pipeline"/>
</p>

Estimated **sorghum stem width** from video captured by a moving robotic platform. **Faster R-CNN** locates stems in each frame, then an edge based refinement stage recovers the stem boundary before depth based metric conversion.

**Pipeline:**
- **Faster R-CNN** for stem detection
- **Wiener filter** for noise reduction before edge detection
- **Canny edges and morphological operations** to isolate stem boundaries
- **RANSAC** for a clean boundary fit
- Metric width computed from the paired **depth image**

📄 [*Sahiner, Heng, Balamurugan, Zakhor.* **"In Situ Width Estimation of Biofuel Plant Stems"**, Electronic Imaging 2019](https://library.imaging.org/ei/articles/31/13/art00009)

---

## Full Stack AI Engineering

<table>
<tr>
<td width="33%" valign="top">

#### Make The Dot

<img src="mtd-portfolio/docs/assets/output-hero.png" width="100%"/>

Designers needed photorealistic garment renders from a hand sketch, a color, and a fabric spec, without retraining a model each time a new material was added. I owned the end to end pipeline: base model, control signals, LoRA training, and GPU serving.

**Model stack:**
- **SDXL Juggernaut v10** as the base, chosen for its curated photorealistic training data
- **ControlNet-Union** for structural adherence to the sketch
- **Custom LoRAs** chosen over IP-Adapter and full fine tuning because they support weighted blending at inference (e.g. *faded = light @0.9 + bleach @0.3*) without retraining

**Serving:** NVIDIA Triton and TensorRT on H100, with multi LoRA blending and zero downtime hot swap via Hyperdisk ML on GKE.

**Eval:** weighted MSE noise prediction loss during training. An in house A/B tool where the design team rated experimental vs. production outputs (worse, same, better) for promotion decisions.

📂 [Repo](https://github.com/hengfranklin/mtd-portfolio)

</td>
<td width="33%" valign="top">

#### FOIA Fluent

<img src="FOIA-Fluent/docs/images/homepage.png" width="100%"/>

Civic AI that finds public records and drafts legally optimized FOIA, FOIL, CPRA, and PIA requests. **Grounded drafting**: Claude writes only from verified statute text, eCFR regulations, and MuckRock outcomes, which prevents hallucinated legal citations.

📂 [Repo](https://github.com/dssg-nyc/FOIA-Fluent) · 🔗 [Website](https://www.foiafluent.com)

</td>
<td width="33%" valign="top">

#### GridTech
🥈 *Cross-Columbia GridTech Hackathon 2026*

<img src="gridtech-hack/gridtech/docs/screenshots/homepage.png" width="100%"/>

Consolidates fragmented NY energy efficiency programs (Smartcharge NY, SCERP, IRA 30C), surfaces eligibility, and gives utilities a dashboard targeting underutilized programs and grid congestion.

📂 [Repo](https://github.com/hengfranklin/gridtech-hack) · 🔗 [Website](https://gridtech.vercel.app/)

</td>
</tr>
</table>

---

## Earlier work

🛰️ **NASA JPL**. Researched image processing methods to align and segment multi wavelength infrared sensor arrays. 📄 [Paper 1](https://www.spiedigitallibrary.org/conference-proceedings-of-spie/9845/984505/Cross-correlation-and-image-alignment-for-multi-band-IR-sensors/10.1117/12.2224694.short), [Paper 2](https://www.spiedigitallibrary.org/conference-proceedings-of-spie/10203/1/Intelligent-multi-spectral-IR-image-segmentation/10.1117/12.2262730.short).

📱 **Samsung SARC**. Researched mobile object detection architectures and contributed to a C++ GPU simulator modeling Samsung's mobile GPU.

---

## Selected publications & patents

| Year | |
|---|---|
| 2025 | Lee J., **Heng F.**, Kesarwani M. *"Medication Beliefs regarding P2Y12 inhibitors..."*. **Cureus** *(NLP, Health)* |
| 2023 | Huang B., …, **Heng F.**, …, Fahy J. *"Mucus plugs in proximal airway generations are consequential for airflow limitation in asthma"*. **JCI Insight** *(CV, Health)* |
| 2022 | **Heng F.**, Orain X. *"Methods for Pulmonary Function Testing With Machine Learning..."*. **US Patent**, filed Feb 2022 |
| 2022 | **Heng F.**, Belanger J., et al. *"Computer vision based phenotyping in Saccharina Latissima"*. **NAPPN** *(CV, Climate)* |
| 2019 | Sahiner A., **Heng F.**, Balamurugan A., Zakhor A. *"In Situ Width Estimation of Biofuel Plant Stems"*. **Electronic Imaging** *(CV, Robotics)* |

[Full list on Google Scholar →](https://scholar.google.com/citations?user=toxljCEAAAAJ)

---

## Toolbelt

| | |
|---|---|
| **CV & ML** | PyTorch · TensorFlow · Keras · Hugging Face Diffusers · OpenCV · Scikit-Image · SAM · ControlNet · LoRA |
| **3D Vision** | COLMAP · Metashape · Open3D · Intel RealSense · Lidar |
| **Serving & MLOps** | NVIDIA Triton · TensorRT · Docker · GKE · Vertex AI · Cloud Run · Cloud Composer / Airflow · Celery |
| **Web** | FastAPI · Next.js · Supabase · Postgres · Redis |
| **Languages** | Python · C++ · MATLAB · TypeScript |

---

## VLM and emerging tools I track

| | |
|---|---|
| **Transformer based detectors** | RF-DETR (DINOv2 backbone, CNN feature extractor), YOLOv12 (attention integrated) |
| **Open source VLMs** | Qwen3-VL, Cosmos Reasoning (tuned on physics and robotics data, built on Qwen3-VL), YOLO-World (CLIP text encoder), Gemma 3 |
| **Proprietary VLMs** | Gemini 2.5, Claude Sonnet 4, Claude Opus, GPT-4, OpenAI o3 |
| **Architecture patterns** | Mixture of Experts (MoE): sparse gating and routing over feed forward layers. Cuts inference cost but introduces routing instability and expert imbalance challenges. |

---

## Education

🐻 **B.A. Computer Science**, UC Berkeley

---

## Fun facts

- 🚑 **EMT**, National Registry of EMTs (2024)
- 🤿 **Open Water Scuba Diver**, SSI (2022)
- 🦖 All CV/ML projects above (except the ones listed under Full Stack AI Engineering) were built before the agentic AI era.

---

## Get in touch

📧 **heng.franklin@gmail.com** &nbsp;·&nbsp; 💼 [LinkedIn](https://www.linkedin.com/in/franklinheng/)