# Exercise

- Implement Model Predictive Control to drive the car around the track.
- Not given the cross track error,
- you'll have to calculate that yourself!
- there's a 100 millisecond latency between actuations commands on top of the connection latency.



# Rubric: Implementation

- The Model

   - Student describes their model in detail.
     This includes the state, actuators and update equations.

- Timestep Length and Elapsed Duration (N & dt)

  - Student discusses the reasoning behind the chosen N (timestep
    length) and dt (elapsed duration between timesteps) values.
    Additionally the student details the previous values tried.

- Polynomial Fitting and MPC Preprocessing

  - A polynomial is fitted to waypoints.
  - If the student preprocesses waypoints, the vehicle state, and/or
    actuators prior to the MPC procedure it is described.

- Model Predictive Control with Latency

  - The student implements Model Predictive Control that handles a 100 millisecond latency.
    Student provides details on how they deal with latency.


# Rubric: Simulation

- The vehicle must successfully drive a lap around the track.

  - No tire may leave the drivable portion of the track surface.
    The car may not pop up onto ledges or roll over any surfaces that
    would otherwise be considered unsafe (if humans were in the vehicle).
  - The car can't go over the curb, but, driving on the lines before the curb is ok.


# Tips

1. It's recommended to test the MPC on basic examples to see if your
   implementation behaves as desired. One possible example is the
   vehicle starting offset of a straight line (reference). 
   If the MPC implementation is correct, after some number of timesteps 
   (not too many) it should find and track the reference line.

2. The `lake_track_waypoints.csv` file has the waypoints of the lake track.
   You could use this to fit polynomials and points and see of
   how well your model tracks curve. 
   NOTE: This file might be not completely in sync with the simulator
   so your solution should NOT depend on it.

3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)

4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)

5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.


# Quiz

git clone https://github.com/udacity/CarND-MPC-Quizzes.git



# TODOs on the sample code

    main.cpp:96:
      // TODO: Calculate steering angle and throttle using MPC.
      // Both are in between [-1, 1].
    
    MPC.cpp:8:
      // TODO: Set the timestep length and duration
    
    MPC.cpp:32:
      // TODO: implement MPC
      // `fg` a vector of the cost constraints, `vars` is a vector of variable values (state & actuators)
      // NOTE: You'll probably go back and forth between this function and
      // the Solver function below.
    
    MPC.cpp:50:
      // TODO: Set the number of model variables (includes both states and inputs).
      // For example: If the state is a 4 element vector, the actuators is a 2
      // element vector and there are 10 timesteps. The number of variables is:
      //
      // 4 * 10 + 2 * 9
    
    MPC.cpp:56:
      // TODO: Set the number of constraints
    
    MPC.cpp:68:
      // TODO: Set lower and upper limits for variables.
    
    MPC.cpp:115:
      // TODO: Return the first actuator values. The variables can be accessed with
      // `solution.x[i]`.
      //
      // {...} is shorthand for creating a vector, so auto x1 = {1.0,2.0}
      // creates a 2 element double vector.


