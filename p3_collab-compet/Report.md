# Project 3 Report

(This project is trained in udacity's workspace)

For this project, I will work with the [Tennis](https://github.com/Unity-Technologies/ml-agents/blob/master/docs/Learning-Environment-Examples.md#tennis) environment.

In this environment, two agents control rackets to bounce a ball over a net. If an agent hits the ball over the net, it receives a reward of +0.1.  If an agent lets a ball hit the ground or hits the ball out of bounds, it receives a reward of -0.01.  Thus, the goal of each agent is to keep the ball in play.

The observation space consists of 8 variables corresponding to the position and velocity of the ball and racket. Each agent receives its own, local observation.  Two continuous actions are available, corresponding to movement toward (or away from) the net, and jumping. 

The task is episodic, and in order to solve the environment, the agents must get an average score of +0.5 (over 100 consecutive episodes, after taking the maximum over both agents). Specifically,

- After each episode, we add up the rewards that each agent received (without discounting), to get a score for each agent. This yields 2 (potentially different) scores. We then take the maximum of these 2 scores.
- This yields a single **score** for each episode.

The environment is considered solved, when the average (over 100 episodes) of those **scores** is at least +0.5.

### Learning Algorithm

The algorithim I chose for this project is the Multiple Agent Deep Deterministic Policy Gradient (MADDPG) algorithm: 

<img src="pics/maddpg.png" alt="maddpg" style="zoom:67%;" />

(This is a screen shot from [the original paper](https://arxiv.org/pdf/1706.02275.pdf), where you can find more details.)

The python implementation is based on Udacity's [ddpg-pendulum](https://github.com/udacity/deep-reinforcement-learning/tree/master/ddpg-pendulum) project. To handle this multiple agent game, I introduce a `Group` class to combine agents together and share the same memory buffer; however, each agent has their own actor and ctiric. 

#### Neural Network Architecture 

The actor network has two hidden layers, where the 1st layer has 256 nodes and the 2n layer has 256 nodes. The critic network has a very sililar structure as the actor nework. The activation functions are all ReLus. The networks structure used here are the same as in [my previous project](https://github.com/luzk-emory/Udacity-Deep-Reinforcement-Learning/tree/master/p2_continuous-control); only the numbers of nodes are different.

#### Hyperparameters 

The key hyperparameters in mine benchmark implementation are as follows:

| Hyperparameter          | Value |
| ----------------------- | ----- |
| Replay buffer size      | 1e5   |
| Batch size              | 128   |
| GAMMA (discount factor) | 0.99  |
| TAU                     | 1e-2  |
| Actor learning rate     | 1e-4  |
| Critic learning rate    | 1e-3  |
| L2 weight decay         | 0     |
| Updating frequency      | 10    |
| Updating times          | 10    |
| OUNoise sigma           | 0.2   |
| Noise decay      | 1 (no decay) |

Most of the parameters are broadly in align with [my previous project](https://github.com/luzk-emory/Udacity-Deep-Reinforcement-Learning/tree/master/p2_continuous-control). Though, the original setting couldn't solve the task. I slightly adjusted some learning rates so that we can solve the game within a reasonable number of episodes. 

### Plot of Rewards

The rewards during the training process are plotted below:

![rewards](pics/rewards.png)

The task is solved at episode 1145. I further trained the agents for 100 more episodes, and the average reward continued to increase.

### Ideas for Future Work

1. One straiforward way to go is to try increasing the number of neural network layers and the number of nodes in each layer, or fine-tune other hyper parameters. However, this process could be quite time-consuming. 
2. My current implementation is a minimum departure from singe agent DDPG, where the agents only share the memory buffer. Maybe one can try other variants of MADDPG where the agents share 
   - actor networks
   - critic networks
   - both actor networks and critic networks
3. Use Prioritized Experience Replay
