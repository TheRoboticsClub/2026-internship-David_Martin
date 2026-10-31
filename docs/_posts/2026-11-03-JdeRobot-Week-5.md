---
title: "Internship Progress Week 6"
date: 2026-11-03
math: true
categories: [Internship 2026, Progress]
tags: [imitation-learning, ros2, nav2, dataset, validation, teleoperation]
---

# Internship Progress Week 6

## Next steps

The next step is to update the expert navigation setup and generate a completely new dataset.

The previous configuration allowed lateral movement. We will modify the Nav2 parameters to keep forward motion and rotation, setting the lateral velocity (`vy`) to zero. We will then test the navigation to check that the robot can reach its goals and avoid obstacles with this restriction.

Once the configuration is working, we will collect new demonstrations and rebuild the training, validation, and test sets. The neural network will still predict a local path as a sequence of relative waypoints.

Finally, we will retrain the model using the new dataset and evaluate its predictions using ADE and FDE.