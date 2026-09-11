# **Project Planning**

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

## **What classes should we detect?**

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

## **How many images do we need?**

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

### Dataset collection rules

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

### Annotation procedure

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

## **Hardware Development**


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

### Do not begin with complicated 3D localization

Avoid starting with:

* Stereo vision
* Depth cameras
* Full 3D reconstruction
* SLAM
* Complex inverse kinematics

These are unnecessary for the first version.

---

### Use a simplified calibrated treatment zone

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

### Do not design a full robotic arm

A multi-joint robotic arm may require:

* Multiple servos
* Complex inverse kinematics
* More power
* More mechanical precision
* More time for calibration
* More structural strength

For an 8-week project, this is risky.

---

###  Recommended arm design

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

### Servo testing sequence

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

### Important thermal protection issue

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

### Thermal system components

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

### Important design decision: Automatic ignition

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

### Thermal subsystem safety requirements

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

### **Integration order**

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

### **Recommended Control Logic**

The complete system should use a state-based control system.

### **Main states**

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

### **Example logic**

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
