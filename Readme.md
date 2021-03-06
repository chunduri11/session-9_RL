**Step-1:**
1. This is the definition for ReplayBUffer. Here number samples will we information corresponding to each state in the state space of the environment, as the agent traverses each state the buffer is updated with the information of the state(state, next_state, action, reward, done).
2. Initially the buffer values are vacant, as the agent traverses in the environment we keep appending the (s,s',a,r,done) values to the buffer. Once the buffer is filled we use the same "add function" to replace a buffer location(using buffer index) with the latest updated state.
3. The buffer is sampled in batches of desired size. These batched are used to train the TD3 algorithm in off-policy  manner.
<p align="center">
  <img src="https://github.com/chunduri11/session-9_RL/blob/master/Images/step1.jpeg" width="350" >
</p>


**Step-2:**
1. Actor network, is a neural network. Used as policy approximater, which predicts possible action values for the given state(s).
2. In this example we are using all Fully connected layers, but depending on the state(s) we choose the network. If the state is an image we use CNN based model.
3. We use the same Actor network, as model and target. By create two different objects for model and target actors, using the same architecture.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step2.jpg" width="350" >
 </p>

**Step-3:**
1. Critic network, is a neural network. Used as optimal value approximater(max Q-value), for the given state and action(coming from the corresponding actor). The foward method builds two critics at once.
2. In this example we are using all Fully connected layers, but depending on the state(s) we choose the network. If the state is an image we use CNN based model.
3. We use the same critic twin network, as model and target. By create two different objects for model and target twin critic, using the same architecture.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step3.jpg" width="350" >
 </p>




**Step-4:**

1. Load samples from replay buffer in batches, for state, next_state,action, reward and done(flag to show status of episode)
2. Load these samples on to the device(cpu or gpu).

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step4.jpg" width="350" >
 </p>

**Step-5:**

1. Using next_state(s') calculate next action(a') with the actor target model as its forward pass.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step5.jpg" width="350" >
 </p>
 
**Step-6:**

1. Add noise to the next action(a') from a normal distribution.
2. Clip the a' values to main a range of values with in the normal distribution.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step6.jpg" width="350" >
 </p>
 
 **Step-7:**

1. Using both s' and a' with added noise calculate Q1t and Q2t values for both critic targets as its forward pass.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step7.jpg" width="350" >
 </p>
 
**Step-8:**

1. Of the two critic target values, take the minimum value.
2. We take the minimum value to avoid the over estimation of the Q-value and overcome the problem of variance.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step8.jpg" width="350" >
 </p>

**Step-9:**

1. Target Q-values(min of two critic targets), is discounted from next_state(s') to state(s) and added to current state-action reward.(Bellmen eq)
2. When the terminal state of the episode is reached, the next state does not exist. To show this we use flag 'done', with done being 1 if the state is the terminal state 0 otherwise. If we reached end of episode done=1, and right side of plus symbol becomes zero.
3. detatch in pytorch is used to remove the sected model or variable form the nn graph which is put on to the device.One detatch operation is used we will not be able to calculate the gradiants for that value or use nn operation as part of the neural network library.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step9.jpg" width="350" >
 </p>
 
**Step-10:**

1. Predict state(s) Q-values with critic-model using state(s) and corresponding action(a) values taken form replay buffer in forward pass.
<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step10.jpg" width="350" >
 </p>

**Step-11:**

1. calculate mse-loss using the optimal target Q-value as reference, with respect to corresponding Q-values(Q1,Q2) of critic-model.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step11.jpg" width="350" >
 </p>

**Step-12:**

1. Using the mse-loss from the Step-11, backpropagate to critic-models.
<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step12.jpg" width="350" >
 </p>


**Step-13:**

1. We use critic-model(Q1) to update the actor model, by applying graiant ascent on the Q-value approximation function.
2. Rather than doing gradinat ascent on Q-value, we are doing gradint descent on negative of Q-value.
3. For every two critic-model updtes, we update the actor-model once. This is to delay the actor-model update, to avoid instant updates to critics for stability.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step13.jpg" width="350" >
 </p>


**Step-14:**

1. Polyak avg for critic-target models, to get stable target Q-values.
2. For every two critic-target model updtes, we update the critic-target models once. This is to delay in the critic-target models update, to stabilize the target value.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step14.jpg" width="350" >
 </p>


**Step-15:***

1. Polyak avg for actor-target models, to get stable target Q-values.
2. For every two actor-target models updtes, we update the critic-target models once. This is to delay in the actor-target models models update, to stabilize the target value.

<p align="center">
  <img src="https://github.com/pasumarthi/EVA/blob/master/Phase2/images/Step15.jpg" width="350" >
 </p>
