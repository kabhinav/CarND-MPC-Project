# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

## The Model
Simulated vehicle state has following elements:

1. x: position in forward direction
2. y: position in lateral direction
3. psi: orientation of vehicle
4. v: velocity of vehicle

The vehicle also have two actuators: steering angle delta (-25, 25 radians) and acceleration (-1 reverse, 1 forward).Additionally, the constant Lf defines as the distance between car fromt and its center of gravity.

## Timestep length and Elapsed duration
The value of timestep length and frequency underwent an iterative tuning process to arrive at final values of 10 and 0.15 respectively. I started with N=25 and duration dt=0.05. However, this selection made the vehicle unstable at sharp turns and on one of the turns the vehicle veered off the road.

Reducing the number of steps with increased length of time steps made the vehicle more stable at speeds around 45 mph. Reduced number of steps also help in improving the solver performance and the system appeared more responsive. I think looking ahead too far on the horizon made solver to predict sharper steering angles.

## Polynomial Fitting
First, the waypoints are transformed into vehicle coordinate system. For polynomial fitting I used as third order polynomial of form  "y = a*x^3 + b*x^2 + c*x + d" to fit the truns on the simulated track.

In the initial state vector for solver, x, y and psi are set to 0 since the coordinate system is relative to the vehicle. Intial cte is equal to 3rd order fitted polynomial in this coordinate system and initial orientation is calculated from derivative of the polynomial.

## Model Predictive control with latency
To solve the latency issue, I looked in Udacity forum discussions for different ways to handle the latency. Either latency is built into coffecients before giving them to solver OR we can use mean of N states for sterring delta and acceleration values. I chose to take the later approach and calculated delta, acceleration values mean with N=3. Since each time step is 150 ms, choosing 3 as N value results in averaging steering values over 450 ms.


## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
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
* Fortran Compiler
  * Mac: `brew install gcc` (might not be required)
  * Linux: `sudo apt-get install gfortran`. Additionall you have also have to install gcc and g++, `sudo apt-get install gcc g++`. Look in [this Dockerfile](https://github.com/udacity/CarND-MPC-Quizzes/blob/master/Dockerfile) for more info.
* [Ipopt](https://projects.coin-or.org/Ipopt)
  * Mac: `brew install ipopt`
       +  Some Mac users have experienced the following error:
       ```
       Listening to port 4567
       Connected!!!
       mpc(4561,0x7ffff1eed3c0) malloc: *** error for object 0x7f911e007600: incorrect checksum for freed object
       - object was probably modified after being freed.
       *** set a breakpoint in malloc_error_break to debug
       ```
       This error has been resolved by updrading ipopt with
       ```brew upgrade ipopt --with-openblas```
       per this [forum post](https://discussions.udacity.com/t/incorrect-checksum-for-freed-object/313433/19).
  * Linux
    * You will need a version of Ipopt 3.12.1 or higher. The version available through `apt-get` is 3.11.x. If you can get that version to work great but if not there's a script `install_ipopt.sh` that will install Ipopt. You just need to download the source from the Ipopt [releases page](https://www.coin-or.org/download/source/Ipopt/) or the [Github releases](https://github.com/coin-or/Ipopt/releases) page.
    * Then call `install_ipopt.sh` with the source directory as the first argument, ex: `sudo bash install_ipopt.sh Ipopt-3.12.1`.
  * Windows: TODO. If you can use the Linux subsystem and follow the Linux instructions.
* [CppAD](https://www.coin-or.org/CppAD/)
  * Mac: `brew install cppad`
  * Linux `sudo apt-get install cppad` or equivalent.
  * Windows: TODO. If you can use the Linux subsystem and follow the Linux instructions.
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
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.
