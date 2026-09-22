---
title: "Internship Progress Week 2"
date: 2026-09-15
math: true
categories: [Internship 2026, Progress]
tags: [imitation-learning, local-planning, ros2, nav2, behavioral-cloning]
---

# Week 2

This week I focused on understanding how previous works use **Imitation Learning for local robot navigation** and on defining more clearly the approach we will follow in our project.

The main objective remains:

$$
f_{\theta}(M_t, G_t) \rightarrow P_t
$$

where:

- **$$M_t$$**: local map around the robot.
- **$$G_t$$**: local goal.
- **$$P_t$$**: local path represented as a sequence of waypoints.

## What I did

I first reviewed classical path planning algorithms such as **Dijkstra, A\*** and **NavFn**.

This helped clarify that our expert should generate a path:

$$
\Pi_{expert}(M_t,G_t) \rightarrow P_t^*
$$

which can later be stored as the target of the Imitation Learning dataset.

I also studied two papers related to our project.

### Frontiers Paper

**Improving Autonomous Robotic Navigation Using Imitation Learning**

[Paper link](https://www.frontiersin.org/journals/robotics-and-ai/articles/10.3389/frobt.2021.627730/full)

This work inserts an Imitation Learning module between the global planner and the local planner:

$$
Global\ Planner
\rightarrow
Imitation\ Learner
\rightarrow
Local\ Goal
\rightarrow
Local\ Planner
$$

The neural network receives LiDAR, visual information and information about the goal.

Instead of predicting velocities, it selects one of **9 predefined local waypoints**.

The model is first trained using demonstrations from the classical ROS navigation stack and later fine-tuned using demonstrations from a human tele-operating the robot.

The main idea is to combine learning with an existing classical navigation stack instead of replacing the whole system.

### Stanford Paper

**Deep Local Trajectory Replanning and Control for Robot Navigation**

[Paper link](https://iliad.stanford.edu/pdfs/publications/pokle2019deep.pdf)

This work is even closer to our project.

Its architecture is:

$$
Global\ Planner
\rightarrow
NN\ Local\ Planner
\rightarrow
NN\ Velocity\ Controller
$$

The first neural network receives:

- Global plan.
- LiDAR.
- Odometry.
- Trajectories of nearby pedestrians.

and generates a **local trajectory composed of 5 future poses**.

The second neural network converts this local trajectory into linear and angular velocities.

The first network is very similar to what we want to build.

Our simplified approach will be:

$$
LocalMap + LocalGoal
\rightarrow
NN
\rightarrow
LocalPath
$$

We will not initially learn the velocity controller. A classical controller will be responsible for following the generated path.

## Dataset Idea

Each training sample should contain:

$$
(M_t,G_t,P_t^*)
$$

where the classical planner generates the expert path $$P_t^*$$.

The output will be represented using a fixed number of waypoints:

$$
P_t^* =
[(x_1,y_1),...,(x_N,y_N)]
$$

Unlike the Stanford paper, which uses poses sampled in time, our first approach will focus only on the **geometry of the path**, sampling points spatially along the expert trajectory.

## What I learned

This week I mainly learned:

- The difference between learning a local goal, a local path and direct velocity commands.
- How Behavioral Cloning can use demonstrations from classical navigation systems and humans.
- Why keeping planning and control separated simplifies the problem.
- How the Stanford Local Planner is very similar to the neural network we want to build.
- How expert paths can be converted into supervised training samples.

## Next Steps

Next week we will start the practical part of the project.

The main idea is to reproduce a **simplified version of the Stanford Local Planner**:

$$
LocalMap + LocalGoal
\rightarrow
NN
\rightarrow
LocalPath
$$

Before training the neural network, the first objective will be to build the dataset generation pipeline:

$$
LocalMap + LocalGoal
\rightarrow
Classical\ Expert
\rightarrow
P_t^*
$$

We will need to decide:

- How to obtain the local map.
- How to define the local goal.
- Which Nav2 planner will be used as the expert.
- How many waypoints will represent the expert path.
- Which robot and simulated environment will be used.

The first practical milestone will be to generate and visualize valid samples of:

$$
(M_t,G_t,P_t^*)
$$