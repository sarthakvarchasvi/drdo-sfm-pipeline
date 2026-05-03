# 📷 DRDO SfM Pipeline — 3D Photogrammetry from Drone Imagery

> **Internship Project | DRDO Centre for Artificial Intelligence and Robotics (CAIR), Bengaluru**  
> **Mentor:** Dr. Sumit Veerwal | **Duration:** June – July 2025  
> **Goal:** City-scale 3D digital twin from aerial drone imagery

[![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=flat-square&logo=opencv&logoColor=white)](https://opencv.org)
[![COLMAP](https://img.shields.io/badge/COLMAP-1A3A5C?style=flat-square)](https://colmap.github.io)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://docker.com)

---

## Overview

A complete Structure-from-Motion (SfM) pipeline for reconstructing geometrically accurate 3D models of real-world environments from aerial drone footage — built during a one-month research internship at DRDO CAIR under Dr. Sumit Veerwal.

The end goal was a **city-scale digital twin** usable for terrain mapping, reconnaissance, and change detection in defence applications.

---

## What I Built

### Stage 1 — Theory & Manual Implementation
Before touching industrial tooling, I implemented SIFT from scratch to build genuine pipeline understanding:

- **Difference-of-Gaussian (DoG) pyramid** — Gaussian blur at multiple scales, DoG computation across octaves
- **Scale-space extrema localisation** — 3D neighbourhood comparison across 26-pixel cubes
- **Keypoint localisation** — Hessian matrix eigenvalue ratio test, contrast threshold filtering
- **Orientation assignment** — gradient histogram with 36 bins, dominant orientation extraction  
- **128-D descriptor generation** — 4×4 subregion histograms, L2 normalisation

> *Reference: Studied Cyrill Stachniss Photogrammetry I & II lectures and "Building Rome in a Day" (Agarwal et al., ICCV 2011)*

### Stage 2 — Industrial Pipeline (COLMAP + PyCOLMAP)
- Feature extraction and exhaustive matching via COLMAP
- Incremental Structure-from-Motion for sparse point cloud + camera trajectory
- Dense reconstruction (Poisson mesher) → fused.ply
- Switched to PyCOLMAP for scripted, automatable batch reconstruction
- Metric scaling via Meshlab: used known reference dimensions to transform scene to real-world coordinates

### Stage 3 — Dockerisation
- Built a `~3.5 GB` Docker container with CUDA support, OpenCV (GUI/X11), COLMAP, Meshlab
- Resolved GPU passthrough (`nvidia-docker2`), X11 forwarding, and GUI rendering inside container
- Verified integrity with checksum file; tested on DRDO lab computers

### Stage 4 — Real-World Video Input
- Python script to extract frames from drone video at configurable frame rates
- Tested on urban and semi-structured scenes

---

## Datasets Tested

| Dataset | Images/Frames | Notes |
|---|---|---|
| Flowerpot (tabletop) | 25 images | Object-scale baseline |
| Church (Echillais) | 21 images | Architectural detail test |
| DRDO Parking Area | 167 frames (3-min video) | Real-world campus test |
| Building drone footage | 274 frames (1.5-min video) | Urban aerial |
| St Paul's Cathedral, Malta | 83 frames (2.5-min video) | City-scale test |

---

## Results

The pipeline successfully produced:
- Sparse point clouds with accurate camera trajectories
- Dense textured 3D models scaled to metric accuracy
- City-block scale reconstruction (Malta dataset) demonstrating scalability to defence-relevant scenarios

---

## Repository Structure

```
drdo-sfm-pipeline/
├── sift_from_scratch/
│   ├── SIFT.ipynb                    # Core SIFT implementation
│   ├── SIFT_Multiple_Filtered.ipynb  # Multi-image SIFT with ratio test
│   └── Points_Reconstruction.ipynb   # Manual triangulation + .ply export
├── colmap_pipeline/
│   ├── Colmap_Working.ipynb          # Full COLMAP + PyCOLMAP pipeline
│   └── Dense_Reconstruction.ipynb    # Dense reconstruction workflow
├── utils/
│   └── Video_Segmentation.ipynb      # Video → frames extraction
└── README.md
```

---

## Key Learnings

The manual SIFT implementation before moving to COLMAP was intentional — debugging the DoG pyramid and descriptor normalisation directly in NumPy built an understanding of what COLMAP is actually doing, which made troubleshooting the industrial pipeline significantly easier. The transition from "why is my reconstruction failing" to "I know exactly which matching step to check" happened because of this ground-up approach.

---

*Part of internship at DRDO CAIR, Bengaluru | June – July 2025*
