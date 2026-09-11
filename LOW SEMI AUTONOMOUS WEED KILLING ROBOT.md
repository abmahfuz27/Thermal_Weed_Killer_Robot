# **Project Planning**

###  **Project Title**

*Development of a Low-Cost Semi-Autonomous Thermal Weed-Control Robot Using YOLO-Based Computer Vision*

---


## **System Workflow**

The final prototype will work approximately as follows:

```text
Human moves robot to crop aisle
                ↓
Human aligns robot with the aisle
                ↓
Autonomous mode activated
                ↓
Robot moves slowly through the aisle
                ↓
Webcam captures crop-field images
                ↓
Raspberry Pi runs YOLO model
                ↓
YOLO detects crop and weed
                ↓
System estimates weed position
                ↓
Crop-safety check
                ↓
Is the weed safe to treat?
        ┌───────────────┴───────────────┐
        │                               │
       No                              Yes
        │                               │
Ignore target                  Robot slows/stops
                                        ↓
                              Servo arm positions
                                        ↓
                              Thermal treatment
                                        ↓
                              Arm returns safely
                                        ↓
                              Robot continues
                                        ↓
                              Aisle completed
                                        ↓
                              Human takes control
```

---

## **Hardware Architecture**

The initial fixed component list is:

| Component                               | Purpose                                        |
| --------------------------------------- | ---------------------------------------------- |
| Four-wheel rover chassis                | Mobile platform                                |
| DC geared motors                        | Robot movement                                 |
| Motor driver                            | Drives the motors                              |
| ESP32 DevKit                            | Low-level control                              |
| Raspberry Pi 4                          | Computer vision and high-level decision-making |
| Webcam                                  | Image acquisition                              |
| Servo motor                             | Arm movement                                   |
| 3D-printed simple arm                   | Thermal-head positioning                       |
| Butane cylinder                         | Thermal-energy source                          |
| Butane-compatible piezoelectric ignitor | Ignition mechanism                             |
| Battery                                 | Electrical power                               |
| Emergency-stop system                   | Safety                                         |
| Heat shield and mounting brackets       | Thermal protection                             |

Some additional components will probably be necessary:

* Motor driver, such as a suitable dual-channel or multi-channel driver
* Buck converter
* Voltage regulator
* Servo power supply
* Jumper wires/connectors
* Mounting brackets
* Camera mount
* Mechanical fasteners
* Fuse
* Physical switch
* Emergency-stop button
* Heat-resistant metal plate
* Fuel-cylinder holder
* Fire extinguisher for supervised testing

---

## **Main Design Decision**

We will approach the project in Three sub-system: 

1. Software Sub-system 
3. Mechanical Sub-system
4. Fire Safety Sub-system 

So, At a time we will target for three sub-system

### Track A — System planning and hardware foundation

* Confirm dimensions
* Measure crop-row spacing
* Assemble chassis
* Make the rover move manually

### Track B — Computer vision dataset preparation

* Collect crop and weed images
* Decide the detection classes
* Annotate images
* Train a first YOLO model

### Track C — Thermal mechanism research and safety design

* Study the thermal mechanism
* Design the arm
* Test servo movement without flame
* Analyze heat shielding and fuel safety


---

## **Computer Vision Strategy**

### What Model We should Use

YOLO, Because it can provide:

* Object class
* Confidence score
* Bounding box
* Real-time or near-real-time detection

However, the most important issue is not simply selecting YOLO.

The most important issue is:

> **The model must be trained using images that resemble the actual experimental environment.**


---

### Which YOLO architecture ?

> **Recommended first model: YOLOv8n**

Reasons:

* Lightweight
* Suitable for embedded deployment
* Easy to train
* Large amount of documentation
* Compatible with common Python workflows
* More practical for Raspberry Pi than large YOLO models
* Good starting point for a university prototype


### Possible later alternatives

If YOLOv8n does not perform adequately, wemay test:

* YOLO11n
* Another lightweight YOLO nano model
* A quantized or optimized model



### Final recommendation

For the initial project plan:

```text
Training model: YOLOv8n
Deployment device: Raspberry Pi 4
Training platform: Google Colab or Kaggle
Inference framework: Ultralytics
```

---

## **What dataset should we use?**

