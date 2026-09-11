##  **Project Title**

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
