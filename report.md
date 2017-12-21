# Report: Model Predictive Control

[MPC Demo](https://youtu.be/A5sj3lrRzyY)

- **Student describes their model in detail. This includes the state, actuators and update equations.**

The kinematic model used considers the following state and control inputs:

State:
- x position (px)
- y position (py)
- orientation (psi)
- velocity (v)
- cross-track error (cte)
- orientation error (epsi)

Control:
- steering angle (delta)
- acceleration (a)

Changes in state are measured by the following update equations:
- x = x + v * cos(psi) * dt
- y = y + v * sin(psi) * dt
- psi = psi + v/Lf * delta * dt (where Lf is the distance between the nose of the vehicle and center of gravity)
- v = v + a ∗ dt
- cte = cte + v * sin(epsi) * dt
- epsi = epsi + v/Lf * delta * dt

These update equations allow us to predict where the vehicle will be in the future.

- **Student discusses the reasoning behind the chosen N (timestep length) and dt (elapsed duration between timesteps) values. Additionally the student details the previous values tried.**

A few different combinations of `N` and `dt` were considered and their effects on model performance. The final values are:
- `N: 13`
- `dt: 0.15`

This combination of values predicts over a time horizon of 1.8 seconds. Values on `N` higher than 20 tended to hinder performance in running calculations, while values of `dt` under 0.01 resulted in erratic driving behavior.

Combined with the weights applied during state calculation. The final `N` and `dt` values provided stable driving over multiple laps of the course, and achieved top speeds of ~90 mph.

- **A polynomial is fitted to waypoints. If the student preprocesses waypoints, the vehicle state, and/or actuators prior to the MPC procedure it is described.**

The waypoints were preprocessed by shifting waypoints in the global coordinate space to zero in the car's coordinate space.

The state is preprocessed to account for the inherent latency between prediction and actuation.

A third-degree polynomial fits a plot the waypoints and car's predicted trajectory.   

- **The student implements Model Predictive Control that handles a 100 millisecond latency. Student provides details on how they deal with latency.**

Latency is accounted for by calculating the change in state produced by the latency time delta. i.e. changes in position, orientation, velocity, and error based on previous state.

These changes can be tracked using the update equations as follows:
* since global coordinates are converted to the car's coordinate space in preprocessing, `cos(0) = 1` and `sin(0) = 0`

- px = v * latency
- py = 0
- psi = v * -delta/Lf * latency
- v = v + throttle * lt
- cte = cte + v * sin(epsi) * latency
- epsi = epsi + v / Lf * delta * latency