### Best option: Our own field dataset

Our primary dataset should be collected from the actual experimental crop field.

The uploaded image shows a maize-like crop with weeds growing between the plants. Therefore, our dataset should include images similar to this environment.

Oour own dataset should contain:

* Crop plants
* Small weeds
* Large weeds
* Weeds close to crops
* Weeds far from crops
* Soil
* Irrigation pipes
* Shadows
* Different sunlight conditions
* Different camera distances
* Different camera heights
* Different crop growth stages

### Why not use only an existing dataset?

General datasets may contain:

* Different crops
* Different soil
* Different lighting
* Different weed species
* Different camera angles
* Different image quality

Therefore, an existing dataset may not represent our actual environment.

---

### Should we use an online dataset?

We may use an online dataset for initial experimentation, but it should not be our only dataset.

### Recommended dataset strategy

```text
Stage 1:
Use a small public dataset to test YOLO training.

Stage 2:
Collect images from our own experimental field.

Stage 3:
Annotate our own images.

Stage 4:
Fine-tune YOLO using our field dataset.

Stage 5:
Evaluate using completely unseen field images.
```

Possible sources for supplementary data include:

* Roboflow Universe
* Kaggle agricultural datasets
* Public crop/weed datasets
* Research datasets

But every external dataset must be checked for:

* License
* Crop type
* Weed type
* Annotation quality
* Similarity to our environment

---

## What classes should we detect?

### Recommended initial classes

Use two classes:

```text
Class 0: Crop
Class 1: Weed
```

This is preferable to detecting only weeds because crop detection can help with crop-safety decisions.

### Why crop detection is important

Suppose the model detects a weed near a maize plant.

If the system knows only that a weed exists, it may attempt treatment even when the crop is nearby.

If the system detects both crop and weed, it can apply a safety rule:

```text
If weed is too close to crop:
    Do not treat
```

### Optional classes

Do not add these at the beginning unless they are necessary:

* Irrigation pipe
* Stone
* Human
* Obstacle
* Soil
* Leaf
* Stem

Too many classes will increase annotation difficulty and may reduce model quality.

---

## How many images do we need?

For a first prototype:

* Minimum target: **500 images**
* Better initial target: **800–1,200 images**
* More important than quantity: **image diversity**

For example, 1,000 nearly identical images are less useful than 600 varied images.

### Suggested image distribution

| Image type                      | Approximate target |
| ------------------------------- | -----------------: |
| Crop-only images                |                100 |
| Weed-only or mostly weed images |                100 |
| Crop and weed together          |                300 |
| Weeds close to crops            |                150 |
| Different lighting and angles   |                150 |
| Difficult/occluded cases        |                100 |

These numbers do not need to be exact.

---

## Dataset collection rules

When collecting images:

1. Use the actual webcam if possible.
2. Mount the camera approximately where it will be installed.
3. Capture images from the robot’s expected viewpoint.
4. Avoid collecting only perfect images.
5. Include difficult cases.
6. Capture images at different times of day.
7. Include different distances from the plants.
8. Include weeds near and far from crop plants.
9. Avoid excessive blur.
10. Keep the original images organized.

### Suggested folder structure

```text
weed_robot_dataset/
│
├── images/
│   ├── train/
│   ├── val/
│   └── test/
│
├── labels/
│   ├── train/
│   ├── val/
│   └── test/
│
└── data.yaml
```

---

## 5.8 Annotation procedure

Use one annotation tool:

* Roboflow
* CVAT
* Label Studio
* LabelImg

For each image:

* Draw a bounding box around each visible crop plant.
* Draw a bounding box around each visible weed.
* Assign the correct class.
* Avoid inconsistent labeling.

### Annotation rules

For crop:

* Label the visible crop plant.
* Do not draw one giant box around an entire row unless the annotation method specifically requires it.

For weed:

* Label each clearly distinguishable weed.
* If several weeds are inseparable, use a consistent grouping rule.
* Do not label uncertain vegetation as weed.

---

## **Hardware Development Philosophy**


The  method is:

> **Build the hardware from the simplest working version to the complete system.**

We should use the following progression:

