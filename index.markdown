---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Autonomous Robotic Construction
---

<!-- # Hello, World!

Welcome to my new Jekyll website. This is a custom homepage that I've created to demonstrate how to modify the default page.

## Features

- Simple and clean design
- Easy to customize
- Powered by Jekyll

## Recent Updates

I'm currently working on building this site. Check back soon for more content!

![Jekyll Logo](https://jekyllrb.com/img/logo-2x.png)

> "The best way to predict the future is to create it." - Abraham Lincoln

```ruby
def hello_world
  puts "Hello, Jekyll!"
end
```

[Learn more about Jekyll](https://jekyllrb.com/) -->

## Motivation and Project Context

This Master's project explores how reinforcement learning (RL) can enable robots to autonomously build stable structures, without the use of scaffolds or human intervention. It focuses on the design of reward functions that guide the learning process of robots as they place building blocks to form spanning structures connecting two fixed points. The broader vision is to contribute to the field of autonomous construction by leveraging the ability of RL to adapt and improve through interaction with its environment.

The construction problem is framed as a sequential decision-making task, where a robot places one block at a time following a policy it learns through trial and error. The environment simulates the physical constraints of the construction site and evaluates the resulting structure at each step. The robot learns to act through a Soft Actor-Critic (SAC) algorithm, a technique in reinforcement learning that encourages both goal-directed behavior and exploration by maximizing entropy.

<div style="max-width: 60%; margin: 0 auto 20px auto;">
  <img src="/images/structure.png" alt="Example of stable structure built by the agent" style="width: 100%; display: block;">
  <p style="text-align: center; font-style: italic; margin-top: 10px;">Example of stable structure built by the agent</p>
</div>

## Structural Stability and Reward Shaping

The stability of a structure is a critical aspect of this project. A mechanical model known as Rigid Block Equilibrium (RBE) is used to check whether a given configuration of blocks can stand on its own. This model assumes that the blocks are rigid and interact through compression, offering a fast way to determine if a structure is stable or not. This information is used not only to evaluate the end result but also to influence how the robot learns throughout the construction process.

A key challenge is to design a reward function that effectively teaches the robot what a "good" structure is. Traditional binary rewards—success or failure—are often too simplistic. They provide little feedback during the intermediate steps of construction and can lead the robot to miss important structural patterns. To address this, the project develops new reward strategies based on metrics that measure how stable a structure is, even before it is complete.

Two stability metrics were explored:
- One based on how much **vertical load** each block can support before the structure collapses
- Another based on how far a structure can be **tilted** before it becomes unstable

These metrics help quantify not just whether a structure works, but how robust it is. This richer information is then used to fine-tune the reward function given to the learning agent.

<div style="display: flex; justify-content: space-between; margin-bottom: 20px;">
  <div style="width: 48%;">
    <img src="/images/load_bearing_test.png" alt="Comparison of load-bearing capacity across multiple designs" style="width: 100%;">
    <p style="text-align: center; font-style: italic; margin-top: 5px;">Load bearing metric example result</p>
  </div>
  <div style="width: 48%;">
    <img src="/images/tilt_test.png" alt="Comparison of structures under tilting tests" style="width: 100%;">
    <p style="text-align: center; font-style: italic; margin-top: 5px;">Tilting metric example result</p>
  </div>
</div>

## Evaluation and Broader Impact

A database of over 1,000 different structures was generated using search algorithms, enabling comparison of the metrics across various design scenarios. The findings revealed that:


1. The two metrics are highly correlated
2. The load-bearing metric is computationally simpler and more practical to use
3. The load-bearing metric indicates where the weak points in a structure are located

<div style="max-width: 70%; margin: 0 auto 20px auto;">
  <img src="/images/success_animation_gap_3.gif" alt="Animation showing successful construction by the robot" style="width: 100%; display: block;">
  <p style="text-align: center; font-style: italic; margin-top: 10px;">Animation of robot successfully building a spanning structure</p>
</div>

By using this kind of reward shaping, the robot not only learns to build structures that remain standing, but also structures that are robust to failure. This improves learning efficiency and leads to better performance in the long run. Instead of rewarding only complete, successful structures, the agent now receives continuous feedback throughout the building process, making it easier to explore new designs and avoid structural weaknesses.

<div style="max-width: 80%; margin: 0 auto 20px auto;">
  <img src="/images/scatterplot.png" alt="Correlation between tilt metric and load bearing capacity" style="width: 100%; display: block;">
  <p style="text-align: center; font-style: italic; margin-top: 10px;">Comparison between tilt and load bearing</p>
</div>

## Learning Integration and Experimental Evaluation

After validating that the stability metrics correlate well and provide meaningful information, these metrics were directly integrated into the reinforcement learning pipeline. They were used to shape the reward signals that guided the agent during training. Instead of using a binary notion of success and failure, the agent was rewarded proportionally based on the robustness of each intermediate structure it built.

Experiments were conducted to compare the behavior and performance of agents trained with the shaped rewards against those trained with a simple baseline. This comparison demonstrated that the use of structural stability metrics in the reward design led to more resilient constructions. This approach enabled to develop a more refined understanding of what makes a structure stable, improving the performance throughout the construction process. 
### Types of Reward Implemented
The image below shows the tilt and load bearing achieved by agents trained with different types of reward functions. These reward types are:
- binary: The agent receives a reward of 1 if the final structure is stable, and -1 if it is not. No reward is given during construction steps.
- stability: Similar to binary, but if the final structure is stable, the reward is proportional to the stability of the structure (not just 1 or -1).
- pos: During construction, the agent receives small positive rewards for steps where the load bearing is high.
- neg: During construction, the agent receives small negative rewards for steps where the load bearing is low.
- posneg: The agent receives both small positive rewards for high load bearing steps and small negative rewards for low load bearing steps during construction.

The reward types can be combined, for example:
- binary_pos: Binary reward at the end, plus small positive rewards during construction.
- stability_neg: Stability-based reward at the end, plus small negative rewards during construction.
- stability_posneg: Stability-based reward at the end, plus both small positive and negative rewards during construction.

Summary:
- "binary" and "stability" are for the final structure only.
- "_pos", "_neg", and "_posneg" add extra feedback during the building process.
- Combining these strategies helps the agent learn to build more stable and robust structures.

This helps to compare how different reward strategies affect the quality of the structures built by the agent. In this next image, we present some of the results of the stability quality using the metrics developed, for each type of reward used. 

<div style="max-width: 100%; margin: 0 auto 20px auto;">
  <img src="/images/comparison_of_success_rates.png" alt="Comparison of success rates between different reward strategies" style="width: 100%; display: block;">
  <p style="text-align: center; font-style: italic; margin-top: 10px;">Comparison of load bearing capacity and tilting capacity between different reward strategies</p>
</div>

Each reward shapping method gave some imporvement on the results when comparing with the baseline binary type.




<div class="final-quote" style="background-color: #f8f9fa; border-left: 4px solid #4a7bab; padding: 20px; margin: 30px 0; border-radius: 4px; font-size: 1.1em; color: #333;">
  <p style="margin: 0; font-style: italic;">
    This project is part of a broader effort to understand how machine learning and robotics can merge with architectural and structural design to produce autonomous systems capable of creative and efficient construction.
  </p>
</div>