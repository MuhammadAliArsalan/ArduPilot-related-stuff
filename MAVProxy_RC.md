## MAVProxy & ArduCopter SITL: RC Overrides, GUIDED & LOITER Workflow

This article documents experiments and learning with ArduCopter SITL and MAVProxy, focusing on RC overrides, LOITER mode, and throttle control. It also explains how to safely test guided missions without a physical RC.

### Introduction

When testing ArduCopter in SITL (Software In The Loop) using MAVProxy, some unexpected behaviors can occur if RC input is missing. Without a physical RC transmitter, autopilot assumes minimum throttle (0 PWM), causing the drone to descend or motors to disarm when switching to LOITER.

To simulate mid-stick (hover) throttle, we use RC channel overrides.

This guide shows the safe workflow and explains what each step does.

#### Key Concepts

* RC Input (Pilot Input): Normally comes from a physical RC transmitter.

* Can also be sent via MAVLink messages (RC_CHANNELS_OVERRIDE).

**Important channels**:

* Channel 1 (Roll)

* Channel 2 (Pitch)

* Channel 3 (Throttle) ← critical for altitude

* Channel 4 (Yaw)

### Throttle and LOITER

LOITER mode requires a valid throttle input.Without it, the drone will descend and disarm in SITL.

**MAVProxy RC Commands**

* ```rc 3 1500``` → sets throttle to mid-stick (hover)

* ```rc all 1500``` → sets all channels to mid-stick

**MAVLink RC Override**

```message RC_CHANNELS_OVERRIDE 0 0 1500 1500 1500 1500 0 0 0 0```

Allows fine-grained control of each RC channel via MAVLink.

## MAVProxy Workflow (Safe Sequence)

### 1. Set flight mode to GUIDED
mode GUIDED

### 2. Arm the motors
arm throttle

### 3. Takeoff to X meters
takeoff 20

### 4. Set throttle to mid-stick to simulate RC input
rc 3 1500

### 5. (Optional) Fly to a guided coordinate
guided <lat> <lon> <alt>

###  6. Switch to LOITER mode
mode LOITER

###  7. (Optional) Release RC override if autopilot is controlling the drone
rc clear

### 8. (Optional) Land safely
mode LAND

## RC Override Examples (MAVLink)

### Example MAVProxy/SITL RC Override Commands

| Command | Description |
|---------|-------------|
| `message RC_CHANNELS_OVERRIDE 0 0 1500 1500 1500 1500 0 0 0 0` | Set channels 1–4 to 1500 |
| `message RC_CHANNELS_OVERRIDE 0 0 1800 1500 1500 1500 0 0 0 0` | Set ch1 (roll) to 1800 (e.g., roll right) |
| `message RC_CHANNELS_OVERRIDE 0 0 1500 1200 1500 1500 0 0 0 0` | Set ch2 (pitch) to 1200 (e.g., pitch forward) |
| `message RC_CHANNELS_OVERRIDE 0 0 1500 1500 1800 1500 0 0 0 0` | Set ch3 (throttle) to 1800 (e.g., climb) |
| `message RC_CHANNELS_OVERRIDE 0 0 1500 1500 1500 1800 0 0 0 0` | Set ch4 (yaw) to 1800 (e.g., rotate clockwise) |
| `message RC_CHANNELS_OVERRIDE 0 0 0 0 0 1800 0 0 0 0` | Set ch4 (yaw) to 1800, all other channels from normal RC |
| `message RC_CHANNELS_OVERRIDE 0 0 0 0 0 0 0 0 1800 0` | Set ch7 to 1800, all other channels from normal RC |
| `message RC_CHANNELS_OVERRIDE 0 0 65535 65535 65535 65535 65535 65535 1800 65535` | Set ch7 to 1800, all other channels unchanged |


### Common Issues

#### Altitude drops and motors disarm when switching to LOITER

**Cause**: throttle channel not overridden → autopilot assumes 0 PWM.

**Fix**: rc 3 1500 before switching to LOITER.

### ADDITIONAL NOTES
* For more information on MAVLink RC Input, see the [ArduPilot MAVLink RC Input Documentation](https://ardupilot.org/dev/docs/mavlink-rcinput.html).

* For MAVProxy system commands, see the [MAVProxy System Configuration Guide](https://ardupilot.org/mavproxy/docs/uav_configuration/system.html).
