---
title: "Internship Progress Week 7"
date: 2026-11-10
math: true
categories: [Internship 2026, Progress]
tags: [imitation-learning, ros2, nav2, dataset, neural-network, evaluation]
---

# Internship Progress Week 7

This week, we expanded the demonstration dataset, added orientation to the network output, and implemented live trajectory prediction in ROS 2.

## Dataset expansion

We collected four additional successful episodes by navigating through previously visited locations in reverse order. After filtering idle periods, data gaps, and unsuitable motion, these episodes contributed **2,853 new samples**.

The combined dataset contains **7,333 samples from 14 episodes**: 5,908 for training, 735 for validation, and 690 for testing. Episodes remain separated between splits, and input normalization is calculated using only training data.

## Future pose prediction

The network now predicts **five future poses over a five-second horizon**, one at each second. Each pose contains `(x, y, cos(Δyaw), sin(Δyaw))`, expressed relative to the robot’s current pose.

Its inputs are LiDAR measurements, a local representation of the global plan, and the current velocities `(v, w)`, where `v` is the forward velocity.

We retrained the network and evaluated position errors using **ADE and FDE**, together with orientation error. We also inspected predicted trajectories against the recorded motion, including curves and cases with larger errors.

## Live visualization in ROS 2

We implemented an inference node that receives the navigation data, applies the training preprocessing, and executes the trained network at **5 Hz**.

The predicted trajectory and its five orientations are displayed in RViz alongside the Nav2 global plan. We recorded these predictions while **Nav2 continued driving the vehicle**; the neural network did not control its movement.

<video controls width="100%">
  <source src="{{ '/assets/vid/ShowPredictions_5PosestBASELINE_v1.mp4' | relative_url }}" type="video/mp4">
  Your browser does not support the video tag.
</video>


## Next steps

We will develop a script to analyse whether the dataset is balanced according to the robot’s heading relative to its navigation target. This will help identify whether demonstrations where the robot is already aligned dominate the dataset, and whether additional examples or balancing are needed.

We will also replace the ten global-plan input points with a single local subgoal, defined by the intersection of the global path with the local costmap boundary. When the final goal lies inside the local costmap, it will be used instead. The subgoal will be expressed in the robot’s reference frame.
