# Dataset Overview

[中文](dataset_CN.md) | English

This document describes the role of the dataset in the vision-follow-car project and how it is organized.

---

## Role of the Dataset

The dataset is the foundation for training the visual follow model. It consists of:

- **Images** — Captured from the onboard CSI camera during human driving
- **Control signals** — Steering, throttle, brake values recorded at the same time

Each image is paired with the corresponding control signals. The model learns to predict control outputs from visual input by imitating this paired data.

---

## Data Source: Human Driving

Data is collected by **human driving**. The operator controls the vehicle manually (e.g., via remote control or a wired interface). While driving, the MCU sends vehicle control data to the Jetson compute module via UART. The compute module synchronously records:

- Camera frames
- Control values received from the MCU at each frame

This yields a set of (image, control) pairs that represent the desired driving behavior.

---

## Training Samples

Training samples are formed by pairing:

- One or more image frames (potentially multi-frame for temporal context)
- The corresponding control target (steering, throttle, brake)

The exact pairing rules and field names are defined in the training repository (`vision-follow-car-training`). This master repository does not define specific data formats or schema details.

---

## Where the Dataset Lives

**Dataset address**: [Hugging Face — DaliangAuto/vision-follow-car](https://huggingface.co/datasets/DaliangAuto/vision-follow-car)

The master repository `vision-follow-car` does **not** store the full dataset. Reasons:

- Dataset size is typically large and not suitable for version control
- Datasets are often stored on external storage or cloud services
- Each training run may use different dataset versions

This repository provides:

- High-level description of the dataset’s role
- Links or references to where dataset structure and access are documented
- Guidance that the training repository defines the actual schema and loading logic

---

## Data Flow in the Pipeline

1. **Collection** — Images and controls are recorded (e.g., during data collection runs).
2. **Storage** — Raw data is stored outside this repository (local disk, NAS, cloud).
3. **Cleaning** — The training project loads raw data, filters invalid samples, and prepares the training set.
4. **Training** — The cleaned dataset is used to train the model.

For specific data formats, paths, and field definitions, refer to the documentation and code in `vision-follow-car-training`.
