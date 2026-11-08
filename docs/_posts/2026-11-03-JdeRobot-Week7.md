---
title: "Internship Progress Week 7"
date: 2026-11-11
math: true
categories: [Internship 2026, Progress]
tags: [imitation-learning, ros2, nav2, dataset, training]
---

# Internship Progress Week 7

## Plan for this week

This week I will collect additional navigation demonstrations, including routes between the same points in reverse order and different approaches to intersections.

I will then train the neural network to predict five future poses over five seconds, each represented as `(x, y, cos(yaw), sin(yaw))`, and evaluate both position and orientation errors.

Finally, I will work on integrating the network into Nav2 as a local trajectory planner, with a conventional controller following its predictions. The aim is to evaluate its behaviour in simulation, focusing on goal completion, collisions and blocking situations.