```text
Chassis
   ↓
Motors
   ↓
Motor driver
   ↓
ESP32 motor control
   ↓
Manual remote control
   ↓
Raspberry Pi communication
   ↓
Camera integration
   ↓
YOLO detection
   ↓
Servo arm
   ↓
Thermal subsystem
   ↓
Autonomous decision-making
   ↓
Full integration
```

---

## **Hardware Development Stages**

### Stage H1 — Mechanical chassis only
---

Start with:

* Four-wheel chassis
* Four motors
* Wheels
* Basic mounting plate

At this stage, do not install:

* Butane cylinder
* Flame system
* Arm
* Raspberry Pi
* Complex wiring

### Tasks

* Assemble the chassis.
* Check wheel alignment.
* Check motor mounting.
* Rotate each wheel manually.
* Check whether the chassis is mechanically stable.
* Measure the actual dimensions.

### Questions to verify

* Can the chassis carry the planned payload?
* Is the wooden platform strong enough?
* Is there enough space for the battery?
* Is there enough space for the Raspberry Pi and ESP32?
* Will the arm disturb the center of gravity?
* Is the robot too narrow for the crop aisle?

### Deliverable

A mechanically assembled four-wheel chassis.

---

### Stage H2 — Motor and ESP32 testing
---

Next, connect:

```text
ESP32 → Motor Driver → Four Motors
```

Do not add the Raspberry Pi yet.

### Required functions

* Forward
* Reverse
* Left
* Right
* Stop
* Speed control

### First test program

The ESP32 should execute a simple sequence:

```text
Move forward for 2 seconds
Stop for 2 seconds
Move backward for 2 seconds
Stop
Turn left
Stop
Turn right
Stop
```

### What must be tested

* Motor direction
* Motor synchronization
* Motor-driver heating
* Battery voltage
* Current consumption
* Wheel slip
* Chassis movement

### Deliverable

A rover that can move using ESP32 commands.

---

### Stage H3 — Manual remote control
---

After the motors work, develop manual control.

Possible control methods:

* ESP32 Wi-Fi webpage
* Laptop keyboard
* Smartphone webpage
* Joystick
* Remote-control interface

For the first version, the simplest option is recommended.

### Suggested first control interface

A simple ESP32 webpage with buttons:

```text
       FORWARD
LEFT     STOP     RIGHT
      BACKWARD
```

Each button sends a command to the ESP32.

### Required safety features

* Stop button
* Automatic stop if communication is lost
* Motors disabled at startup
* No automatic movement after reboot
* Emergency-stop input

### Deliverable

A manually controlled rover.

---

### Stage H4 — Raspberry Pi and ESP32 communication
---

Now connect the Raspberry Pi to the ESP32.

### Recommended initial communication

Use:

* USB serial, or
* UART

USB serial is easier for initial debugging.

### Architecture

```text
Raspberry Pi
    │
    │ Serial communication
    ▼
ESP32
    │
    ▼
Motor Driver
    │
    ▼
Motors
```

### Example commands

```text
FORWARD
BACKWARD
LEFT
RIGHT
STOP
SPEED 30
SERVO 90
THERMAL OFF
```

### Important rule

The Raspberry Pi sends high-level commands.

The ESP32 performs the actual hardware control.

For example:

```text
Raspberry Pi:
"Move forward"

ESP32:
Controls motor pins and PWM.
```

### Deliverable

Raspberry Pi successfully controls the rover through ESP32.

---

## **Computer Vision Development Stages**

Computer vision development should begin early, in parallel with the hardware.

Do not wait until the rover is completely finished before starting the dataset.

---

###  CV1 — Test YOLO on a computer
---

Before using the Raspberry Pi, train and test YOLO on:

* Google Colab
* Kaggle
* A desktop computer with GPU


The recommended workflow is:

```text
Dataset collection
       ↓
Annotation
       ↓
Training on Colab/Kaggle
       ↓
Model evaluation
       ↓
Export trained model
       ↓
Deploy on Raspberry Pi
```

---

### CV2 — Train a basic model
---

Start with a small dataset to verify the entire training pipeline.

### Initial process

1. Prepare 100–200 annotated images.
2. Create the YOLO dataset configuration.
3. Train YOLOv8n.
4. Check whether the model detects crop and weed.
5. Identify errors.
6. Improve the dataset.
7. Train again.

