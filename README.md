# CarND-Controls-MPC Project
Self-Driving Car Engineer Nanodegree Program

---

## Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.


## Answer to Rubric:Implementation

### The Model

This project applies a kinematic bicycle model to its vehicle model.

The state vector of the model is as follows.

    // read state vector
    double x    = state[0]; // X Position
    double y    = state[1]; // Y Position
    double psi  = state[2]; // Orientation Angle
    double v    = state[3]; // Velocity
    double cte  = state[4]; // Cross Track Error
    double epsi = state[5]; // psi Error

Actuator outputs are acceleration(a/throttle in the code) and delta(delta/steering_angle).

The update equations with them are as follows.

    // x_[t+1] = x[t] + v[t] * cos(psi[t]) * dt
    // y_[t+1] = y[t] + v[t] * sin(psi[t]) * dt
    // psi_[t+1] = psi[t] + v[t] / Lf * delta[t] * dt
    // v_[t+1] = v[t] + a[t] * dt
    // cte[t+1] = f(x[t]) - y[t] + v[t] * sin(epsi[t]) * dt
    // epsi[t+1] = psi[t] - psides[t] + v[t] * delta[t] / Lf * dt

`Lf` is a physical characteristic of the vehicle telling the distance between the front of the vehicle and its center of gravity.

The vehicle model is coded in `FG_eval` class.


### Timestep Length and Elapsed Duration (N & dt)

In the final project, N value is 10 and dt value is 0.1.
Here, T would be 1 seconds.

As a guidelines in lesson,
T should be as large as possible, while dt should be as small as possible.

Because the latency of the simulator is set 100msec, dt should be equal or larger than 50msec.

When the reference velocity is set to 100 mph, I had some examinations as folows.

- N= 5, dt=0.05 (T is 0.25 second) NG
- N= 5, dt=0.1 (T is 0.5 second) NG
- N= 5, dt=0.2 (T is 1 second) NG
- N= 5, dt=0.5 (T is 2.5 second) NG

- N= 8, dt=0.05 (T is 0.4 second) OK, but Crawling
- N= 8, dt=0.125 (T is 1 second) OK
- N= 8, dt=0.25 (T is 2 second) OK
- N= 8, dt=0.5 (T is 4 second) OK

- N=10, dt=0.05 (T is 0.5 second) OK, but Crawling
- N=10, dt=0.1 (T is 1 second) OK
- N=10, dt=0.2 (T is 2 second) OK
- N=10, dt=0.5 (T is 5 second) OK

- N=20, dt=0.05 (T is 1 second) NG
- N=20, dt=0.1 (T is 2 second) OK
- N=20, dt=0.2 (T is 4 second) OK
- N=20, dt=0.5 (T is 10 second) OK

Thus, 8 or larger N and 100msec dt showed good results.


### Polynomial Fitting and MPC Preprocessing

The waypoints are preprocessed into the coordinates in the vehicle coordinate system with the following code.

    // Transform the points to the vehicle's orientation
    for (int i = 0; i < ptsx.size(); i++) {
       double x = ptsx[i] - px;
       double y = ptsy[i] - py;
       ptsx_car[i] = x * cos(-psi) - y * sin(-psi);
       ptsy_car[i] = x * sin(-psi) + y * cos(-psi);
    }

After the transformation,
X, Y Position and Orientation Angle in the state vector has always zero.


### Model Predictive Control with Latency

After the waypoints transformation above,
state vector are replaced with new values based on 100msec delay via folowing code.

    // Latency for predicting time at actuation
    const double dt = 0.1;

    // Predict state after latency
    // x, y and psi are all zero after transformation above
    double pred_px = 0.0 + v * dt; // Since psi is zero, cos(0) = 1, can leave out
    const double pred_py = 0.0; // Since sin(0) = 0, y stays as 0 (y + v * 0 * dt)
    double pred_psi = 0.0 + v * - steering_angle / Lf * dt;
    double pred_v = v + throttle * dt;
    double pred_cte = cte + v * sin(epsi) * dt;
    double pred_epsi = epsi + v * - steering_angle / Lf * dt;

    // Feed in the predicted state values
    Eigen::VectorXd state(6);
    state << pred_px, pred_py, pred_psi, pred_v, pred_cte, pred_epsi;

### Answer to Rubric:Simulation

Here's a [link to my video result](./MPC-demo.mp4)




# CarND-Controls-MPC (Original README.md)
Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Tips

1. It's recommended to test the MPC on basic examples to see if your implementation behaves as desired. One possible example
is the vehicle starting offset of a straight line (reference). If the MPC implementation is correct, after some number of timesteps
(not too many) it should find and track the reference line.
2. The `lake_track_waypoints.csv` file has the waypoints of the lake track. You could use this to fit polynomials and points and see of how well your model tracks curve. NOTE: This file might be not completely in sync with the simulator so your solution should NOT depend on it.
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)
4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)
5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric

Note: regardless of the changes you make, your project must be buildable using
cmake and make!

More information is only accessible by people who are already enrolled in Term 2
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/b1ff3be0-c904-438e-aad3-2b5379f0e0c3/concepts/1a2255a0-e23c-44cf-8d41-39b8a3c8264a)
for instructions and the project rubric.

## Hints!

* You don't have to follow this directory structure, but if you do, your work
  will span all of the .cpp files here. Keep an eye out for TODOs.

## Call for IDE Profiles Pull Requests

Help your fellow students!

We decided to create Makefiles with cmake to keep this project as platform
agnostic as possible. Similarly, we omitted IDE profiles in order to we ensure
that students don't feel pressured to use one IDE or another.

However! I'd love to help people get up and running with their IDEs of choice.
If you've created a profile for an IDE that you think other students would
appreciate, we'd love to have you add the requisite profile files and
instructions to ide_profiles/. For example if you wanted to add a VS Code
profile, you'd add:

* /ide_profiles/vscode/.vscode
* /ide_profiles/vscode/README.md

The README should explain what the profile does, how to take advantage of it,
and how to install it.

Frankly, I've never been involved in a project with multiple IDE profiles
before. I believe the best way to handle this would be to keep them out of the
repo root to avoid clutter. My expectation is that most profiles will include
instructions to copy files to a new location to get picked up by the IDE, but
that's just a guess.

One last note here: regardless of the IDE used, every submitted project must
still be compilable with cmake and make./

## How to write a README
A well written README file can enhance your project and portfolio.  Develop your abilities to create professional README files by completing [this free course](https://www.udacity.com/course/writing-readmes--ud777).
