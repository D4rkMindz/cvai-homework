# CVAI HS26 Homework – Shortlist (internal)

Coach: Joachim Wirth · Team of 3 · Budget: < 50 h total (≈ 15 h each) · Presentation: Thu/Fri 17.–18.12.2026 (SW14)

Shared ground rules for every idea:

- **Classical core** from Wirth's lectures (SW01-2 – SW04-2): color spaces/HSV, point operators, morphology, smoothing, convolution, Canny, Hough, SIFT matching. A pretrained deep model is at most a bonus comparison.
- **Python + OpenCV in Jupyter, locally.** AI coding assistants allowed.
- **Small quantitative evaluation** in every project (one metric, ~20–50 labelled or measured samples). That's what lifts it from "nice demo" to full points.
- Demo: pre-computed results or live, decided per idea.

No grading rubric is available. Assumption: working result + clear presentation + evaluation + honest failure cases = 10 points.

---

## Ranking (for "10 points with little effort")

| Rank | Idea | Effort | Wirth fit | Risk | Fun / presentability |
|---|---|---|---|---|---|
| 1 | **S-2** swisstopo land cover | ~20–25 h | ★★★ color + morphology | low | ★★ |
| 2 | **C-2** EuroSAT land use | ~20–25 h | ★★ color hist + Canny | low | ★★ |
| 3 | **S-1** Rail track detection | ~25 h | ★★★ Canny + Hough | medium (curves) | ★★★ |
| 4 | **3D-1** Loco: COLMAP vs LiDAR | ~30–35 h | ★★ SIFT matching | medium | ★★★ |
| 5 | **3D-2** Relief model | ~30 h | ★★ SIFT matching | medium | ★★ |
| 6 | **C-3** Swiss train types | ~30–35 h | ★★ SIFT BoVW | medium–high | ★★★ |

My pick: **S-2** if you want the safest path, **S-1** if you want the best fit with Wirth's material plus the railway theme.

---

## 3D-1 – Locomotive at Verkehrshaus Luzern: photogrammetry vs. iPhone LiDAR

**Question:** How accurate is a photo-only 3D reconstruction compared to a LiDAR scan, and how many photos do you need?

- **Data:** One locomotive at Verkehrshaus Luzern. ~80 phone photos walking around it (overlapping, constant exposure) plus one LiDAR scan with a free iPhone app (e.g. Scaniverse, 3D Scanner App, Polycam). Tape-measure 3–5 real dimensions (wheel diameter, buffer height, door width).
- **Tools:** COLMAP for the reconstruction (sparse on Mac; **dense needs the NVIDIA machine**). MeshLab or CloudCompare to measure distances in the models.
- **Own code (small):** OpenCV notebook: SIFT keypoints + ratio-test matching + RANSAC on an image pair, with inlier visualisation (extends exercise 7). Image-count study: reconstruct with 20 / 40 / 80 photos.
- **Evaluation:** COLMAP's scale is arbitrary, so scale it with *one* measured dimension and evaluate the *others*. LiDAR is metric. Report the % error per dimension for both methods and per image count.
- **Risks:** Crowds and mixed indoor lighting. Shiny paint and windows give poor features. You may not get all the way around the vehicle. Pick a matte, freestanding loco; go early on a weekday.
- **Split:** A = photo session + COLMAP runs · B = LiDAR scan + measurements in MeshLab/CloudCompare · C = SIFT notebook + slides.
- **Effort:** visit 4 h · COLMAP 6–8 h · LiDAR + measuring 4 h · SIFT notebook 6 h · evaluation 4 h · slides 6 h ≈ **30–35 h**

## 3D-2 – Relief model reconstruction with COLMAP

**Question:** How well does photogrammetry reconstruct a physical terrain relief, and what limits it (texture, lighting, number of images)?