This first model is not necessarily the final model.

Its purpose is to verify that the pipeline works.

---

### CV3 — Build the complete field dataset
---

After confirming that training works:

1. Collect more field images.
2. Annotate them.
3. Divide into training, validation, and testing sets.
4. Train the improved model.
5. Evaluate it on unseen images.

### Suggested dataset split

```text
70% Training
20% Validation
10% Testing
```

Avoid placing nearly identical frames from one video into all three groups.

That would produce artificially high performance.

---

### CV4 — Evaluate the model
---

Record:

* Precision
* Recall
* mAP@50
* mAP@50–95
* Confusion matrix
* False-positive examples
* False-negative examples
* Inference time

### Most important error types

#### False positive

The model says something is a weed, but it is not.

#### False negative

The model fails to detect an actual weed.

#### Dangerous false positive

The model incorrectly identifies a crop as a weed.

For this project, dangerous false positives are especially important because the robot uses thermal treatment.

---

### CV5 — Deploy YOLO on Raspberry Pi
---

After the model performs reasonably well on the computer:

1. Install the required Python environment on Raspberry Pi.
2. Connect the webcam.
3. Load the trained model.
4. Run live detection.
5. Display bounding boxes.
6. Measure inference speed.
7. Check Raspberry Pi temperature.
8. Optimize the model if necessary.

### Expected challenge

YOLO inference on Raspberry Pi 4 may be slower than on a computer.

Therefore, we may need to use:

* Lower image resolution
* Lightweight model
* Frame skipping
* Lower robot speed
* Model optimization
* Efficient image preprocessing

### Deliverable

YOLO detects crop and weed using the actual Raspberry Pi webcam.

---

## **Target Localization Strategy**

YOLO gives image coordinates. The robot needs a physical target position.

This is one of the most important technical issues.

## **Do not begin with complicated 3D localization**

Avoid starting with:

* Stereo vision
* Depth cameras
* Full 3D reconstruction
* SLAM
* Complex inverse kinematics

These are unnecessary for the first version.

---

## **Use a simplified calibrated treatment zone**

The initial design can use:

* Fixed camera position
* Fixed camera angle
* Fixed arm height
* Fixed robot speed
* Limited lateral arm movement
* A predefined treatment area

For example:

```text
Camera image
┌─────────────────────────────┐
│                             │
│       Crop       Crop       │
│                             │
│             Weed            │
│              ↓              │
│       Treatment Zone        │
└─────────────────────────────┘
```

The system only treats weeds that appear inside a known region.

---

### Basic coordinate conversion

The image can be divided into zones:

```text
Left zone       Center zone       Right zone
```

The servo arm can move according to the zone:

```text
Weed in left zone:
    Servo moves left

Weed in center zone:
    Servo remains centered

Weed in right zone:
    Servo moves right
```

This is much easier than calculating precise world coordinates.

### Initial target-position method

```text
YOLO bounding box
        ↓
Calculate bounding-box center
        ↓
Compare center with image zones
        ↓
Select servo position
        ↓
Move arm
```

---

## **Servo Arm Development**

The arm should be simple.

## Do not design a full robotic arm

A multi-joint robotic arm may require:

* Multiple servos
* Complex inverse kinematics
* More power
* More mechanical precision
* More time for calibration
* More structural strength

For an 8-week project, this is risky.

---

##  Recommended arm design

Use:

* One servo for lateral movement
* Fixed vertical height
* Fixed or semi-fixed thermal-head orientation

Conceptually:

```text
          Thermal head
               |
               |
        3D-printed arm
               |
             Servo
               |
          Robot chassis
```

The robot’s forward movement provides one direction, while the servo provides lateral positioning.

---

## Servo testing sequence

Before attaching the thermal system:

1. Print the arm.
2. Mount the servo.
3. Test the servo independently.
4. Check the movement range.
5. Check mechanical stability.
6. Check whether the arm can hold the thermal-head weight.
7. Check vibration while the robot moves.
8. Add mechanical limits.
9. Test target positions using markers.

### Example servo positions

```text
0°   → Left
90°  → Center
180° → Right
```

The actual range depends on the arm design and servo.

Do not force the servo beyond its safe mechanical range.

---

## Important thermal protection issue

