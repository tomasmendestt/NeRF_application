# Neural Radiance Fields (NeRF) - Real-Time Scene Reconstruction

## Overview

This project implements a full pipeline for 3D scene reconstruction using Neural Rafiance Fields (NeRF).
The The reconstruction process starts from a video sequence. Frames are extracted and used to estimate camera poses via Structure-from-Motion. A neural radiance field is  then trained to learn a continuous volumetric representation of the scene, enabling novel viewe synthesis.

The implementation is based on:
Nerfstudio – https://github.com/nerfstudio-project/nerfstudio

