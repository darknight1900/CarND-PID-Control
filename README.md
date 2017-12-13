# CarND-Controls-PID
Self-Driving Car Engineer Nanodegree Program

## Reflection
---
### Describe the effect each of the P, I, D components had in your implementation.
- P components decides how fast the controller reacting to the Cross Track Error (CTE). CTE indicates how far away the vechile is from the middle 
of the road. It is quite intuitive as when the vechile is to the left of the middle line, we want to steer to the right and if the vechile is to the right side of the middle line, we want to steer to left. A larger P components will correct the CTE quicker, however it will also incur more oscillations due to overshoot. If a P component is too small, the vechile will react to CTE too slowly and it might eventually run out of track. 

- I components is to integrate all the past CTEs until current point. This component is trying to correct the systematic bias of a vechile. The bias could be a steering drift which might prevent P-D controller from converging to middle of the line. In this project, the systematic bias seems to be very small as my twiddle training shows that a very small I component is enough for the vechile to stay around the center line. 

- D component is decided by the first deriative of CTE. It has the opposite effect as P component and it tries to smooth the controller. If the CTE change over time is too sharp (oscillation), the first deriative of CTE will be large. By incorporating deriative of CTE into the controller, we can drive those sharp steering to zero so that vechile can drive more smoothly on the road. 

### Describe how the final hyperparameters were chosen.
The final hyperparameters (P, I, D coefficients) are selected by a mix of manual tuning and twiddle algorithm. 
Also, to make the tuning process easiler, I also update my PID controller to calculate throttling so that vechile can maintain constant speed.
#### Manual tuning
- First, I will set I, D coefficients to be zero and manually tune P coefficients so that I can have a P which allow the vechile stay on track for ~1000 steps
- Then, I start manually tuning D coefficients so that there is a combination of P, D coefficients to allow the vechile to stay on track for the whole loop. The P,D coefficients I have after this step is (0.09 and 0.2)
#### Twiddle tuning
Twiddle tuning can be enabled in main.cpp by changing twiddle_training to be true. I am searching the P, I, D coefficients within below ranges
- P: [0.01, 4]
- I: [0, 0.01]
- D: [0.01, 4] 

During the twiddle tuning stage, the sum of error from the first 1500 steps are collected for each iteration. Twiddle will try a different set of P, I, D coefficients after each iteration. If the sum of error is reduced with the new set of coefficients, we will log the current error as best error and will also log this set of coefficients. 
After about 10 hours twiddle training, I got a relatively small accumulated CTE error with P, I, D coefficients  (0.172, 6.93e-06, 1.58738) for constant vehicle speed at about 48 mph.

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
  * Run either `./install-mac.sh` or `./install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets 
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.
* Simulator. You can download these from the [project intro page](https://github.com/udacity/self-driving-car-sim/releases) in the classroom.

There's an experimental patch for windows in this [PR](https://github.com/udacity/CarND-PID-Control-Project/pull/3)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`. 

Tips for setting up your environment can be found [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)

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
of CarND. If you are enrolled, see [the project page](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/f1820894-8322-4bb3-81aa-b26b3c6dcbaf/lessons/e8235395-22dd-4b87-88e0-d108c5e5bbf4/concepts/6a4d8d42-6a04-4aa6-b284-1697c0fd6562)
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

