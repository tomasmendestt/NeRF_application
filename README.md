# Neural Radiance Fields (NeRF) - Real-Time Scene Reconstruction

## Overview

This project implements a full pipeline for 3D scene reconstruction using Neural Rafiance Fields (NeRF).
The The reconstruction process starts from a video sequence. Frames are extracted and used to estimate camera poses via Structure-from-Motion. A neural radiance field is  then trained to learn a continuous volumetric representation of the scene, enabling novel viewe synthesis.

The implementation is based on:

Nerfstudio – https://github.com/nerfstudio-project/nerfstudio

---

## Project Pipeline

The complete workflow consists of:

1. ZED Acquisition (`importpyzed.py`) 
2. Frame Extraction (`extract_frames.py`)  
3. Dataset Processing (`ns-process-data`)  
4. Neural Field Training (`ns-train`)  
5. Interactive Visualization (`ns-viewer`)  

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

## 1. ZED Acquisition

The input data used for the reconstruction was acquired using a **ZED stereo camera**.  
To perform the acquisition step, the camera must first be connected to the computer and the appropriate Python environment must be activated.

After that, run the acquisition script:

```bash
python importpyzed.py
```

The script allows the user to select one of two acquisition modes:
- Video mode: records a video sequence (.avi) for a user-defined duration.
-  Frame mode: captures a user-defined number of frames directly.

If the video mode is selected, the recorded video must later be converted into individual frames before continuing the pipeline.
The purpose of this step was to capture a sequence of views with sufficient overlap and viewpoint variation to enable reliable 3D reconstruction.

During acquisition, the camera was moved smoothly around the target scene while maintaining stable motion and good visibility of the scene structure.

Two acquisition strategies were used depending on the target:

- **Object acquisition:** the camera was moved around the object to capture it from multiple viewpoints.
- **Scene acquisition:** the camera was carried through the environment while maintaining continuous motion and covering the visible geometry.

---

## 2. Frame Extraction

If the acquisition was performed in video mode, the recorded video must be converted into individual frames.
The video file must be saved inside the following directory structure:

data/crane/input_video.mp4

The frames should then be extracted inside the 'data/crane/' folder.

Run:

```bash
python extract_frames.py -i scene_name/data/crane/input_video.mp4 --fps 3
```

**Note: You should adjust fps in order to have arround 200-300 frames per video**
All images will then be saved inside 'scene_name/data/crane'

Recommended:
 - Images must be captured with camera motion (not rotating the object with a fixed camera)
 - Larger datasets increase processing time significantly
 - Save all images inside a folder and call it '/input'

If the frame mode was used during acquisition, this step is not necessary since the images are already captured individually.

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
        -- Density
        -- RGB color
- Volumetric rendering integrates these values
- The loss is computed between predicted and ground-truth pixels
- Parameters are optimized using Adam

Training outputs:

```bash
<location>
│
├──outputs
   └── scene_name
        └── nerfacto
            └── timestamp
                ├── config.yml
                ├── dataparser_transforms.json
                ├── nerfstudio_models
                    └──step-XXXXXXX.ckpt
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
```

---------------------------------------------------------------------------------------------------------------------------------------

## ZED Acquisition
The input data used for the reconstruction was acquired using a **ZED stereo camera**.  
To perform the acquisition step, the camera must first be connected to the computer and the appropriate Python environment must be activated.

After that, run the acquisition script:

```bash
python importpyzed.py
```

The purpose of this step was to capture a sequence of views with sufficient overlap and viewpoint variation to enable reliable 3D reconstruction.

During acquisition, the camera was moved smoothly around the target scene while maintaining stable motion and good visibility of the scene structure.

Two acquisition strategies were used depending on the target:

- **Object acquisition:** the camera was moved around the object to capture it from multiple viewpoints.
- **Scene acquisition:** the camera was carried through the environment while maintaining continuous motion and covering the visible geometry.

The recorded sequence was later used as the source input for the reconstruction.  
From this data, image frames were extracted and used for camera pose estimation and training.

### Acquisition Guidelines

To obtain good reconstruction quality, the following conditions were considered during capture:

- slow and continuous camera motion  
- strong overlap between consecutive frames  
- good lighting conditions  
- minimal motion blur  
- wide viewpoint coverage of the target  
- avoidance of reflective, transparent, or textureless surfaces

