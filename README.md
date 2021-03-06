[//]: # (Image References)

[image1]: https://user-images.githubusercontent.com/10624937/43851024-320ba930-9aff-11e8-8493-ee547c6af349.gif "Trained Agent"

[image2]: https://user-images.githubusercontent.com/10624937/43851646-d899bf20-9b00-11e8-858c-29b5c2c94ccc.png "Crawler"

# Project 2: Continuous Control

### Links:

- [Continuous_Control.py](./Continuous_Control.py)
- [Report.pdf](./docs/Report.pdf)
- Saved model weights
    - [Actor network weights](checkpoint-actor.pth)
    - [Critic network weights](checkpoint-critic.pth)
- [Installation Instructions](#installation-instructions)
- [My Implementation](#my-implementation)
- [Console logs](./docs/result.log)

### Introduction

For this project, you will work with
the [Reacher](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Learning-Environment-Examples.md#reacher)
environment.

![Trained Agent][image1]

In this environment, a double-jointed arm can move to target locations. A reward of +0.1 is provided for each step that
the agent's hand is in the goal location. Thus, the goal of your agent is to maintain its position at the target
location for as many time steps as possible.

The observation space consists of 33 variables corresponding to position, rotation, velocity, and angular velocities of
the arm. Each action is a vector with four numbers, corresponding to torque applicable to two joints. Every entry in the
action vector should be a number between -1 and 1.

### Distributed Training

For this project, we will provide you with two separate versions of the Unity environment:

- The first version contains a single agent.
- The second version contains 20 identical agents, each with its own copy of the environment.

The second version is useful for algorithms like [PPO](https://arxiv.org/pdf/1707.06347.pdf)
, [A3C](https://arxiv.org/pdf/1602.01783.pdf), and [D4PG](https://openreview.net/pdf?id=SyZipzbCb) that use multiple (
non-interacting, parallel) copies of the same agent to distribute the task of gathering experience.

### Solving the Environment

Note that your project submission need only solve one of the two versions of the environment.

#### Option 1: Solve the First Version

The task is episodic, and in order to solve the environment, your agent must get an average score of +30 over 100
consecutive episodes.

#### Option 2: Solve the Second Version

The barrier for solving the second version of the environment is slightly different, to take into account the presence
of many agents. In particular, your agents must get an average score of +30 (over 100 consecutive episodes, and over all
agents). Specifically,

- After each episode, we add up the rewards that each agent received (without discounting), to get a score for each
  agent. This yields 20 (potentially different) scores. We then take the average of these 20 scores.
- This yields an **average score** for each episode (where the average is over all 20 agents).

The environment is considered solved, when the average (over 100 episodes) of those average scores is at least +30.

### Installation Instructions

1. Download the environment from one of the links below. You need only select the environment that matches your
   operating system:

    - **_Version 1: One (1) Agent_**
        - Linux: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher_Linux.zip)
        - Mac OSX: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher.app.zip)
        - Windows (
          32-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher_Windows_x86.zip)
        - Windows (
          64-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/one_agent/Reacher_Windows_x86_64.zip)

    - **_Version 2: Twenty (20) Agents_**
        - Linux: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher_Linux.zip)
        - Mac OSX: [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher.app.zip)
        - Windows (
          32-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher_Windows_x86.zip)
        - Windows (
          64-bit): [click here](https://s3-us-west-1.amazonaws.com/udacity-drlnd/P2/Reacher/Reacher_Windows_x86_64.zip)


2. Place the file in the DRLND GitHub repository, in the `p2_continuous-control/` folder, and unzip (or decompress) the
   file.


3. Create (and activate) a new environment with Python 3.6.

    - __Linux__ or __Mac__:
   ```bash
   conda create --name drlnd python=3.6
   source activate drlnd
   ```
    - __Windows__:
   ```bash
   conda create --name drlnd python=3.6 
   activate drlnd
   ``` 
4. Install the dependencies:
   ```bash 
   pip install .
   pip install pandas
   ```
   If you get the error message that torch=0.4.0 could not be found, try the following
   ```bash
   conda install pytorch=0.4.0 -c pytorch
   ```

5. Run the `Continous_Control.py` file
   ```bash
   python Continous_Control.py
   ```

### My implementation

I have chosen the first Option, to train a single Agent using the Deep Deterministic Policy Gradient (DDPG) algorithm.
The resulting trained agent looks like the following:

![trained agent](./docs/img/trained-agent.gif)

The training loop for the DDPG Agent can be found in [Continuous_Control.py](Continuous_Control.py). All hyperparameters
can be defined by passing it into the constructor of the Agent class. The boolean `watch_only` can be used to either
train or watch the agent.

```python
_agent = Agent(_state_size, _action_size,
               gamma=0.99, lr_actor=0.0002, lr_critic=0.0003, tau=0.002, weight_decay=0.0001,
               buffer_size=1000000, batch_size=128)

# with this boolean you can decide if you just want to watch an agent or train the agent yourself
watch_only = True
if watch_only:
    watch_agent_from_pth_file(_env, _brain_name, _agent, './checkpoint-actor.pth', './checkpoint-critic.pth')
else:
    scores = train_agent(_env, _brain_name, _agent, n_episodes=500, max_steps=1500)
    watch_agent(_env, _brain_name, _agent)
    plot_scores(scores=scores, sma_window=10)
```

After training the agent the following score chart should be plotted.

![score chart](./docs/img/scores.png)

