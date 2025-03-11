# **Appendix H: Multi-Sensor Redundancy & Voting Algorithms**

## **Introduction**
For systems that rely on multiple sensors (e.g., temperature monitoring, motion detection, air quality), data inconsistencies between sensors can occur. To improve reliability, a **multi-sensor redundancy strategy** using a **voting algorithm** can discard outliers and generate a more accurate reading.

## **🔹 Implementing Multi-Sensor Redundancy in Home Assistant**

### **Example: Weighted Sensor Averaging with Outlier Rejection**
```yaml
sensor:
  - platform: multi_sensor_voting
    name: "Room Temperature"
    sensors:
      - sensor.temp_sensor_1
      - sensor.temp_sensor_2
      - sensor.temp_sensor_3
    method: "median"  # Options: average, median, majority_vote
    outlier_threshold: 3  # Ignore values deviating by 3+ degrees from the median
```

### **Behavior:**
✅ Collects values from **three sensors**.  
✅ Uses the **median method** to discard outliers.  
✅ Ignores sensors reporting temperatures **3°F higher or lower than the median**.  

---

## **🔹 Using Majority Voting for Critical Sensors**
Some automation systems, particularly **security or motion detection**, can use a **majority voting** method to determine if an event truly occurred.

### **Example: Motion Sensor Voting to Prevent False Alarms**
```yaml
binary_sensor:
  - platform: motion_voting
    name: "Verified Motion Detector"
    sensors:
      - binary_sensor.motion_1
      - binary_sensor.motion_2
      - binary_sensor.motion_3
    voting_threshold: 2  # At least 2 of 3 must report motion
```

### **Behavior:**
✅ Requires at least **2 out of 3 motion sensors** to report motion **before triggering an alarm**.   
✅ Helps **prevent false triggers** from a single malfunctioning sensor.  

---

## **🔹 Automations Using Multi-Sensor Redundancy**

### **1️⃣ Ensuring a Reliable Temperature Reading**
```yaml
- alias: Reliable Temperature Control
  trigger:
    - platform: numeric_state
      entity_id: sensor.room_temperature
      below: 68
  action:
    - service: climate.set_temperature
      data:
        entity_id: climate.room_heater
        temperature: 72
```
✅ Uses the **aggregated value** from multiple sensors, preventing incorrect triggers due to a faulty sensor.

### **2️⃣ Sending Alerts When Sensor Discrepancies Are Detected**
```yaml
- alias: Alert on Sensor Discrepancy
  trigger:
    - platform: template
      value_template: >
        {% set sensors = [states('sensor.temp_sensor_1') | float, states('sensor.temp_sensor_2') | float, states('sensor.temp_sensor_3') | float] %}
        {{ max(sensors) - min(sensors) > 5 }}
  action:
    - service: notify.mobile_app
      data:
        title: "Sensor Discrepancy Alert"
        message: "Temperature sensors have a 5+ degree difference!"
```
✅ Detects when one or more sensors **report drastically different values**, signaling a possible failure.

---

## **Conclusion**
- **Multi-sensor redundancy prevents failures caused by single sensor malfunctions**.  
- **Voting algorithms provide more reliable automation triggers**.  
- **Outlier rejection ensures automation logic is based on realistic values**.  
- **Alerts help users identify faulty sensors before they cause major issues**.  

These techniques significantly improve **Home Assistant’s ability to handle multiple sensor inputs reliably**.

---

[⬅ Back to Main Proposal](README.md)
