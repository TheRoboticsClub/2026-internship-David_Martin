---
title: "Internship Progress Week 3"
date: 2026-09-26
math: true
categories: [Internship 2026, Progress]
tags: [imitation-learning, ros2, gazebo, nav2, dataset]
---

## What I did this week

This week I moved the **UniBotics Global Navigation** environment to a local setup so I can work directly with ROS 2, without relying on HAL or WebGUI.

I created a clean environment with **ROS 2 Jazzy + Gazebo Harmonic** using Pixi, and imported the main elements from the UniBotics exercise:

- the **City Large** world,
- the `autonomous_car`,
- and the **LiDAR setup**.

The robot is now running locally in Gazebo and can be controlled directly from ROS 2.

![Global Navigation running locally](/assets/img/week3_global_navigation.png)

## Next steps

The next step is to launch **Nav2** in this environment and use it as the expert planner.

From there, the goal is to start recording the first dataset samples for the Imitation Learning pipeline:

```text
local map + local goal -> expert path
```