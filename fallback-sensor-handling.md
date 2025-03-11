# **Appendix B: Fallback Sensor Handling**

## **Introduction**
When a primary sensor becomes unavailable, it is often useful to have a **fallback sensor** that can be used to maintain system functionality. This is especially useful for **critical automations** such as heating control, environmental monitoring, or security alerts.

## **🔹 Implementing Fallback Sensors in Home Assistant**
Home Assistant should support the ability to **define a fallback sensor** for any entity that provides numeric data.

### **Example Configuration**
```yaml
sensor:
  - platform: fallback
    name: "Cubby Temperature"
    primary: sensor.cubby_floor_temp
    fallback: sensor.cubby_ceiling_temp
    offset_if_primary_fails: +4  # If floor sensor fails, estimate using ceiling -4°F
    offset_if_fallback_fails: -4  # If ceiling sensor fails, estimate using floor +4°F
```

### **Behavior:**
✅ If **sensor.cubby_floor_temp** is working, use its value.  
✅ If **sensor.cubby_floor_temp fails**, use **sensor.cubby_ceiling_temp - 4°F**.  
✅ If **both fail, mark as `unavailable`**.

---

## **🔹 Handling Fallback Sensors in Automations**

### **1️⃣ Automatically Switching to a Fallback Sensor in Automations**
```yaml
- alias: Heater Control with Fallback Sensor
  trigger:
    - platform: numeric_state
      entity_id: sensor.cubby_temperature
      below: 68
  condition:
    - condition: template
      value_template: "{{ states('sensor.cubby_temperature') | float(default=72) > 60 }}"
  action:
    - service: climate.set_temperature
      data:
        entity_id: climate.cubby_heater
        temperature: 72
```
✅ Ensures that if **sensor.cubby_temperature fails**, a **fallback temperature is used**.

### **2️⃣ Notifying Users When a Fallback is in Use**
```yaml
- alias: Notify on Sensor Fallback
  trigger:
    - platform: state
      entity_id: sensor.cubby_temperature
      attribute: used_sensor
      to: "fallback"
  action:
    - service: notify.mobile_app
      data:
        title: "Sensor Fallback Alert"
        message: "Primary sensor failed. Using fallback sensor."
```
✅ **Notifies users when the system is relying on a backup sensor**.

---

## **Conclusion**
- **Fallback sensors prevent automation failures** when primary sensors become unavailable.
- **Offset adjustments** allow more accurate fallback values.
- **Alerts can notify users when fallbacks are in use**.

These improvements help **Home Assistant remain reliable in critical monitoring scenarios**.

---

[⬅ Back to Main Proposal](README.md)
