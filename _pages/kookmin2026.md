---
permalink: /projects/kookmin2026/
title: "Kookmin University Autonomous Driving Competition 2026"
excerpt: "Case study of a ROS 2 camera/LiDAR autonomous-driving system that finished 7th among 132 teams."
author_profile: true
---

<p class="case-kicker">Autonomous Driving · Team SVE · Completed</p>

<p class="intro">A 1/10-scale Xycar autonomous-driving system integrating camera and LiDAR perception, mission-level decision making, path tracking, actuator safety, and measured-data-based simulation. The project advanced from 132 teams to the 22-team main round and finished 7th overall.</p>

<figure class="case-hero">
  <img src="{{ '/images/projects/kookmin2026/vehicle.jpg' | relative_url }}" alt="Team SVE's Xycar driving on the main-round competition track" />
  <figcaption>Team SVE’s 1/10-scale Xycar during the main-round competition run.</figcaption>
</figure>

<nav class="case-nav" aria-label="Case study contents">
  <a href="#overview">Overview</a>
  <a href="#competition-result">Result</a>
  <a href="#my-contribution">Contribution</a>
  <a href="#system-architecture">Architecture</a>
  <a href="#qualifying-round">Qualifying</a>
  <a href="#main-round">Main Round</a>
  <a href="#simulation--sim-to-real">Sim-to-Real</a>
  <a href="#validation--experiments">Validation</a>
  <a href="#problems--solutions">Problems &amp; Solutions</a>
  <a href="#lessons-learned">Lessons</a>
  <a href="#repository">Repository</a>
</nav>

## Overview

The project was developed for the 9th Kookmin University Autonomous Driving Competition. The main-round vehicle used a fisheye camera, 2D LiDAR, and VESC actuator interface on Ubuntu 22.04 with ROS 2 Humble. The engineering goal was not a single algorithm demo, but an end-to-end stack that could coordinate lane driving, traffic lights, dynamic and static obstacles, a shortcut left turn, and a cone mission under real vehicle constraints.

<dl class="fact-list">
  <div><dt>Platform</dt><dd>1/10-scale Xycar</dd></div>
  <div><dt>Runtime</dt><dd>Ubuntu 22.04 · ROS 2 Humble · Fast DDS</dd></div>
  <div><dt>Sensing</dt><dd>Fisheye camera · 2D LiDAR</dd></div>
  <div><dt>Control</dt><dd>Stanley · Pure Pursuit · PID · PI motor control</dd></div>
</dl>

## Competition Result

<div class="result-strip" aria-label="Competition results">
  <div><span>Qualifying</span><strong>9th / 132</strong><small>3 laps · 151.32 s</small></div>
  <div><span>Main-round field</span><strong>22 teams</strong><small>Advanced from qualifying</small></div>
  <div><span>Final result</span><strong>7th / 132</strong><small>149.65 s total</small></div>
</div>

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/final-ranking.png' | relative_url }}" alt="Final competition ranking showing Team SVE from Konkuk University in seventh place with a time of 149.65 seconds" loading="lazy" />
  <figcaption>Final ranking screen: Team SVE placed 7th with a total time of 149.65 seconds.</figcaption>
</figure>

## My Contribution

This was a team project. My contribution focused on the following areas and on integrating and tuning them as a working system; it does not imply sole authorship of the entire stack.

- **Camera perception:** separated lane and scene YOLO pipelines and tuned confidence, input resolution, and execution rates.
- **Lane geometry and control:** developed YOLO + OpenCV lane geometry and a scheduled Stanley controller.
- **Mission integration:** coordinated `STOP`, `PAUSED`, `LANE`, `OVERTAKE`, and `CONE` states with freshness checks and VESC fail-safe behavior.
- **Obstacle perception:** connected dynamic/static YOLO detections with LiDAR association and mission parameters.
- **Shortcut left turn:** integrated traffic-light recognition with entry, turn, and exit state transitions.
- **Sim-to-Real:** measured vehicle geometry, steering, and speed, then calibrated direction-specific lookup tables and the Gazebo interface.

## System Architecture

The stack kept perception, control, and actuator safety behind explicit ROS 2 interfaces. Camera work was split by task so lane tracking could use a smaller, faster input while scene understanding retained more detail.

