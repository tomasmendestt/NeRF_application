# Neural Radiance Fields (NeRF) - Real-Time Scene Reconstruction

## Overview

This project implements a full pipeline for 3D scene reconstruction using Neural Rafiance Fields (NeRF).
The The reconstruction process starts from a video sequence. Frames are extracted and used to estimate camera poses via Structure-from-Motion. A neural radiance field is  then trained to learn a continuous volumetric representation of the scene, enabling novel viewe synthesis.

The implementation is based on:

Nerfstudio – https://github.com/nerfstudio-project/nerfstudio

---

## Project Pipeline

The complete workflow consists of:

1. Image acquisition
2. Dataset Processing (`ns-process-data`)  
3. Neural Field Training (`ns-train`)  
4. Interactive Visualization (`ns-viewer`)  

---

## Clone the Repository

```bash
git clone https://github.com/tomasmendestt/NeRF_application --recursive
cd gaussian-splatting
```
---

## Create the Conda Environment

```bash
conda create -n nerfstudio310 -y python=3.10
conda activate nerfstudio310
pip install --upgrade pip
pip install nerfstudio
```
---

## 1 Image acquisition

The images used for reconstruction were extracted from a video recording of the scene.

Approximately 200 frames were sampled from the video to serve as the input dataset for the reconstruction pipeline. Frame extraction was performed to ensure sufficient overlap between consecutive views while avoiding redundant information.

The extracted frames were organized in the following directory structure:

```bash
<location>
|---data
    |---my_object
        |---images
            |---<image 0>
            |---<image 1>
            |---...
```

All subsequent processing steps (Structure-from-Motion, dataset conversion, and training) use the images contained in this folder as input.

---

## 2 Data Processing

Run:

```bash
ns-process-data images --data path/to/frames --output-dir path/to/output
```

This script:

- Runs COLMAP internally
- Estimates camera intrinsics and extrinsics
- Normalizes the scene
- Generates `transforms.json`

The output directory contains:

```bash
<location>
|---data
    |---my_object
        |---images
            |---transforms.json
            |---colmap
```
This formatted dataset is required for training.

---

## 3 Neural Field Training

Run:

```bash
ns-train nerfacto --data path/to/output
```

During training:

- Rays are sampled from images
- Points are sampled along each ray
- A neural network predicts:
        - Density (σ)
        - RGB color (c)
- Volumetric rendering integrates these values
- The loss is computed between predicted and ground-truth pixels
- Parameters are optimized using Adam

Training outputs:

```bash
<location>
|---outputs
    |---scene_name
        |---nerfacto
            |---timestamp
                |---config.yml
                |---nerfstudio_models
                |---dataparser_transforms.json
```
---

## 4 Interactive Visualization

During training, a local web-based viewer is automatically initialized.

To monitor the optimization process in real time, open the URL displayed in the command prompt. The interface provides live visualization of camera poses, scene bounds, and intermediate renderings of the neural field.

---

### Alternatively, after training has finished

Run:

```bash
ns-viewer --load-config path/to/config.yml
