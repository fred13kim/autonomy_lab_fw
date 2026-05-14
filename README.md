# Autonomy Lab FW
Fred Kim EE'26

## Introduction
For the Spring 2026 semester of VIP, my focus was twofold:
* **Onboard new members of the club & provide them the basic footing to develop**
* **Create a unified vision for student members**

Before the beginning of any development it was necessary to analyze the current
state of the club and project.

## Autonomy Lab
As the president of the club, I believed creating solid groundwork for students
to collaborate, share ideas, and develop for an autonomous vehicle was
necessary. A goal was formulated in the beginning of the system: make a 1/10th
scale re-design of the IGVC car. I believed this goal was necessary for students
to not only build upon the past projects of VIP, but also bulid the foundational
skills necessary to start a project from the ground up. We characterized the
systems that were required for this project and delegated tasks to subteams and
members. Noteable goals that were accomplished within this semester was
**onboarding projects** and the crucial **CANBUS** project.

## Firmware Beginnings
The firmware stack of the club is transitioning to the Raspberry Pi Pico2 which
features the RP2350 micro-controller. Previously the club used the DBW Node
boards that featured an ESP32S3 micro-controller. However, the decision to
transition to a new board was suggested by alumni Jacob Koziej and agreed upon
by me as the ESP32 stack introduced significant bloat and overhead that impedes
upon the simple learning framework of what we believe a micro-controller should have.

### Debugging Platform & Onboarding
Every firmware student was provided a debugging platform consisting of a main
Pico2 mcu & a Pico2 running debug probe firmware. To make sure the setup of this
development environment was seemless for both linux & windows users the
`opneocd_setup.md` file was created and heavily tuned/updated to ensure students
wouldn't run into issues when interfacing with the micro-controllers.

Alongside the markdown file, I took the responsibilty to host these `setup`
sessions with students. Every step of the procedure was explained in full detail
& discussion. Portions of the `setup` instructions were re-iterated to make sure
students had the basics of why the setup was necessary and what each component
of the setup does.

Members were to begin by wiring their development kits & flashing a basic
firmware, all done within the period of a single `setup` session. Additionally,
members not in firmware such as Amanda, Wendy, & Paddy also joined in these
`setup` sessions in interest of getting a hollistic view of the development
stack.

### CANBUS
The CANBUS team consists of EEHW & FW members working alongside eachother to
make the Pico2 communicate with eachother using the CANBUS protocol on a new
SPI2CAN board. The tasks were split up as follows:
* EEHW: Wendy Zhu (EE'27) & Amanda Su (EE'28) were responsible for creating the board to convert SPI
messages to CAN messages surrounding the MCP218FD IC
* FW: Matthew Jeong (EE'28) & Marco Chen (EE'28) were responbile for writing the
  firmware driver libaries needed to ensure a proper CAN message is sent from
the Pico2

### UART Protocol
New members were tasked with interfacing with low level (register-level) of the
micro-controller. From a high-level view of how `printf()` appears on the
console, members read through the PICO-SDK & RP2350 datasheet to discover a bit
more of the innerworkings of a micro-controller. 

All of these projects were sub-advised by me and weekly check-in meetings were
held to overcome roadblocks.

## Future Work & Plans
* Need to incorporate RTOS capability to drive CAN & rate-tasking
* Motor driver firmware & board
* High-level application for controls
* Encorporate LIDAR into stack
