# Stewart Stack

**A robotic arm built from three stacked tilting platforms, with camera-based tracking.**

[![Stewart Stack demo video](https://img.youtube.com/vi/VIDEO_ID/maxresdefault.jpg)](https://youtube.com/shorts/B0Hum-vTiDE?feature=share)

▶️ *Click the image to watch the demo on YouTube.*

---

## What it does

Three triangular platforms, each tilted by three linear actuators, are stacked on top of each
other. Small tilts at every stage add up to a large, smooth bend, so the tip of the arm can
sweep a wide dome above its base.

- Holds each platform at an **absolute angle relative to gravity**, whatever the stages below are doing
- Moves all three platforms along **synchronized, smooth trajectories** that arrive together
- **Tracks the arm with a depth camera** and shows it live in a 3D web view
- **Follows a fingertip** detected by the camera
- **Collects its own training data** by sweeping through poses and recording where the tip ends up

---

## Engineering challenges

**Stages that disturb each other.**
Every move of a lower platform tilts everything above it, so each stage's sensor sees motion it
didn't cause. Solved with cross-stage coupling compensation and a hybrid control scheme that
switches between actuator feedback during motion and IMU feedback at rest.

**Coordinated motion across the stack.**
Three independently driven stages have to start, move and stop as one. Solved with time-based
minimum-jerk trajectories and velocity feed-forward, executed in sync on every stage.

**Smooth, jitter-free motion with simple DC actuators.**
Low-cost DC actuators have static friction and a dead zone: too little power and they don't
move, a little more and they overshoot. A naive controller hunts back and forth around the
target. Solved with shaped motor commands, settle zones with hysteresis, and protected
direction reversals, so the arm glides into position and holds still.

**Orientation near the vertical.**
At steep angles the standard roll/pitch representation breaks down (gimbal lock), and the arm
became unstable. Replaced it with a singularity-free attitude estimate and control law.

**Self-calibration and safety.**
The arm calibrates itself at every power-up: it finds each actuator's mechanical limits and
measures its own geometry instead of relying on nominal values. Runtime stall detection
protects the motors when a target is out of reach.

**Seeing the arm.**
Marker-based pose estimation, stereo depth and hand tracking are brought into one shared
reference frame anchored to the arm's base. Noisy fingertip measurements are stabilized with
a filter that rejects outliers.

**Toward learned inverse kinematics** *(in progress)*.
An automated pipeline sweeps the arm through its workspace and records platform angles against
the measured tip position, building a dataset for a model that maps a target point to the
angles that reach it.

---

## Tech stack

`Embedded C++` · `ESP32 / FreeRTOS` · `Control Systems` · `Sensor Fusion` · `IMU` ·
`Trajectory Planning` · `Computer Vision` · `OpenCV` · `OAK-D / DepthAI` · `Python` ·
`Kalman Filtering` · `Kinematics` · `WebSocket` · `Three.js`

---

*Work in progress. The source code is kept in a private repository. Questions and ideas are
welcome; feel free to open an issue.*
