# Hardware Platform

[中文](hardware_CN.md) | English

This document describes the main hardware components of the vision-follow-car system at a system level. It does not include detailed electrical specifications or datasheet-style parameter tables.

Chassis mechanics, vehicle control PCB, and electrical wiring are designed by Daliang. For purchasing information, see the BOM (Bill of Materials). *TODO: BOM to be updated by March 2026; link to be added.*

---

## Overview

The hardware platform consists of:

- A compute module for training data collection, vision, and inference
- An MCU board for low-level control
- A camera for visual input
- Mechanical chassis and actuators for steering and braking

---

## Compute: Jetson Orin Nano

The Jetson Orin Nano serves as the main compute unit. It is responsible for:

- Acquiring images from the CSI camera
- During data collection, capturing video frames and vehicle control data received from the MCU via UART, time-synchronized and saved together
- Running neural network inference in real time
- Producing control commands and sending them to the MCU via UART

It runs embedded Linux and hosts the `vision-follow-car-jetson` project.

---

## MCU Board: Daliang Development Board (STM32F103RCT6)

A development board developed by Daliang, based on STM32F103RCT6, with I/O circuitry and connection terminals. The board acts as the low-level controller. It:

- Receives control commands from Jetson over UART
- During data collection, sends vehicle control data to Jetson over UART
- Drives the steering mechanism
- Controls throttle
- Drives the brake actuator
- Handles mode switching (e.g., manual vs. auto-follow)

The `vision-follow-car-mcu` firmware runs on this board.

---

## Camera: CSI (IMX219)

A CSI-connected camera using the IMX219 sensor provides monocular visual input to the Jetson. The camera interface and image acquisition are handled by the Jetson project.

---

## Mechanical Structure and Actuators

- **Chassis**: Aluminum profile frame with 4-wheel suspension.
- **Rear drive**: Two brushless hub motors.
- **Steering mechanism**: DC servo motor controlling front-wheel steering angle.
- **Brake actuator**: Electric brake cylinder that pulls the brake cable; front wheels equipped with cable brake calipers.

Steering and brake actuator are driven by the STM32 based on commands received from Jetson.

---

## Connection Summary

- **Camera → Jetson**: CSI bus
- **Jetson ↔ MCU**: UART (serial) — Jetson sends control commands to MCU; during data collection, MCU sends vehicle data to Jetson

The training project runs on a separate server and does not participate in the onboard hardware connection.
