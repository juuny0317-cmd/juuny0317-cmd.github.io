---
permalink: /projects/
title: "Projects"
excerpt: "Autonomous driving, robotics, simulation, and embedded systems projects by YongJun."
author_profile: true
---

<p class="intro">Selected engineering projects in autonomous driving and robotics. Each entry emphasizes the system problem, my contribution, and the evidence available in the implementation.</p>

<div class="project-list project-list--index">
  <article class="project-entry project-entry--media">
    <div class="project-year">2026</div>
    <div class="project-body">
      <img class="project-thumbnail" src="{{ '/images/projects/kookmin2026/vehicle.jpg' | relative_url }}" alt="Team SVE Xycar on the competition track" loading="eager" />
      <div class="project-heading">
        <h2>Kookmin University Autonomous Driving Competition</h2>
        <span class="project-status project-status--complete">Completed</span>
      </div>
      <p>Designed and integrated a vision- and LiDAR-based ROS 2 autonomous-driving stack for a 1/10-scale Xycar. The system combined dual YOLO perception, OpenCV lane geometry, Stanley and Pure Pursuit control, mission arbitration, VESC safety handling, and a measured-data-based Gazebo environment.</p>
      <p class="project-result">Qualifying: 9th / 132 · Final: 7th / 132 teams</p>
      <p class="project-tech">ROS 2 · Xycar · YOLO · OpenCV · LiDAR · Stanley · Pure Pursuit · PID · Gazebo · Sim-to-Real</p>
      <p class="project-links"><a href="{{ '/projects/kookmin2026/' | relative_url }}">Read case study <span aria-hidden="true">→</span></a><span aria-hidden="true">·</span><a href="https://github.com/juuny0317-cmd/Kookmin2026">View repository <span aria-hidden="true">↗</span></a></p>
    </div>
  </article>

  <article class="project-entry">
    <div class="project-year">2026</div>
    <div class="project-body">
      <div class="project-heading"><h2>HL FMA 2026</h2><span class="project-status">In Progress</span></div>
      <p>Developing a safety-oriented ROS 2 driving stack for a 1/5-scale autonomous vehicle. Current work connects dual-antenna RTK GNSS, route tracking, mission management, speed and steering control, and an embedded vehicle interface.</p>
      <p class="project-tech">RTK GNSS · Path Planning · Vehicle Control · ROS 2 · NUCLEO-H743ZI2</p>
    </div>
  </article>

  <article class="project-entry">
    <div class="project-year">2026</div>
    <div class="project-body">
      <div class="project-heading"><h2>KAI 2026</h2><span class="project-status">In Progress</span></div>
      <p>Developing an autonomous-mobility system that connects ROS 2 and Gazebo simulation with perception, behavior decisions, path and speed planning, control, CAN software-in-the-loop, and STM32 hardware-in-the-loop.</p>
      <p class="project-tech">ROS 2 Humble · Gazebo Harmonic · Perception · Planning · Control · CAN · STM32</p>
    </div>
  </article>

  <article class="project-entry">
    <div class="project-year">2026</div>
    <div class="project-body">
      <div class="project-heading"><h2>CODRIVE</h2><span class="project-status">In Progress</span></div>
      <p>Building a distributed driver-assistance framework that connects Jetson-based perception, STM32 audio sensing, and Raspberry Pi sensor fusion and risk assessment through CycloneDDS.</p>
      <p class="project-tech">Jetson · STM32 · Raspberry Pi · CycloneDDS · Sensor Fusion · Edge AI</p>
    </div>
  </article>
</div>