A normal 3D-printed plastic arm should not be directly exposed to:

* Flame
* High radiant heat
* Hot metal
* Heated nozzle surfaces

Use:

* Metal thermal-head bracket
* Heat shield
* Thermal barrier
* Adequate distance
* Heat-resistant mounting material

The plastic arm should only position the thermal mechanism from a safe distance.

---

## **Thermal Subsystem Development**

This subsystem must be developed separately from the robot.

Do not mount the butane system on the moving rover at the beginning.

---

## Thermal system components

The planned thermal subsystem includes:

* Butane cylinder
* Butane-compatible burner or thermal source
* Piezoelectric automatic ignitor
* Thermal-head mounting
* Servo-controlled positioning
* Fuel-cylinder holder
* Heat shield
* Emergency shutoff
* Thermal isolation

---

## Important design decision: Automatic ignition

The automatic piezoelectric ignitor may be mechanically activated by a servo, but this should not be treated as a basic feature.

Automatic ignition introduces additional risks:

* Ignition failure
* Gas accumulation
* Repeated ignition attempts
* Fuel leakage
* Unexpected flame
* Ignition near plastic or wiring

### Recommended development order

1. First test the thermal source manually and safely.
2. Confirm that the thermal source is compatible with the fuel.
3. Test the ignitor separately.
4. Determine whether ignition can be safely controlled.
5. Add a normally-off thermal enable mechanism.
6. Add an emergency shutdown.
7. Only then consider servo-assisted ignition.

### Safer project approach

For the first successful prototype:

> The thermal system may be manually ignited under supervision, while the robot autonomously controls target positioning.

This still demonstrates autonomous weed targeting and thermal treatment without making automatic ignition the main technical dependency.

If automatic ignition is required, it should be added only after the safety system is validated.

---

## Thermal subsystem safety requirements

The system must include:

* Secure fuel-cylinder mounting
* Fuel leakage inspection
* Heat shield
* Separation from battery
* Separation from Raspberry Pi and ESP32
* Separation from wiring
* Separation from wooden chassis
* Normally-off thermal state
* Physical emergency shutdown
* Manual override
* Controlled testing area
* Fire extinguisher
* Human supervision

### Do not test flame operation:

* Near dry grass
* Near loose crop residue
* Near fuel containers
* Near exposed batteries
* Near plastic components
* Near people without protection
* Near buildings
* During strong wind
* In uncontrolled public areas

---

## **Full Integration Strategy**

Only integrate the complete system after every major module works separately.

## Integration order

```text
Integration 1:
Rover + ESP32 + Motor Driver
```

```text
Integration 2:
Raspberry Pi + ESP32 + Rover
```

```text
Integration 3:
Raspberry Pi + Webcam + YOLO
```

```text
Integration 4:
YOLO + Simple target-position output
```

```text
Integration 5:
ESP32 + Servo Arm
```

```text
Integration 6:
YOLO target position + Servo Arm
```

```text
Integration 7:
Manual rover + YOLO detection
```

```text
Integration 8:
Autonomous movement + YOLO
```

```text
Integration 9:
Thermal system + Servo Arm
```

```text
Integration 10:
Complete supervised prototype
```

---

## Recommended Control Logic

The complete system should use a state-based control system.

## Main states

```text
IDLE
MANUAL_MODE
AUTONOMOUS_MODE
SEARCHING
WEED_DETECTED
TARGET_VALIDATION
TARGET_SAFE
TARGET_UNSAFE
ROBOT_STOPPED
ARM_POSITIONING
THERMAL_READY
TREATMENT
ARM_RETURN
CONTINUE_MOVEMENT
FAULT
EMERGENCY_STOP
AISLE_COMPLETE
```

---

## Example logic

```text
Start
  ↓
Safety check
  ↓
Manual mode
  ↓
Operator places robot in aisle
  ↓
Autonomous mode enabled
  ↓
Robot moves slowly
  ↓
Camera captures image
  ↓
YOLO detects crop and weed
  ↓
Check confidence
  ↓
Check target position
  ↓
Check crop-safety distance
  ↓
If unsafe:
    Ignore target
  ↓
If safe:
    Stop or slow robot
    Move servo arm
    Enable supervised thermal treatment
    Disable thermal treatment
    Return arm
    Continue movement
```

