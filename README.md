The **Autonomous Car with Robotic Arm using ESP32** was one of the projects we proudly participated in with during the **Plaza Competition 2026**, organized by Team Starks at Faculty of Engineering, Mansoura University.

The system was designed to perform multiple intelligent tasks autonomously and manually. As illustrated in the schematic diagram, the car relies on **three ultrasonic sensors** positioned at different directions to detect walls and obstacles, enabling it to **solve mazes automatically** and determine the correct path without any human intervention.

In this project, I worked mainly as a Hardware Engineer with a basic contribution to the software side. My role included designing and integrating the electronic system, including the ESP32 microcontroller, motor drivers, servo connections, power regulation circuits, and sensor interfacing, as illustrated in the schematic diagram.

At the core of the system, an ESP32-WROOM microcontroller handles sensor processing, motor control, and communication tasks. The movement system is powered through dual motor drivers, allowing the robot to navigate efficiently through different environments, including obstacles and ramps during fight scenes and competitions.

In addition, the robot is equipped with a **robotic arm** controlled using **five servo motors**, as shown in the servo section of the schematic. The arm is capable of lifting and handling objects with precision. Unlike the autonomous navigation mode, the robotic arm is operated manually using a PlayStation 4 Controller, giving the user real-time control and flexibility during operation.

The schematic also demonstrates the integration of:

* Ultrasonic sensing modules for autonomous navigation
* ESP32 MCU for central processing
* Motor drivers for DC motor control
* Servo driver circuits for robotic arm movement
* Level shifter and power regulation circuits for stable operation

This project combines **embedded systems, robotics, autonomous navigation, wireless control, and mechanical design** into a single intelligent robotic platform capable of both autonomous and human-controlled operations.
