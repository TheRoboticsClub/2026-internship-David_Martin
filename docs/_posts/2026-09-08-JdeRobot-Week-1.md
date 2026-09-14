---
title: "Internship Progress Week 1"
date: 2026-09-08
categories: [Internship 2026, Progress]
tags: [imitation-learning, local-planning, ros2, nav2, unibotics]
---

# Week 1

This week I started defining the main idea of the project and learning the basic concepts needed for local navigation and local path planning.

## What I did

The main objective of the project is to learn a local planner using Imitation Learning:

$$
f_{\theta}(M_t, G_t) \rightarrow P_t
$$

where:

- **\(M_t\)**: local 2D map around the vehicle.
- **\(G_t\)**: local goal or subgoal.
- **\(P_t\)**: local path represented as a sequence of waypoints.

I first studied **DWA (Dynamic Window Approach)** and **DWB**, the Nav2 controller based on DWA.

DWA generates different candidate velocities, simulates their motion, discards unsafe alternatives and selects the best one.

After discussing the approach with my mentor, we clarified that DWA/DWB will not be the initial expert used to generate the dataset, because their main output is related to motion commands rather than the local path we want to learn.

Instead, the idea is to use a classical planner over a **local map and a local goal**:

$$
\Pi_{expert}(M_t, G_t) \rightarrow P_t^*
$$

The resulting path \(P_t^*\) will be used as the expert demonstration for the Imitation Learning dataset.

I also started working with the **UniBotics Obstacle Avoidance environment** and configured the Robotics Backend using Docker, w ROS2 Humble and Gazebo harmonic

During the first experiments I worked with:

```python
HAL.getPose3d()
HAL.getLaserData()
WebGUI.getNextTarget()

HAL.setV()
HAL.setW()
```

This allowed me to inspect the vehicle position, yaw orientation, LiDAR measurements, local goal and basic motion commands.
![Entorno UniBotics]({{ '/assets/img/week-1.png' | relative_url }})

## What I learned

This week I mainly learned:

- The difference between **local navigation** and **local path planning**.
- The basic operation of DWA and DWB.
- Why the expert for our dataset should generate a path \(P_t^*\).
- How pose, yaw and local goals are represented in UniBotics.
- How LiDAR provides distance measurements to nearby obstacles.
- The difference between global coordinates and coordinates relative to the vehicle.
- How linear velocity \(V\) and angular velocity \(W\) affect the vehicle motion.
- How the angular error between the vehicle orientation and the goal can be used to orient the vehicle.

## Next Steps

The next step is to study **classical path planning algorithms**, especially Dijkstra and A*.

The main objective will be to understand how to obtain:

$$
M_t + G_t \rightarrow P_t^*
$$

using a planner operating over a local map.

This will allow us to start defining how the expert demonstrations for the future Imitation Learning dataset will be generated.

## Main Question for Next Week

Which classical planner should be used as the expert to generate P* from the local map?