---

## **Nine-Week Master Schedule**

We have:

* **8 weeks for development**
* **1 additional week for performance and parameter testing**

The following schedule is realistic if the team works in parallel.

---

# Week 1 — Project Foundation and Requirements

## Main goal

Understand the field, freeze the design, and start the simplest hardware and dataset work.

### Tasks

#### A. Field investigation

* Visit the experimental crop field.
* Measure crop-row spacing.
* Measure crop height.
* Measure soil condition.
* Identify likely weed types.
* Identify irrigation pipes and obstacles.
* Determine where the robot can safely operate.

#### B. Mechanical planning

* Confirm four-wheel chassis dimensions.
* Determine battery location.
* Determine Raspberry Pi location.
* Determine ESP32 and motor-driver location.
* Determine camera mounting location.
* Estimate arm mounting position.
* Create a basic system layout.

#### C. Safety planning

* Identify flame-related risks.
* Decide where the fuel cylinder will be mounted.
* Plan heat shielding.
* Plan emergency shutdown.
* Decide whether the first thermal test will be manual ignition.

#### D. CV preparation

* Install YOLO training environment on Colab/Kaggle.
* Test YOLOv8n on a sample dataset.
* Decide annotation classes.
* Collect the first field images.

### Week 1 deliverables

* Finalized project scope
* Field measurement sheet
* Basic block diagram
* Initial safety plan
* Initial dataset
* YOLO training environment
* Chassis procurement/assembly started

---

# Week 2 — Build the Basic Rover

## Main goal

Make the rover physically move reliably.

### Tasks

* Assemble four-wheel chassis.
* Mount motors and wheels.
* Install motor driver.
* Connect ESP32.
* Prepare battery and power distribution.
* Test each motor.
* Implement forward/reverse/left/right/stop.
* Measure motor current if possible.
* Check chassis stability.

### Do not do yet

* Do not install the butane cylinder.
* Do not install the flame mechanism.
* Do not install the arm permanently.
* Do not attempt autonomous navigation.

### Week 2 deliverables

* Assembled rover
* ESP32 motor-control code
* Manual motor test results
* Basic power and battery test
* Mechanical layout drawing

---

# Week 3 — Manual Control and Communication

## Main goal

Make the rover controllable and establish Raspberry Pi–ESP32 communication.

### Tasks

#### A. Manual control

Develop:

* Forward
* Reverse
* Left
* Right
* Stop
* Speed control

#### B. Safety

Implement:

* Startup motor lock
* Communication timeout
* Emergency stop
* Manual override
* Thermal system disabled by default

#### C. Raspberry Pi–ESP32 communication

* Connect Raspberry Pi and ESP32.
* Send basic commands.
* Receive acknowledgment.
* Test communication reliability.

#### D. Dataset work

* Continue collecting images.
* Start annotation.
* Prepare the first meaningful dataset.

### Week 3 deliverables

* Manually controlled rover
* Raspberry Pi–ESP32 communication
* Command protocol
* Basic safety behavior
* Annotated initial dataset

---

# Week 4 — Train the First YOLO Model

## Main goal

Obtain a working crop-and-weed detection model.

### Tasks

* Complete initial image collection.
* Annotate the dataset.
* Split dataset into training, validation, and testing.
* Train YOLOv8n.
* Evaluate precision, recall, and mAP.
* Inspect false positives and false negatives.
* Improve labels and images.
* Train a second version if necessary.

### Important

Do not wait for a perfect model.

At the end of Week 4, we need a working baseline model that can be improved later.

### Week 4 deliverables

* Annotated dataset
* `data.yaml`
* Trained YOLOv8n model
* Initial evaluation metrics
* Example detection images
* Error analysis

---

# Week 5 — Deploy YOLO and Develop Autonomous Movement

## Main goal

Run computer vision on Raspberry Pi and begin constrained autonomous movement.

### Tasks

#### A. Raspberry Pi deployment

* Install YOLO inference environment.
* Connect webcam.
* Run live detection.
* Display bounding boxes.
* Measure inference speed.
* Optimize resolution/model if necessary.

#### B. Autonomous rover movement

Start with the simplest method:

