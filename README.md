# **Car Control Project: MPC Control**
#### _Lorenzo's version_
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

[results]: ./simulator.png
[model]: ./model.png

![alt text][results]
---
### Project Introduction
In this project, we drive a car around a track (with Udacity's car simulator) using MPC (Model Predictive Control).
The implementation is in C++ and the parameters have been tuned manually. We set a reference speed to 100 mph, which works well even with a 100ms (simulated) latency.

### Model
The global kinematic model is a simple model that approximates the vehicule trajectory using 4 states variables
- `x` : position of the car on the x axis
- `y` : position of the car on the y axis
- `ψ` : orientation of the car relatively to the x axis
- `v` : car velocity

The model also has 2 actuators:
- `δ` : steering
- `a` : acceleration (or brake if negative)

Computing the next state of the model, given the current state and the actuators is given by the below:
![alt text][model]


### Model Predictive Control
In order to drive the car, we use model predictive control. The steps in order to achieve model predictive control are the following:
1. We fit a 3 degree polynomial based on the reference points on the track we are supposed to follow (those are given)
2. We define a loss function (`FG_eval` in `MPC.cpp`) that translates the values of our actuators (present and future) into a number that indicates how far or close we are from the target trajectory (the polynomial previously fitted) and how smooth we want to drive the car
3. We minimize that function in order to get the current steering and throttle we should set right now. In our current case, we use automatic differentiation to get a local minimum, because our function is non-convex (if it was, we could use convex optimization to find a global minimum)

Note: we need to set the parameters `N` and `dt` in order to define our far in the futures we want to predict our trajectory. We have set `N` to 10 (higher numbers we tried, such as 100, let to very strange behaviour) and `dt` to 0.1 (in order to make it easy to account for latency).


### Dealing with latency
In order to deal with the 100ms simulated latency, we have tweaked the loss function to take into account the actuators at time t in order to compute the model equations at time t+2 (see comment in `FG_eval:MPC.cpp` ).



---


### Running the code

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.
