# **Appendix D: Browser-Based UI Risks & Local State Management**

## **Introduction**
Home Assistant now provides `last_reported`, which ensures that sensor updates are properly timestamped. However, **this data is not yet well-integrated into the frontend**, meaning users may still unknowingly act on stale values. Additionally, browser-based dashboards remain vulnerable to **stale UI issues, silent connection failures, and execution limitations**.

---

## **🔹 Key Risks of Browser-Based UI**

### **1️⃣ Stale UI Data Not Properly Surfaced**
✅ **Problem:** Although `last_reported` exists, **Home Assistant dashboards do not automatically highlight stale sensors**.  
✅ **Why It Matters:** Users may rely on outdated readings without realizing it.  
✅ **Solution:** Introduce a **stale data warning indicator** based on `last_reported`.  

### **2️⃣ WebSockets & Connection Loss**
✅ **Problem:** If a WebSocket disconnects, **the UI may freeze at an old state without users noticing**.  
✅ **Why It Matters:** The frontend may show incorrect device statuses until the page is manually refreshed.  
✅ **Solution:** Implement **automatic WebSocket reconnection and stale data alerts**.  

### **3️⃣ Browser Execution Limits**
✅ **Problem:** Browsers **pause JavaScript execution** in background tabs, potentially delaying UI updates.  
✅ **Why It Matters:** A user viewing an inactive Home Assistant tab **may see outdated data** upon returning.  
✅ **Solution:** Periodically **force refresh UI elements** if no update has been received for a set time.  

---

## **🔹 Proposed Enhancements**

### **1️⃣ Display Stale Data Warnings in the Dashboard**
✅ If a sensor’s `last_reported` value is older than a configured threshold, show a warning.  
✅ Example:  
```yaml
stale_warning_threshold: 300  # Show banner if last update > 5 minutes
```
✅ **Warning Banner Example:**
> 🚨 **Warning: Sensor data may be outdated!**   
> _(Last update: 7 minutes ago)_

### **2️⃣ Improve WebSocket Reconnect Handling**
✅ Automatically **detect and recover WebSocket disconnections**.  
✅ Example:  
```yaml  
auto_reconnect_websockets: true  # Automatically attempts reconnection  
```  

### **3️⃣ Explore a Native UI Alternative**
✅ **Why?**  
- Eliminates browser execution limits.  
- Maintains persistent local state across sessions.  
- Ensures **faster and more reliable UI updates**.  
✅ **Potential Development Platforms:**  
| **Tool** | **License** | **Key Strengths** |  
|---------|------------|------------------|  
| **Delphi Community Edition** | Free (Community) | Modern, full design-time behaviors |  
| **Visual Studio (.NET MAUI, WPF)** | Free (Community) | Strong Windows-native support |  
| **Lazarus + Free Pascal** | Open Source | Delphi-compatible, cross-platform |  

---

## **Conclusion**
- **Web-based dashboards still need better stale data visibility** despite `last_reported`.  
- **UI enhancements (stale warnings, WebSocket recovery) are necessary**.  
- **A native UI alternative would provide a more robust solution** for critical use cases.  

---

[⬅ Back to Main Proposal](README.md)