1. Move forward slowly.
2. Stop after a fixed distance or time.
3. Confirm stable movement.
4. Add aisle-centering logic if feasible.

#### C. Crop-row guidance

Possible first approach:

* Use crop positions detected by YOLO.
* Estimate the aisle center.
* Compare robot center with aisle center.
* Adjust motor speeds.

Do not begin with SLAM or GPS.

#### D. Arm design

* Measure available mounting space.
* Create a simple CAD design.
* Decide the servo range.
* Print a first arm prototype.

### Week 5 deliverables

* YOLO running on Raspberry Pi
* Live webcam detection
* Basic autonomous forward movement
* Initial aisle-guidance logic
* First arm design

---

# Week 6 — Develop and Test the Servo Arm

## Main goal

Make the arm move to selected target positions without using flame.

### Tasks

* Print the arm.
* Mount the servo.
* Connect servo to ESP32.
* Test movement range.
* Test left, center, and right positions.
* Add mechanical limits.
* Check vibration.
* Test arm movement while the rover is stationary.
* Test arm movement while the rover moves slowly.
* Connect YOLO target zone to servo position.

### Initial control method

Use image zones:

```text
Left target   → Servo left
Center target → Servo center
Right target  → Servo right
```

Do not begin with complicated inverse kinematics.

### Week 6 deliverables

* Functional 3D-printed arm
* Servo-control code
* Target-zone-to-servo mapping
* Arm repeatability test
* No-flame targeting demonstration

---

# Week 7 — Thermal Subsystem Development

## Main goal

Develop and test the thermal subsystem separately and safely.

### Tasks

* Study the butane cylinder and compatible thermal source.
* Design secure cylinder mounting.
* Design heat shield.
* Design thermal-head bracket.
* Keep hot components away from plastic.
* Test the thermal source separately.
* Test treatment on isolated weeds or a controlled testbed.
* Determine preliminary treatment parameters.
* Test manual emergency shutdown.
* Evaluate whether automatic ignition is safe and practical.

### Important recommendation

Do not make the whole project dependent on automatic ignition.

A valid first version may use:

* Manual supervised ignition
* Autonomous target detection
* Autonomous arm positioning
* Controlled thermal treatment

Automatic ignition can be treated as an advanced feature.

### Week 7 deliverables

* Thermal subsystem design
* Safety assessment
* Heat-shield design
* Preliminary thermal treatment test
* Initial treatment parameters
* Decision on automatic ignition

---

# Week 8 — Full System Integration

## Main goal

Integrate the robot, YOLO, arm, and thermal subsystem.

### Tasks

#### A. Integrate the state machine

Implement:

* Manual mode
* Autonomous mode
* Weed detection
* Target validation
* Crop-safety check
* Robot stop/slow
* Servo positioning
* Thermal treatment
* Arm return
* Continue movement
* Fault handling

#### B. Integrate safety logic

The robot must stop or disable treatment if:

* Camera disconnects
* Raspberry Pi crashes
* ESP32 communication is lost
* Servo fails
* Target is too close to crop
* Obstacle is detected
* Emergency stop is pressed
* Thermal system is not ready

#### C. Perform staged integration

Do not begin with real flame immediately.

Use this sequence:

```text
YOLO + simulated treatment command
        ↓
YOLO + servo movement without heat
        ↓
YOLO + arm positioning with thermal system disabled
        ↓
Stationary thermal test
        ↓
Slow-moving supervised test
        ↓
Full aisle demonstration
```

### Week 8 deliverables

* Integrated prototype
* Working control-state diagram
* Integrated software
* Safety response test
* Preliminary field demonstration

---

# Week 9 — Performance and Parameter Testing

This week should not be used for major construction.

The robot should already be functional before Week 9.

## Main goal

Collect measurable results for the report.

---

## 9.1 Computer vision testing

Test under:

* Different lighting
* Different camera distances
* Different weed sizes
* Different crop densities
* Weeds near crops
* Partially hidden weeds

Record:

* Precision
* Recall
* mAP@50
* mAP@50–95
* False positives
* False negatives
* Inference time
* FPS

---

## 9.2 Navigation testing

Measure:

* Aisle traversal success
* Lateral deviation
* Average speed
* Number of stops
* Number of manual interventions
* Wheel-slip problems
* Communication failures

