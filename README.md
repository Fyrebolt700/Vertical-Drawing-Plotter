# Vertical Wall-Climbing Drawing Plotter

A mechatronic system that converts colour images into drawn output on a vertical whiteboard using inverse kinematics, PID control, and real-time encoder feedback.

![Robot drawing on whiteboard](your-photo-here.jpg)

---

## What It Does

The robot hangs from a whiteboard using two DC motors and a system of cables. Given any colour image, it processes it into a plottable trajectory, computes the required motor angles using an inverse kinematics model, and draws the image on the whiteboard in real time. A micro-servo controls a marker pen lift mechanism for 2-bit colour drawing.

---

## System Overview

```
Colour Image (MATLAB)
       ↓
Grayscale → Binarize → Morphological filtering
       ↓
Pixel extraction → Segment merging → Scaling to board coordinates
       ↓
Inverse Kinematics Model → Motor shaft angles
       ↓
Arduino Nano 33 IoT (PID loop + encoder feedback)
       ↓
DC Motors + Servo → Robot moves and draws
```

---

## Hardware

| Component | Purpose |
|---|---|
| Arduino Nano 33 IoT | Real-time control, PID loop execution |
| 2× DC motors with encoders | Cable-driven XY movement across whiteboard |
| Micro-servo | Pen lift mechanism (marker up/down) |
| Marker pens (2 colours) | 2-bit colour drawing output |
| Whiteboard + mounting hardware | Drawing surface and robot suspension |

---

## Software

### MATLAB — Image Processing Pipeline
- Converts colour source image to grayscale and binarizes it
- Applies morphological dilation and erosion to clean up geometry
- Uses recursion to extract pixel paths and merge them into drawable segments
- Applies a scaling factor to convert pixel coordinates to physical whiteboard distances
- Outputs an ordered list of (x, y) board coordinates for the robot to follow

### MATLAB — Inverse Kinematics Model
- Takes target (x, y) board coordinates as input
- Uses trigonometry and linear mechanics to compute the required cable lengths
- Translates cable lengths into required DC motor shaft angles
- Accounts for motor torque limits: constrains movement to regions where motor load stays below 30% of stall torque
- Generates torque plots across the drawing surface to visualize safe operating zones

### Arduino — Real-Time Control (PID + Encoder Feedback)
- Reads encoder counts from both DC motors at runtime
- Computes positional error between current and target shaft angle
- Runs a PID control loop to drive motors toward target with minimal overshoot
- Coordinates servo state (pen up/pen down) with trajectory data from MATLAB

---

## Key Engineering Challenges

**Torque constraints:** Gravitational and tension forces on the motors vary significantly across the whiteboard surface. Free-body diagrams were used to model these forces, and motor datasheets were used to compute torque at each position. Robot movement was constrained to zones where motor load stays below 30% stall torque to prevent stalling or slipping.

**Image geometry:** Raw pixel extraction produces noisy, invalid paths. Morphological operations (dilation + erosion) were applied before extraction, and recursive segment merging was used to produce clean, motor-executable trajectories.

**PID tuning:** Encoder feedback was used to close the control loop and correct for positional drift during drawing. Gains were tuned empirically to balance tracking accuracy against motor oscillation.

---

## Results

The robot successfully reproduced recognizable images on a vertical whiteboard, demonstrating accurate path following and stable motor control across the drawing surface.

---

## Tech Stack

`Arduino` `MATLAB` `C++` `PID Control` `Inverse Kinematics` `Encoder Feedback` `DC Motors` `Servo Control` `Image Processing`

---

## Context

Built as part of the Mechatronics Engineering 35 course at STEM Innovation Academy, Calgary. Featured in the [STEMIA Journal](https://stemiajournal.ca/2024/11/02/integrated-stem-in-mechatronics-engineering-35-drawing-robots/).