<ol class="architecture-flow" aria-label="End-to-end system architecture">
  <li><strong>Sensors</strong><span>Camera · LiDAR</span></li>
  <li><strong>Routing &amp; Fusion</strong><span>Latest frame · DBSCAN</span></li>
  <li><strong>Perception</strong><span>Dual YOLO · OpenCV</span></li>
  <li><strong>Planning &amp; Control</strong><span>Stanley · Pure Pursuit</span></li>
  <li><strong>Mission Manager</strong><span>State and safety arbitration</span></li>
  <li><strong>Vehicle</strong><span>VESC adapter · watchdog</span></li>
</ol>

## Qualifying Round

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/qualifying-simulator.jpg' | relative_url }}" alt="Official qualifying-round simulator and ROS 2 terminal output" loading="lazy" />
  <figcaption>Qualifying-round simulator used for the three-lap autonomous-driving evaluation.</figcaption>
</figure>

### Perception

The simulator pipeline applied a camera region of interest, bird’s-eye-view transform, HSV masks for white and yellow lanes, and sliding-window tracking. The resulting lane points were converted into a target path and visualized in RViz during debugging.

### Decision

A rule-based finite-state machine managed cone-to-lane transition, police-car and traffic-light ordering, shortcut left turn, lap counting, and finish detection. State and transition conditions prevented repeated finish-line detections from being counted as separate laps.

### Control

Early Pure Pursuit experiments produced oscillation on straight segments. The final qualifying controller used PID lane control, while path stabilization, mission transitions, left-turn recovery, and speed policy were tuned together.

### Problems &amp; Solutions

The total three-lap time fell from 276.00 seconds to 151.32 seconds. This improvement is attributed to the complete pipeline iteration—not to the controller change alone. The public repository preserves the run evidence and reconstructed design record, but not the original qualifying source files.

## Main Round

### Perception

The real-vehicle stack separated a lane branch (320 px, up to 15 Hz) from a scene branch (640 px, up to 10 Hz). Within YOLO regions, adaptive thresholding, Canny edges, Hough lines, and polynomial fitting recovered lane geometry. LiDAR clusters were associated with image detections for obstacle and cone handling.

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/perception.jpg' | relative_url }}" alt="Recorded vehicle-camera frame with lane and scene detections highlighted" loading="lazy" />
  <figcaption>Recorded frame from the final lane and scene perception checkpoints.</figcaption>
</figure>

### Decision

Separate logic handled dynamic obstacles, static obstacles, and shortcut entry. Detection outputs were converted into target-lane, speed, and event parameters instead of directly commanding the actuator.

### Control

Lane driving used Stanley control with gains scheduled by speed and path context. The cone mission used a spline-derived center path and Pure Pursuit. Curvature severity and S-reversal evidence reduced speed before difficult transitions.

### Mission Manager

The Mission Manager arbitrated `STOP`, `PAUSED`, `LANE`, `OVERTAKE`, and `CONE` modes. Its priority rules ensured pause and stop conditions overrode ordinary lane and obstacle commands.

### Safety

Camera timestamps were preserved through the control contract. Stale states produced a zero-speed command, and the actuator adapter applied conversion, rate limiting, feed-forward with PI correction, and a 0.30-second watchdog.

## Simulation & Sim-to-Real

Vehicle dimensions and response were measured rather than guessed: a 0.355 m wheelbase, 0.250/0.266 m track, 0.050 m wheel radius, and 4.1 kg mass were reflected in the model. Five-meter timing created a speed lookup table, while measured left/right steering asymmetry motivated direction-specific curvature tables.

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/gazebo-course.jpg' | relative_url }}" alt="Kookmin competition course reconstructed in Gazebo" loading="lazy" />
  <figcaption>Measured-data-based competition course and vehicle environment in Gazebo.</figcaption>
</figure>

The result is a practical calibration framework, not a complete digital twin. Tire behavior, actuator delay, camera pose, inference latency, and the visual domain gap remain important limitations.

## Validation & Experiments

Validation combined raw calibration measurements, recorded-perception replay, and simulator checks.

