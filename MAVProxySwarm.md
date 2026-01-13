## Multi-Drone SITL Simulation with MAVProxy and Mission Planner

**Purpose**: Demonstrate launching multiple ArduCopter vehicles in SITL, visualize them in MAVProxy, and forward telemetry to Mission Planner

## Overview

This exercise demonstrates:

- Running **3 ArduCopter SITL instances** on Windows.
- Connecting them to **MAVProxy** for command and control.
- Forwarding **MAVLink telemetry via UDP** to Mission Planner.
- Controlling drones **individually** or **collectively**.
- Visualizing drone positions and movements in **Mission Planner** in real-time.

## Prerequisites

- **Windows 10/11**
- [Python 3.11+](https://www.python.org/)
- [MAVProxy](https://ardupilot.org/mavproxy/docs/getting_started/download_and_installation.html)
- [Mission Planner](https://ardupilot.org/planner/docs/mission-planner-installation.html)
- [ArduPilot SITL](https://ardupilot.org/dev/docs/sitl-simulator-software-in-the-loop.html)

## Setup Instructions

### 1. Launch SITL Drones

On Windows, go the folder where you have arducopter.exe (check start.md for reference) open separate terminals for each drone and run:

```bat
arducopter.exe -w -S --model quad --speedup 1 --defaults parameters/copter.parm -I0 --sysid 1
arducopter.exe -w -S --model quad --speedup 1 --defaults parameters/copter.parm -I1 --sysid 2
arducopter.exe -w -S --model quad --speedup 1 --defaults parameters/copter.parm -I2 --sysid 3
```

* -I<n> specifies the instance index.

* --sysid <n> assigns a unique ID to each drone.

* TCP ports default to 5760 + instance index.

### 2. Start MAVProxy

Open a new terminal and run:

```python -m MAVProxy.mavproxy ^
 --master=tcp:127.0.0.1:5760 ^
 --master=tcp:127.0.0.1:5770 ^
 --master=tcp:127.0.0.1:5780 ^
 --out=udp:127.0.0.1:14550 ^
 --console --map
```

* --master connects to each SITL instance via TCP.

* --out forwards MAVLink telemetry to Mission Planner via UDP 14550.

* --console enables command-line control.

* --map shows drone positions on a map.

### Control Commands
**Individual Vehicle Control**

Switch the active drone in MAVProxy:

Use vehicle <n> to set the active vehicle for example
```
vehicle 1
vehicle 2
vehicle 3
```

Then send commands, for eg:

```
arm throttle
takeoff 10
guided <latitude> <longitude> <altitude>
```

### Collective Commands (Swarm)

Send commands to all drones simultaneously:

```
alllinks arm throttle
alllinks takeoff 10
alllinks mode RTL
```

### Forwarding Telemetry to Mission Planner

* Open Mission Planner.

* Click on connect button on top-right of, select UDP as the connection type.

* Set port to 14550.

* Click Connect.

* Mission Planner will show all 3 drones in real-time.

* MP also shows monitoring altitude, speed, heading, and position.

## Notes & Tips

* Ensure unique SYSIDs and TCP ports for each drone.

* Avoid identical home locations to prevent visual overlap.

* The PyPI help module error in MAVProxy can be ignored; it does not affect simulation.

* Use alllinks for synchronized commands, vehicle n for individual control.