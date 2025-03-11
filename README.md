# **Proposal: Enhancing Stale Data Handling in Home Assistant**

## **Introduction**
Home Assistant introduced the **`last_reported` attribute** in March 2024, which tracks the last time a state update was received, even if the state itself did not change. This is a **major improvement** for detecting stale data. However, additional enhancements are needed to **leverage `last_reported` effectively in automations, UI warnings, and fallback mechanisms**.

This proposal focuses on **three key areas**:
1. **Standardized `stale_after` handling** for marking entities as `unavailable` based on `last_reported`.
2. **UI-level enhancements** to provide clear stale data warnings and auto-refresh mechanisms.
3. **Fallback sensor handling and multi-sensor redundancy** for improved automation resilience.

---

## **🚀 Proposed Enhancements**
### **1️⃣ Standardized `stale_after` Handling for Sensors**
While `last_reported` tracks when data was last received, there is **no built-in mechanism** to mark a sensor as `unavailable` if no updates have arrived within a user-defined time.

**🔹 Proposed Feature: `stale_after` Attribute**
- Users should be able to specify a **timeout threshold** after which a sensor is considered `unavailable`.
- This would remove the need for complex template sensors and automations to track stale entities.

#### **Example Configuration in YAML**
```yaml
sensor:
  - platform: bluetooth
    name: "Shelly H&T Temperature"
    stale_after: 300  # Mark as unavailable if no update in 300 seconds (5 minutes)
```

#### **Behavior Table**
| Time Since Last Update | Sensor State |
|-----------------------|-------------|
| < 5 minutes          | "74.0°F" (Valid) |
| > 5 minutes (`stale_after: 300`) | `unavailable` |
| No `stale_after` set | Continues showing "74.0°F" with `last_reported` attribute |

---

### **2️⃣ UI Enhancements for Stale Data Warnings**
Currently, Home Assistant dashboards **do not visually indicate stale data** unless users manually inspect timestamps. This can lead to **unintended automation failures** or decisions based on outdated information.

**🔹 Proposed Enhancements:**
✅ Add a **warning banner** if sensor data has not updated in a user-defined time.  
✅ Optionally **auto-refresh dashboards** when stale sensors are detected.  
✅ Show **color-coded indicators** for sensors that are still reporting old data.

#### **Example UI Configuration**
```yaml
refresh_ui_on_stale: true  # Forces UI refresh if a sensor hasn't updated
stale_warning_threshold: 300  # Show warning banner if last update > 5 min
```

#### **Example Warning Message**
🚨 **Warning: Sensor data may be outdated!**  
_(Last update: 7 minutes ago)_

---

### **3️⃣ Fallback Sensor Handling**
If a sensor becomes unavailable, automations should have **an option to switch to a fallback sensor** to prevent failures.

#### **Example Configuration**
```yaml
sensor:
  - platform: fallback
    name: "Cubby Temperature"
    primary: sensor.cubby_floor_temp
    fallback: sensor.cubby_ceiling_temp
    offset_if_primary_fails: +4  # Adjust for known temperature difference
```

✅ **If the floor sensor fails, the system switches to the ceiling sensor (-4°F adjustment).**  
✅ **If both fail, the entity is marked as `unavailable`.**

---

### **4️⃣ Multi-Sensor Redundancy & Voting Algorithms**
For **critical sensors** (temperature, motion, air quality), a **multi-sensor voting algorithm** can improve reliability by discarding outliers.

#### **Example: Majority Vote for Motion Detection**
```yaml
binary_sensor:
  - platform: motion_voting
    name: "Verified Motion Detector"
    sensors:
      - binary_sensor.motion_1
      - binary_sensor.motion_2
      - binary_sensor.motion_3
    voting_threshold: 2  # At least 2 of 3 must detect motion
```

✅ **Prevents false positives from malfunctioning motion sensors.**

#### **Example: Outlier Rejection for Temperature Sensors**
```yaml
sensor:
  - platform: multi_sensor_voting
    name: "Room Temperature"
    sensors:
      - sensor.temp_sensor_1
      - sensor.temp_sensor_2
      - sensor.temp_sensor_3
    method: "median"
    outlier_threshold: 3  # Ignore values deviating by 3+ degrees from median
```

✅ **Eliminates faulty sensor readings by using median-based filtering.**

---

## **📑 Appendices: Technical Considerations & Additional Enhancements**
For more details on specific areas of this proposal, refer to the following appendices:

- [A. Node-RED & Automation Considerations](node-red-considerations.md)
- [B. Fallback Sensor Handling](fallback-sensor-handling.md)
- [C. Multi-Sensor Redundancy & Voting Algorithms](multi-sensor-redundancy.md)
- [D. Browser-Based UI Risks & Local State Management](browser-ui-risks.md)
- [E. Aging in Place & Home-Based Virtual Care](aging-in-place.md)
- [F. AI-Enhanced Sensor Handling & Anomaly Detection](ai-enhanced-sensor-handling.md)
- [G. Smart Heater Safety & Automation Failures](heater-safety-lessons.md)
- [H: Wired Sensors for Smart Home Resilience & Safety](wired-sensor-resilience.md)
---

## **🚀 Conclusion & Next Steps**
By implementing `stale_after`, **UI-level warnings**, and **sensor redundancy mechanisms**, Home Assistant can further improve **sensor reliability and stale data visibility**. While `last_reported` was a significant step forward, these additions would help users **better manage stale data in automations and dashboards.**

---

With these additions, Home Assistant will **greatly improve stale data handling across both automation and UI layers**, while also exploring **a more sophisticated native UI for advanced dashboard experiences**. 🚀