- **Data:** ~50–100 photos of a relief model you have access to. Measure a few distances and heights on it (e.g. base edges, height of the highest peak above the base).
- **Tools:** COLMAP (dense on the NVIDIA machine), MeshLab for measuring.
- **Own code (small):** Same SIFT matching notebook as 3D-1, plus feature statistics: keypoints per image in textured vs. smooth regions, effect of contrast enhancement (CLAHE/unsharp masking from SW01-2/SW02-1) on the number of matches.
- **Evaluation:** Scale with one known length, report the error on the other measured lengths and heights. Image-count study (25 / 50 / 100).
- **Optional bonus (skip if short on time):** Qualitative comparison of one terrain profile with swissALTI3D (free swisstopo elevation model).
- **Risks:** Relief behind glass → reflections. Smooth uniformly-colored areas → few features (but that's a finding, not a failure).
- **Split:** A = photos + COLMAP · B = measurements + evaluation · C = SIFT/contrast notebook + slides.
- **Effort:** ≈ **30 h**

## S-1 – Rail track detection with Canny + Hough

**Question:** How far do classical edge and line detection get on rail tracks from the driver's view?

- **Data:** **RailSem19** (8,500 images from train/tram ego-view, pixel-level rail annotations; free download after registration on wilddash.cc). Use a subset of ~50–100 images.
- **Method:** Grayscale → Gaussian smoothing → Canny → region of interest (trapezoid in front of the train) → probabilistic Hough lines → filter by angle/position → draw the track. Parameters: sigma, Canny thresholds, Hough votes.
- **Evaluation:** Compare the detected rail pixels (lines dilated to rail width) with the RailSem19 rail masks: IoU or hit rate per image. Small parameter study. Split results into straight vs. curved tracks and day vs. night.
- **Risks:** Curves break the straight-line assumption (expected; show it as a limitation). Switches and multiple tracks. The ground-truth format needs a few hours to understand.
- **Split:** A = data loading + mask/metric code · B = Canny/Hough pipeline · C = parameter study, failure gallery, slides.
- **Effort:** data setup 4 h · pipeline 6 h · evaluation 6 h · parameter study 3 h · slides 6 h ≈ **25 h**

## S-2 – Land cover from swisstopo national maps (HSV + morphology)

**Question:** Can you segment forest and water from Swiss national map tiles with color thresholds alone, and what % of a region is forest?

- **Data:** swisstopo Landeskarte raster tiles (e.g. 1:25,000; free open data from swisstopo). Pick a region like canton Luzern / Lake Lucerne.
- **Method:** RGB → HSV → thresholds for forest-green and lake-blue → morphological opening/closing to remove text, contour lines and symbols → connected components (drop small blobs) → area % per class. Optional third class: buildings (dark pixels + morphology).
- **Evaluation:** Hand-label ~20 small tiles (forest/water/other) and compute IoU per class. Optional sanity check: computed forest % vs. officially published forest share of the region.
- **Risks:** Labels, contour lines and hatching sit on top of colored areas → morphology tuning. Water vs. other blue symbols (rivers are thin lines). Both are good discussion points.
- **Split:** A = tile download + hand labelling · B = HSV/morphology pipeline · C = evaluation + area statistics + slides.
- **Effort:** data 3 h · pipeline 6 h · labelling 3 h · evaluation 4 h · slides 6 h ≈ **20–25 h**

## C-2 – Land-use classification on EuroSAT (classical features)

**Question:** How well do hand-crafted color and edge features classify satellite image patches?

- **Data:** **EuroSAT RGB**: 27,000 labelled 64×64 Sentinel-2 patches, 10 classes (AnnualCrop, Forest, HerbaceousVegetation, Highway, Industrial, Pasture, PermanentCrop, Residential, River, SeaLake). Download directly, e.g. via `torchvision.datasets.EuroSAT`.
- **Method:** Features per patch: HSV color histograms, plus edge density / gradient-orientation histogram from Canny/Sobel (SW04-1). Classifier: kNN and/or SVM (scikit-learn).
- **Evaluation:** Train/test split, accuracy + confusion matrix. Feature ablation (color only / edges only / both). Discuss which classes get confused (e.g. Highway vs. River) and why.
- **Optional bonus:** Pretrained CNN features + the same SVM as a comparison.
- **Risks:** Low. Mainly "accuracy isn't impressive", but the ablation and confusion analysis are the story.
- **Split:** A = data + feature extraction · B = classifiers + ablation · C = confusion analysis, example gallery, slides.
- **Effort:** setup 2 h · features 6 h · classifiers 4 h · analysis 4 h · slides 6 h ≈ **20–25 h**

## C-3 – Swiss train type recognition (classical vs. pretrained features)

**Question:** Can you tell Swiss train types apart from photos, and how much do deep features beat classical ones?

- **Data:** Own photos at Luzern station: 3–4 types you see often (decide on site; e.g. FLIRT, IC2000 double-decker, Re 460-hauled trains). ~30–40 photos per class, varied angles and distances.
- **Method (classical):** HSV color histograms + SIFT bag of visual words (k-means vocabulary) → SVM.
- **Planned fallback / comparison:** Pretrained ResNet (torchvision, no training) as feature extractor + the same SVM. Guarantees a good result and gives a "classical vs. deep features" slide. Adds ~3–4 h.
- **Evaluation:** Held-out test set, accuracy + confusion matrix for both feature types.
- **Risks:** Highest of all six. Shared SBB livery makes color features weak. Data collection takes time. Background (platforms) can dominate the features → crop to the train.
- **Split:** A = photo sessions + cropping/labelling · B = classical pipeline · C = ResNet features + comparison + slides.
- **Effort:** photos + labelling 8 h · classical 8 h · ResNet comparison 4 h · evaluation 4 h · slides 6 h ≈ **30–35 h**

---

## Next steps

1. Pick one idea as a team (or two, and let Wirth comment).
2. Quick feasibility check (~1 h): download the data (RailSem19 registration, swisstopo tiles, EuroSAT) or take 10 test photos.
3. Register the topic with the coach.
