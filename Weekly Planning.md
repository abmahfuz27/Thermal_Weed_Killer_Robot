## **Nine-Week Master Schedule**

We have:

* **8 weeks for development**
* **1 additional week for performance and parameter testing**

The following schedule is realistic if the team works in parallel.

---

### **Week 1 — Project Foundation and Requirements**

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

### **Week 2 — Build the Basic Rover**

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

### **Week 3 — Manual Control and Communication**

### Main goal

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

### **Week 4 — Train the First YOLO Model**

### Main goal

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

### **Week 5 — Deploy YOLO and Develop Autonomous Movement**
### Main goal

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

### **Week 6 — Develop and Test the Servo Arm**

### Main goal

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

### **Week 7 — Thermal Subsystem Development**

### Main goal

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

### **Week 8 — Full System Integration**

### Main goal

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

### **Week 9 — Performance and Parameter Testing**

This week should not be used for major construction.

The robot should already be functional before Week 9.

### Main goal

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
