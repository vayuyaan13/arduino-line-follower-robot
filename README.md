# Arduino Line Follower Robot

Build a simple Arduino line follower robot that detects a track with IR sensors and adjusts the two drive motors to stay on the line.

This repository is intended as a practical starting point for students and makers. It includes the core parts list, wiring guidance, example control logic, calibration notes, and troubleshooting steps.

## How the robot works

A basic line follower uses two IR sensors mounted near the front of the robot. The sensors distinguish the line from the surrounding surface. Arduino reads those sensor states and changes the left and right motor speeds or directions to correct the robot's position.

The control loop is simple:

1. Read the left and right IR sensors.
2. Determine whether the robot is centered, drifting left, or drifting right.
3. Adjust the motor outputs.
4. Repeat quickly while the robot is moving.

The exact sensor output depends on the module and track surface, so calibration matters.

## Main components

- Arduino Uno R3
- 2-channel motor driver such as L298N or L293D
- Two geared DC motors
- Two IR line tracking sensors
- Robot chassis and wheels
- Battery pack suitable for the motors and driver
- Jumper wires and a switch

## Example wiring

The following is a common arrangement. Check the pin labels on your specific motor driver before connecting power.

| Part | Arduino |
|---|---|
| Left IR sensor OUT | D2 |
| Right IR sensor OUT | D3 |
| Motor driver IN1 | D8 |
| Motor driver IN2 | D9 |
| Motor driver IN3 | D10 |
| Motor driver IN4 | D11 |
| Motor driver ENA/ENB | PWM-capable pins, as supported by the driver |

Connect the sensor and driver grounds to Arduino GND. Do not power the motors directly from an Arduino I/O pin.

## Control logic

A two-sensor robot can begin with four basic states:

| Left sensor | Right sensor | Typical action |
|---|---|---|
| Line | Line | Move forward or follow the sensor's calibrated interpretation |
| Line | Off line | Correct toward one side |
| Off line | Line | Correct toward the other side |
| Off line | Off line | Stop, search, or continue briefly depending on the track |

Because sensor modules can report black as either HIGH or LOW, test their output before finalizing the conditions in code.

## Example Arduino sketch

```cpp
const int leftSensor = 2;
const int rightSensor = 3;

const int leftMotor1 = 8;
const int leftMotor2 = 9;
const int rightMotor1 = 10;
const int rightMotor2 = 11;

void setup() {
  pinMode(leftSensor, INPUT);
  pinMode(rightSensor, INPUT);

  pinMode(leftMotor1, OUTPUT);
  pinMode(leftMotor2, OUTPUT);
  pinMode(rightMotor1, OUTPUT);
  pinMode(rightMotor2, OUTPUT);
}

void forward() {
  digitalWrite(leftMotor1, HIGH);
  digitalWrite(leftMotor2, LOW);
  digitalWrite(rightMotor1, HIGH);
  digitalWrite(rightMotor2, LOW);
}

void turnLeft() {
  digitalWrite(leftMotor1, LOW);
  digitalWrite(leftMotor2, LOW);

  digitalWrite(rightMotor1, HIGH);
  digitalWrite(rightMotor2, LOW);
}

void turnRight() {
  digitalWrite(leftMotor1, HIGH);
  digitalWrite(leftMotor2, LOW);

  digitalWrite(rightMotor1, LOW);
  digitalWrite(rightMotor2, LOW);
}

void stopRobot() {
  digitalWrite(leftMotor1, LOW);
  digitalWrite(leftMotor2, LOW);
  digitalWrite(rightMotor1, LOW);
  digitalWrite(rightMotor2, LOW);
}

void loop() {
  int left = digitalRead(leftSensor);
  int right = digitalRead(rightSensor);

  // Change these conditions after checking your sensor's actual output.
  if (left == LOW && right == LOW) {
    forward();
  } else if (left == HIGH && right == LOW) {
    turnLeft();
  } else if (left == LOW && right == HIGH) {
    turnRight();
  } else {
    stopRobot();
  }

  delay(5);
}
```

## Calibration

Place the robot over the actual track before tuning the sensor modules. Adjust each IR module's potentiometer until the digital output changes reliably between the line and background.

Then test the robot at low speed. If it oscillates, reduce the motor speed or make the correction less aggressive. If it repeatedly loses the line, check sensor height, alignment, track contrast, and motor direction.

## Troubleshooting

### Robot moves backward

Swap the polarity of the affected motor or reverse the logic used for that motor.

### Robot turns in the wrong direction

Check the left/right sensor connections and confirm which sensor state means line detected.

### Both motors run but the robot does not follow the line

Recalibrate the IR sensors and verify that they are mounted at similar heights and angles.

### Robot resets when motors start

This can happen when motor current causes the supply voltage to drop. Use a suitable motor supply, keep motor wiring separate from sensitive signal wiring, and make sure grounds are connected correctly.

## Next steps

After the two-sensor version works, you can improve it with PWM speed control, three or five IR sensors, sharper-turn handling, PID control, and better chassis alignment.

For an additional beginner-friendly explanation of building and tuning an Arduino line follower robot, see the related guide on Vayuyaan:

**[Line Follower Robot Using Arduino](https://vayuyaan.com/blog/line-follower-robot-using-arduino/)**

The guide expands on the project setup, working principle, components, wiring, code, and practical tips.

## License

This project is released under the MIT License.