### Aisle success rate

$$
\text{Aisle Success Rate}
=
\frac{\text{Successful aisle trials}}
{\text{Total aisle trials}}
\times 100
$$

---

## 9.3 Targeting testing

Measure:

* Number of detected weeds
* Number of correctly targeted weeds
* Number of incorrectly targeted weeds
* Servo positioning error
* Targeting time
* Missed targets

### Targeting success rate

$$
\text{Targeting Success Rate}
=
\frac{\text{Correctly targeted weeds}}
{\text{Total selected weeds}}
\times 100
$$

---

## 9.4 Thermal treatment testing

Measure:

* Number of treated weeds
* Number of successfully damaged weeds
* Number of weeds showing regrowth
* Treatment time
* Treatment distance
* Treatment effectiveness for different weed sizes
* Crop damage
* Fuel consumption, if safely measurable

### Weed-control effectiveness

$$
\text{Weed Control Effectiveness}
=
\frac{\text{Successfully controlled weeds}}
{\text{Total treated weeds}}
\times 100
$$

---

## 9.5 Crop-safety testing

Record:

* Number of crop plants near treated weeds
* Number of damaged crop plants
* Distance between weed and crop
* Treatment conditions
* Type and severity of crop damage

### Crop damage rate

$$
\text{Crop Damage Rate}
=
\frac{\text{Damaged crop plants}}
{\text{Nearby exposed crop plants}}
\times 100
$$

For this project, crop damage should be treated as a critical result.

---

## **Team Division**

A four-person team could divide the work as follows.

## Member 1 — Mechanical and Chassis Engineer

Responsibilities:

* Chassis assembly
* Motor mounting
* Wheel alignment
* Battery placement
* Arm mounting
* CAD design
* 3D printing
* Mechanical testing

---

## Member 2 — Embedded and Motor-Control Engineer

Responsibilities:

* ESP32 programming
* Motor-driver control
* PWM speed control
* Servo control
* Raspberry Pi–ESP32 communication
* Manual-control interface
* Emergency-stop logic
* Fault handling

---

## Member 3 — Computer Vision Engineer

Responsibilities:

* Dataset collection
* Annotation
* YOLO training
* Model evaluation
* Raspberry Pi inference
* Target localization
* Crop-safety detection
* Computer-vision integration

---

## Member 4 — Thermal, Safety, and System-Integration Engineer

Responsibilities:

* Thermal mechanism research
* Fuel mounting
* Heat shielding
* Thermal-head design
* Treatment experiments
* Safety assessment
* State-machine integration
* Performance testing
* Documentation

### Important

Although tasks are divided, every member should understand the full system.

The project should not depend on only one person understanding the entire architecture.

---

## **Development Order**

This is the most important section of the entire guide.

Follow this order:

```text
STEP 1
Understand the field and measure crop-row spacing
        ↓
STEP 2
Freeze the project scope and safety requirements
        ↓
STEP 3
Assemble the four-wheel chassis
        ↓
STEP 4
Test motors using ESP32
        ↓
STEP 5
Develop manual remote control
        ↓
STEP 6
Add Raspberry Pi–ESP32 communication
        ↓
STEP 7
Collect crop and weed images
        ↓
STEP 8
Annotate the dataset
        ↓
STEP 9
Train YOLOv8n on Colab/Kaggle
        ↓
STEP 10
Evaluate and improve the model
        ↓
STEP 11
Deploy YOLO on Raspberry Pi
        ↓
STEP 12
Develop slow autonomous aisle movement
        ↓
STEP 13
Design and print the simple servo arm
        ↓
STEP 14
Test servo positioning without heat
        ↓
STEP 15
Develop thermal subsystem separately
        ↓
STEP 16
Test thermal treatment safely
        ↓
STEP 17
Connect YOLO target position to servo movement
        ↓
STEP 18
Integrate navigation, detection, arm, and thermal logic
        ↓
STEP 19
Perform supervised field trials
        ↓
STEP 20
Conduct performance and parameter testing
        ↓
STEP 21
Prepare report, presentation, and demonstration
```


Philosophy is:

> **First make it move. Then make it see. Then make it locate. Then make it aim. Then make it treat. Finally, make all of them work together safely.**
