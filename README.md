# Reinforcement Learning for Automated Package Sorting

[![Python 3.8+](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![NumPy](https://img.shields.io/badge/NumPy-013243.svg?style=flat&logo=numpy&logoColor=white)](https://numpy.org/)
[![Matplotlib](https://img.shields.io/badge/Matplotlib-%23ffffff.svg?style=flat&logo=Matplotlib&logoColor=black)](https://matplotlib.org/)

This repository contains the implementation of a Reinforcement Learning (RL) agent for controlling an automated material handling system (AMS). The agent is trained using **Proximal Policy Optimization (PPO)** to sort packages of varying sizes to designated exit points on a 5x5 grid of actuated conveyor modules.

The project demonstrates the viability of modern RL techniques for solving complex industrial control problems characterized by high-dimensional action spaces and dynamic environmental interactions. A key component of this work is a **curriculum learning** strategy, which trains the agent on progressively harder tasks to achieve robust and efficient performance.

---

## 🚀 Showcase & Demos

### Center Sorting Performance
The fully trained agent successfully learns to sort packages and route them to the correct exit lane (middle of platform).

<!-- You should create a GIF of your simulation and replace the URL below -->

<p align="center">
  <img src="media/animation4.gif" alt="Sorting Demo GIF" width="500">
</p>

*<p align="center">Demonstration of the trained agent sorting packages succesfully.</p>*

### Accuracy of the Sorting Policy
The plot below shows the deviation of each box from its target exit position over 100 simulated runs. The agent consistently guides the vast majority of boxes to the center with minimal error, demonstrating an effective and reliable sorting policy.

<p align="center">
  <img src="media/errorCenter.png" alt="Final Sorting Deviation" width="500">
</p>

*<p align="center">Figure: Deviation of box x-positions from their target at the conveyor exit.</p>*

---

### Final Sorting Performance
The fully trained agent successfully learns to differentiate packages by size and route them to the correct exit lane (small packages to the right, large packages to the left).

<!-- You should create a GIF of your simulation and replace the URL below -->
<p align="center">
  <img src="media/sizeSortanimation(2).gif" alt="Sorting Demo GIF" width="500">
</p>

*<p align="center">Demonstration of the trained agent sorting packages of different sizes.</p>*

### Accuracy of the Sorting Policy
The plot below shows the deviation of each box from its target exit position over 100 simulated runs. The agent consistently guides the vast majority of boxes to the correct location with minimal error, demonstrating an effective and reliable sorting policy.

<p align="center">
  <img src="media/errorSize.png" alt="Final Sorting Deviation" width="500">
</p>

*<p align="center">Figure: Deviation of box x-positions from their target at the conveyor exit.</p>*

---

## 🔧 How It Works

### 1. The Environment: Automated Material System (AMS)
The simulation environment, defined in `RL_environment.m`, consists of:
-   A **5x5 grid** of independently controlled conveyor modules.
-   Each module's action is defined by a **rotation angle** and **actuation velocity**.
-   Packages of two different sizes (small and large) are generated in pairs at the entry of the grid.
-   A custom physics engine handles package movement and **collision detection/resolution**.

The sorting objective is to route packages based on their size:
-   **Small Packages** (`diameter < 0.225m`) should exit at a target lane on the right.
-   **Large Packages** (`diameter ≥ 0.225m`) should exit at a target lane on the left.

### 2. The RL Agent: Proximal Policy Optimization (PPO)
We use the PPO algorithm from the MATLAB Reinforcement Learning Toolbox.
-   **Actor-Critic Architecture:**
    -   The **Actor** network (`rlContinuousGaussianActor`) decides the next action (module rotation and velocity). It is a feedforward network with layers of size [32, 32, 16].
    -   The **Critic** network (`rlValueFunction`) estimates the value of the current state to stabilize training. It is also a feedforward network with layers [32, 32, 16].
-   **Observation Space:** The agent receives a 100-dimensional state vector describing the system, including module actions and the state (position, velocity, size) of up to 10 packages.
-   **Key Agent Options:** The PPO agent is configured with an `ExperienceHorizon` of 512, a `ClipFactor` of 0.1, and a `GAEFactor` of 0.95.

### 3. Reward Function Design
A carefully designed reward function (`getReward` method) was crucial for guiding the agent. The total reward is a combination of four components:

$$
R_{\text{total}} = R_{\text{alignment}} + R_{\text{exit}} - R_{\text{gap}} + R_{\text{forward}}
$$

Where:
-   **`R_alignment`**: Rewards the agent for moving packages into their correct target lane.
-   **`R_exit`**: Provides a large bonus for successfully exiting from the correct location.
-   **`R_gap`** (Penalty): Discourages packages from clustering too closely together to prevent collisions.
-   **`R_forward`**: Rewards the agent for maintaining throughput and continuous forward motion.

### 4. Curriculum Learning Strategy
To tackle the complexity of the task, the agent was trained using a curriculum learning approach, breaking the problem down into simpler, sequential stages:

1.  **Stage 1: Centered Sorting:** The agent first learns the basic mechanics of moving packages by training to guide all packages to a single exit point in the center. This establishes a baseline policy for stable system operation.
2.  **Stage 2: Sorting by Size:** Using the pre-trained model from Stage 1 as a starting point, the agent is then fine-tuned on the main task: sorting packages to different exits based on their size.
3.  **Refinement:** Throughout training, hyperparameters like learning rates and entropy loss are scheduled to transition the agent from an exploratory to an exploitative policy, refining its behavior for higher accuracy.

---

## 💻 How to Run

### Prerequisites
-   **MATLAB R2021b** or newer.
-   **Reinforcement Learning Toolbox™**.
-   **(Optional) Deep Learning Toolbox™**.
-   **(Optional) Parallel Computing Toolbox™** (if `UseParallel` is set to `true`).

### Instructions
1.  **Clone or download the repository** and place all the files in a single directory.
    ```bash
    git clone https://github.com/AydinLT00/RL-AMS-sorting.git
    ```
2.  **Open MATLAB**.
3.  **Navigate** to the directory where you saved the files using the MATLAB file browser or the `cd` command in the Command Window.
    ```matlab
    cd path/to/your/repository
    ```
4.  **Run the main training script** by typing its name in the Command Window and pressing Enter.
    ```matlab
    RL_main
    ```
    - To train a new agent, ensure the `istrained` flag at the top of `RL_main.m` is set to `false`.
    - To load and simulate a pre-trained agent, set `istrained` to `true` and ensure a `agent_trained.mat` file is in the directory.

---

## License
This project is licensed under a proprietary license. Please see the `LICENSE` file for details.

## 🙏 Acknowledgments
This project was completed for the "Integrating Bayesian Optimization, Reinforcement Learning & Language Models in Robotics" group project at **Politecnico di Milano**, under the supervision of Prof. Loris Roveda.
-   **Team:** Mohsen Ghasemi, Aidin Latifi, Shiyuan Liu
-   **Professor:** Loris Roveda
