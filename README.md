# **Proposal: System-Wide Handling of Stale Sensor Data in Home Assistant**

## **Problem Statement**
Home Assistant currently **does not handle stale sensor data consistently** across integrations. If a sensor stops reporting, **Home Assistant continues displaying the last known value**, even though the data may be outdated. This can lead to **critical safety issues**, particularly in scenarios such as:
- **Temperature-based automations** (e.g., controlling heaters in nurseries, pet enclosures, medical use cases).
- **Environmental monitoring** (e.g., CO2, smoke detection, water leak sensors).
- **Health-related tracking** (e.g., presence/movement detection for elderly care).

There is no **system-wide mechanism** to mark sensor data as **stale or unavailable** when updates stop. Some integrations handle this at their own level, but many do not. Users currently rely on **custom automations or template sensors** to mitigate this issue, but this should be **a core feature, not a workaround**.

Additionally, **browser-based dashboards present a unique challenge** in that stale data may continue to be displayed **even if the backend is no longer receiving updates**. This can lead users to unknowingly act on outdated information. Unlike **rich native applications**, web-based interfaces **do not inherently manage state internally**, making real-time UI updates more vulnerable to failures in WebSockets, polling mechanisms, or browser execution limits.

---

## **🚀 Proposed Solution: Standardized Stale Data Handling Across Home Assistant**
### **🔹 1️⃣ Ensure `last_reported` is a Standard Attribute**
- This should be **mandatory** for all integrations that update sensor values.
- `last_reported` should update **whenever new data is received**, even if the state remains the same.

#### **Example of `last_reported` Implementation**
```yaml
sensor.shelly_ht_temperature:
  state: "74.0"
  last_changed: "2025-03-06T12:30:00Z"
  last_updated: "2025-03-06T12:45:00Z"
  last_reported: "2025-03-06T12:45:00Z"  # Consistently tracks last received update
```

---

### **🔹 2️⃣ Introduce a `stale_after` Attribute**
A new **optional** `stale_after` setting should be **introduced across all sensors**, allowing users to define an **acceptable data freshness threshold**.

- **If the sensor does not update within `stale_after` seconds, it is automatically marked as `unavailable`.**
- Users **no longer need to write custom automations** to handle stale sensors.

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

### **🔹 3️⃣ Implement UI-Level Fixes for Stale Data in Dashboards**

#### **Problem:**
- **Some dashboards fail to update sensor states until the page is refreshed manually.**
- **If a sensor transitions to `unavailable`, the UI may still display the last known value, misleading users.**

#### **Solution:**
✅ **Force a UI refresh when a sensor transitions to `unavailable`.**  
✅ **Ensure real-time updates in Lovelace, avoiding stale UI elements.**  
✅ **Provide a visual indicator for sensors using a fallback value.**

🔹 **Example UI Setting:**
```yaml
refresh_ui_on_stale: true  # Forces UI refresh if a sensor hasn't updated
stale_warning_threshold: 300  # Show warning banner if last update > 5 min ago
```

🔹 **If data updates stop, display a banner:**  
🚨 **“Warning: UI Data May Be Outdated”**  

🔹 **Expose `used_sensor` as an attribute in UI elements**
```yaml
type: entity
entity: sensor.cubby_temperature
attributes:
  - used_sensor
  - last_reported
color: >
  {% if is_state_attr('sensor.cubby_temperature', 'used_sensor', 'fallback') %}
    red
  {% else %}
    green
  {% endif %}
```

✅ **Enable an "Auto-Refresh Stale UI Data" Option**
- If a WebSocket connection **drops silently**, Home Assistant should **auto-reconnect instead of waiting for user action**.
- If the UI hasn’t received new data in **X seconds**, it **forces a refresh**.

🔹 **Example Auto-Refresh Configuration:**
```yaml
auto_reconnect_websockets: true  # Reconnect automatically on WebSocket drop
force_ui_refresh_on_stale: 300  # Hard refresh if UI hasn’t received updates in 5 min
```

---

## **🚀 Additional Benefits of a Native UI for Home Assistant**
Beyond improving stale data handling, a **native Home Assistant frontend** would enable **a level of UI sophistication that is difficult to achieve in a browser-based environment**.

### **🔹 Why a Native UI Enhances the Home Assistant Experience**
✅ **True WYSIWYG Dashboard Design:** Drag-and-drop UI components, live preview of automations, real-time visual updates.
✅ **Advanced Visual Elements:** Smooth animations, vector-based scalable UIs, layered design capabilities.
✅ **High-Speed Responsiveness:** No reliance on browser polling mechanisms, ensuring real-time updates.
✅ **Offline Functionality:** Retains local state and caches data for seamless operation during network interruptions.
✅ **Superior Layout Management:** Automatic resizing, docking, and grid-based design for a clean and flexible interface.

### **🔹 Viable Tools for Development**
| **Development Tool** | **License** | **Key Strengths** |
|---------------------|------------|------------------|
| **Delphi Community Edition** | Free (Community) | Modern, rapid UI design, full design-time behaviors |
| **Visual Studio Community (.NET MAUI, WPF, WinForms)** | Free (Community) | Strong Windows-native support, modern .NET framework |
| **Lazarus + Free Pascal** | Open Source (GPL/LGPL) | Delphi-compatible, cross-platform, aligns with Home Assistant’s open-source principles |

This native UI initiative would complement **Home Assistant’s web-based dashboards** by providing **a more powerful and flexible interface for high-performance use cases**.

---

With these additions, Home Assistant will **greatly improve stale data handling across both automation and UI layers**, while also exploring **a more sophisticated native UI for advanced dashboard experiences**. 🚀

