<div align="center">

<h1>DexFIT: Force Informed Transfer for Dexterous Manipulation<br>using Kinematically Isomorphic Tactile Glove</h1>

**Anonymous Author(s)**

---

[![Paper](https://img.shields.io/badge/Paper-PDF-red?style=flat-square&logo=adobeacrobat)](https://anonymous.4open.science/r/DexFIT-478C/paper.pdf)
[![Code](https://img.shields.io/badge/Code-Anonymous-blue?style=flat-square&logo=github)](https://anonymous.4open.science/r/DexFIT-478C/)

</div>

---

<!-- ★ teaser 이미지 교체: static/images/teaser.png 를 레포에 올리면 자동으로 표시됨 -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/teaser.png"
       alt="DexFIT System Overview" width="90%">
  <br>
  <sub>The DexFIT system enables real-time contact force transfer via a kinematically isomorphic glove during data collection and achieves stable autonomous manipulation via Diffusion Policy + Adaptive Force Control (AFC).</sub>
</div>

---

## Abstract

Acquiring high-quality demonstration data for dexterous robotic hand manipulation remains challenging because conventional methods either lose critical force information during collection or require complex, error-prone retargeting processes.

We propose **DexFIT** (Dexterous Force-Informed Transfer), a system that transfers human contact forces to a robotic hand in real-time through a **kinematically isomorphic** wearable glove with integrated fingertip tactile sensors. The 16-DoF glove is mechanically identical to the target robot hand — no retargeting needed.

- **Force Generation (FG):** Converts fingertip tactile pressure into supplementary joint commands during data collection
- **Adaptive Force Control (AFC):** Monitors task progress via onboard tactile sensors and adjusts grasp force at deployment
- **Vision-Free Diffusion Policy:** Trained on joint positions, kinesthetic, and tactile signals only

> **Result:** 100% task completion rate · 34.5% faster · 16% less energy than position-only isomorphic control

---

## System Overview

<!-- ★ 전체 파이프라인 영상 → GIF로 변환해서 static/images/overview.gif 에 넣기 -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/overview.gif"
       alt="DexFIT overview" width="90%">
</div>

---

## Hardware Design

### DexFIT Glove & Multi-Sensory Robot Hand

<!-- ★ 하드웨어 사진: static/images/hardware.png -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/hardware.png"
       alt="Hardware" width="85%">
  <br>
  <sub>(a) 16-DoF Multi-Sensory Robotic Hand &nbsp;|&nbsp; (b) DexFIT Glove — kinematically isomorphic, 21 tactile taxels</sub>
</div>

<br>

<div align="center">

| | **DexFIT Glove** | **Robot Hand** |
|---|---|---|
| DoF | 16 | 16 |
| Joint sensor | Potentiometer (Alps RDC506018A) | Magnetic encoder × 16 |
| ADC | 12-bit MCP3208 (~0.08°) | — |
| Accuracy | R²=0.998, RMSE=2.59° | — |
| Tactile | 21 taxels (3×7 BMP384) | 60 taxels (15/finger), 250 Hz |
| Kinesthetic | — | 12 × 3-axis F/T, 300 Hz |
| Update rate | 90 Hz (SPI) | EtherCAT @ 1 kHz |
| MCU / Comm | ATmega2560 | STM32 |
| Weight | ~180 g | ~600 g |

</div>

---

### Fingertip Tactile Sensor

<!-- ★ 촉각센서 히트맵 GIF + 단면도 나란히 -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/tactile_live.gif"
       alt="Tactile live" width="44%">
  &nbsp;&nbsp;
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/tactile_detail.png"
       alt="Tactile structure" width="44%">
  <br>
  <sub>Left: Real-time 7-taxel pressure heatmap during contact &nbsp;|&nbsp; Right: BMP384 + Ecoflex-50 layer structure</sub>
</div>

<br>

<div align="center">

| Spec | Value |
|---|---|
| Sensing principle | Piezoresistive barometric (BMP384) |
| Taxels per finger | 7 (7 mm spacing), Flexible PCB |
| Cover material | Ecoflex-50, 3 mm |
| Response time | < 50 ms (47 ms measured) |
| Force range | 0–6.2 N |

</div>

---

### Joint Tracking

<!-- ★ 관절 추종 GIF: static/images/joint_tracking.gif -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/joint_tracking.gif"
       alt="Joint tracking" width="80%">
  <br>
  <sub>16-DoF isomorphic motion transfer — glove to robot hand, synchronized at 90 Hz</sub>
</div>

---

## Force Transfer Pipeline

<!-- ★ 파이프라인 다이어그램: static/images/pipeline.png -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/pipeline.png"
       alt="Pipeline" width="90%">
  <br>
  <sub>Left: Data acquisition with Force Generator &nbsp;|&nbsp; Right: Diffusion Policy + Adaptive Force Controller</sub>
</div>

<br>

**① Force Generation (FG) — during data collection**

$$\theta_{cmd} = \theta_{glove} + K_j \cdot \frac{T_f}{T_{scale}}$$

When the operator's fingertip contacts an object, the maximum taxel reading drives additional joint flexion on the robot. Optimal gain K=30 for DIP/PIP, K=15 for MCP. 3-finger avg RMSE = 0.63 N, Pearson r > 0.93.

**② Adaptive Force Control (AFC) — during deployment**

$$g_{AFC} = \max\!\left(0,\; \frac{\text{clip}(10-\omega,\,-10,\,10)}{10} \cdot G_{max}\right)$$

When rotation stalls, DIP joints receive additional flexion. Compensates for increasing socket friction beyond 300° of bulb rotation.

---

## Experiment: Light Bulb Rotation

<!-- ★ 실험 셋업 + 토크 그래프 나란히 -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/experiment_setup.png"
       alt="Experiment setup" width="44%">
  &nbsp;&nbsp;
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/torque_plot.png"
       alt="Torque profile" width="44%">
  <br>
  <sub>Left: Setup — RGB camera + 6-axis F/T sensor &nbsp;|&nbsp; Right: Z-axis torque rises as AFC activates past 300°</sub>
</div>

---

### Demo: DexFIT Autonomous Deployment (FG + AFC)

<!-- ★ 자율 배포 GIF: static/images/deploy.gif -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/deploy.gif"
       alt="DexFIT deployment" width="80%">
  <br>
  <sub>Autonomous light bulb rotation — 100% success rate (5/5 trials)</sub>
</div>

---

### Ablation: Data Collection Modes

<!-- ★ 3가지 텔레오퍼 비교 GIF 나란히 -->
<div align="center">
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/teleop_only.gif"
       alt="Teleoperation" width="29%">
  &nbsp;
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/contact_no_fg.gif"
       alt="Contact no FG" width="29%">
  &nbsp;
  <img src="https://anonymous.4open.science/r/DexFIT-478C/static/images/fg_teleop.gif"
       alt="FG teleoperation" width="29%">
  <br>
  <sub>Teleoperation (no contact) &nbsp;|&nbsp; Contact only (FG off) &nbsp;|&nbsp; <b>FG Teleoperation (Ours)</b></sub>
</div>

---

## Results

### Table I — Data Collection

| Mode | Contact | FG | Success/Try | Avg. Time (s) | Ang. Vel. (deg/s) | Exec. Rate (%) |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Teleoperation | ✗ | ✗ | 10/21 | 33.35 | 12.83 | — |
| Contact Teleoperation | ✓ | ✗ | 10/12 | 33.47 | 10.43 | 40.69 |
| **FG Teleoperation** | ✓ | ✓ | **10/13** | **26.24** | **15.07** | **48.53** |

### Table II — Policy Deployment Ablation

| Method | Contact | FG | AFC | Success (%) | Time (s) | Ang. Vel. (deg/s) | Energy (kJ) |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Teleoperation | ✗ | ✗ | ✗ | 0 | — | 1.186 | — |
| Teleoperation | ✗ | ✗ | ✓ | 0 | — | 3.881 | — |
| Contact (AFC off) | ✓ | ✗ | ✗ | 20 | 40.39 | 4.374 | 571.2 |
| Contact (AFC on) | ✓ | ✗ | ✓ | 100 | 41.86 | 6.603 | 576.3 |
| FG (AFC off) | ✓ | ✓ | ✗ | 100 | 31.48 | 7.366 | 543.9 |
| **DexFIT (Ours)** | ✓ | ✓ | ✓ | **100** | **26.45** | **9.107** | **479.9** |