- At a 5 m test distance, command 4 measured 0.399 m/s and command 25 measured 2.222 m/s.
- At steering magnitude 40, the measured left radius was 48.4% larger than the right radius, supporting separate direction tables.
- Gazebo max-steer radius replay showed 0.9% right and 1.9% left absolute error at the calibrated ±40 knots.
- In two S-curve replays, unsafe 10→16 acceleration events changed from 7→0 and 3→0 after the speed-policy update.

<figure class="evidence-figure evidence-figure--wide">
  <img src="{{ '/images/projects/kookmin2026/s-curve-validation.png' | relative_url }}" alt="S-curve replay plots comparing detected lane-center error and commanded speed" loading="lazy" />
  <figcaption>Recorded-perception replay used to inspect S-curve tracking and speed-command behavior. Image-plane error is not vehicle-pose error.</figcaption>
</figure>

## Problems & Solutions

<div class="iteration-list">
  <section class="iteration">
    <h3>Camera backlog and stale control</h3>
    <dl>
      <div><dt>Problem</dt><dd>Control could react to an older scene when camera work accumulated in a FIFO queue.</dd></div>
      <div><dt>Why</dt><dd>The pipeline lacked a latest-frame policy and the command contract did not carry freshness information end to end.</dd></div>
      <div><dt>Approach</dt><dd>Replace pending work with the newest frame, preserve timestamps, and stop on stale state.</dd></div>
      <div><dt>Result</dt><dd>Backlog accumulation was structurally prevented and a verifiable 0.30-second stale-state stop was added. No unsupported latency-improvement percentage is claimed.</dd></div>
    </dl>
  </section>

  <section class="iteration">
    <h3>One detector for two different tasks</h3>
    <dl>
      <div><dt>Problem</dt><dd>Lane localization favored smaller, faster inputs while scene objects required more image detail.</dd></div>
      <div><dt>Why</dt><dd>A single multi-class configuration forced both tasks into the same resolution and cadence.</dd></div>
      <div><dt>Approach</dt><dd>Split lane and scene inference, then route only the most recent frame to each worker.</dd></div>
      <div><dt>Result</dt><dd>The final operating configuration was lane 320 px / 15 Hz maximum and scene 640 px / 10 Hz maximum. A before/after FPS claim is intentionally omitted because an equivalent benchmark was not recorded.</dd></div>
    </dl>
  </section>

  <section class="iteration">
    <h3>Unseen red source at the competition site</h3>
    <dl>
      <div><dt>Problem</dt><dd>A broadcast-camera red indicator was misclassified as a traffic light and held the vehicle in STOP.</dd></div>
      <div><dt>Why</dt><dd>A low confidence threshold helped distant-light recall but lacked sufficient location, geometry, and temporal context for an unseen red object.</dd></div>
      <div><dt>Approach</dt><dd>Raise the final threshold, require repeated confirmation, and identify ROI and context gating as the next validation target.</dd></div>
      <div><dt>Result</dt><dd>The failure is documented rather than presented as fully solved. It showed that a safe state also needs a tested recovery policy.</dd></div>
    </dl>
  </section>
</div>

## Lessons Learned

- System performance depends on the contracts between perception, decision, control, and hardware—not only on the headline algorithm.
- Measurements and replay are most useful when their limits are explicit; image-plane error should not be reported as physical tracking error.
- Sim-to-real work benefits from calibrating directional asymmetry and actuator response before tuning controllers in simulation.
- Fail-safe logic must be validated together with recovery behavior, especially when perception encounters an unseen environment.

## Repository

The repository contains the detailed architecture, algorithms, calibration data, validation manifest, competition retrospective, simulator environment, and ROS 2 workspace.

<p class="repository-links"><a href="https://github.com/juuny0317-cmd/Kookmin2026">Open Kookmin2026 on GitHub <span aria-hidden="true">↗</span></a></p>

Key implementation references: [camera perception](https://github.com/juuny0317-cmd/Kookmin2026/tree/main/ros2_ws/src/cam), [mission manager](https://github.com/juuny0317-cmd/Kookmin2026/blob/main/ros2_ws/src/mission_cone_drive/mission_cone_drive/mission_manager_node.py), [simulation](https://github.com/juuny0317-cmd/Kookmin2026/tree/main/simulation/xycar_gz_sim), and [validation evidence](https://github.com/juuny0317-cmd/Kookmin2026/blob/main/docs/VALIDATION_EVIDENCE.md).
