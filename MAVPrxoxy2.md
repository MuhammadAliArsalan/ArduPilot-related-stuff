## 1. What is Geofencing in ArduPilot?

Geofencing is a safety feature that restricts where a drone or vehicle can fly. It creates virtual “fences” (boundaries) around areas so the vehicle cannot fly outside a defined region.

It can include:

* Maximum altitude (how high it can go)
* Minimum altitude (how low it can go)
* Circular or polygon areas

## 2. Using MAVProxy for Geofencing

MAVProxy is a command-line ground control software for ArduPilot. You can control and monitor drones through it.

You start MAVProxy with:

```mavproxy.py --master=127.0.0.1:14550 --console --map```
* ```--console``` gives you a command interface
* ```--map``` shows a map of the area

## 3. Enabling/Disabling Fences

Enable fences by using any one of the following cmds

```param set FENCE_ENABLE 1``` OR ```fence enable```


Disable fences:

```param set FENCE_ENABLE 0```
OR
```fence disable```


## 4. Parameter Modification Example

You can check and chang a parameter:
Run the following commands sequentially
* ```param show FENCE_ALT_MAX```
Output: 100.0
* ```param set FENCE_ALT_MAX 50```
* ```param show FENCE_ALT_MAX```
Output: 50.0


--> Meaning:
You reduced the drone’s maximum altitude limit from 100m to 50m.
Now, if it tries to go higher, autopilot will trigger the fence action (usually RTL).

## 🧭 Geofence Parameter Overview

| Parameter            | Meaning                                  | Current Value  | Explanation                                                                                                                                      |
| -------------------- | ---------------------------------------- | -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **FENCE_ACTION**     | What to do if the fence is breached      | `1`            | ✅ Means **RTL (Return-To-Launch)** — when a fence is breached, drone will return home automatically.                                             |
| **FENCE_ALT_MAX**    | Maximum altitude limit                   | `50.0` meters  | ✅ Drone cannot fly above **50m** — crossing this will trigger a fence breach and RTL.                                                            |
| **FENCE_ALT_MIN**    | Minimum altitude limit                   | `-10.0` meters | ⚠ Negative means **no lower fence** (below home level allowed). Typically should be `0` or a positive value if you want to enforce min altitude. |
| **FENCE_AUTOENABLE** | Automatically enable fence after takeoff | `0`            | ❌ Fence will **not auto-enable** — you must manually enable it using `fence enable` or `param set FENCE_ENABLE 1`.                               |
| **FENCE_ENABLE**     | Main switch for geofencing               | `0`            | ❌ Geofence is **currently disabled**. The drone won’t enforce any of the limits until you enable it.                                             |
| **FENCE_MARGIN**     | Safety buffer around the fence boundary  | `2.0` meters   | ✅ This gives a **2-meter grace zone** beyond the fence before triggering a breach — prevents false triggers.                                     |
| **FENCE_OPTIONS**    | Additional fence options (bitmask)       | `0`            | No special options enabled (e.g., pre-arming checks or exclusion zones).                                                                         |
| **FENCE_RADIUS**     | Circular fence radius (horizontal limit) | `150.0` meters | ✅ Drone can’t go beyond **150m** from home position (once fence is enabled).                                                                     |
| **FENCE_TOTAL**      | Number of polygon fence points defined   | `0`            | ✅ No custom polygon fences are currently loaded — only the circular fence is active.                                                             |
| **FENCE_TYPE**       | Which fences are active (bitmask)        | `7`            | ✅ Includes **Max Altitude (1) + Circle (2) + Polygon (4)**. This is the **default combination** — no min altitude fence.                         |
