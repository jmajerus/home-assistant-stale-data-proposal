# **Appendix G: Smart Heater Safety & Automation Failures**

## **Introduction**
Space heaters are among the most **dangerous household appliances**, responsible for a significant percentage of home fires and fatalities. According to the **National Fire Protection Association (NFPA),** space heaters account for **43% of all home heating fires** and **85% of heating-related deaths** annually. These statistics highlight the **critical need for proactive safety measures in smart home automation.**

Recent **real-world heater failures** underscore why Home Assistant and other smart home platforms must **go beyond basic automation** and actively **detect, mitigate, and prevent dangerous heater-related scenarios.**

---

## **1️⃣ The Danger of Runaway Radiant Flux**
A **2004 letter to Underwriters Laboratories from the U.S. Consumer Product Safety Commission** ([link](https://www.cpsc.gov/s3fs-public/pdfs/blk_media_HeaterFeb04.pdf)) explains how certain heaters, particularly **radiant heaters**, can **overheat objects indefinitely** due to uncontrolled energy transfer. Unlike air heaters, radiant heaters do not stop heating an object once it reaches a stable temperature.

🚨 **Critical Safety Warning from the CPSC Letter:**
> "Unlike air heaters, radiant heaters have the possibility of raising the surface temperature
of an object in the path of the radiant flux very high. With a heating element temperature of
hundreds of degrees Celsius, radiant heaters will continue to transfer energy to objects after they
have warmed. Cooling mechanisms (conduction, convection, etc.) are required to keep the
object from continuing to heat indefinitely. For materials with low thermal conductivity or high
emissivity, there is the possibility of continued heating until ignition temperatures are reached."

💡 **Key Lessons for Smart Home Automation:**
- **AI-driven automation must recognize the risk of continued heating.** A heater that stays on too long or reaches unexpected temperatures should be flagged for immediate shutdown.
- **Temperature sensors should be positioned to detect overheating at the object level, not just ambient air.**
- **Failsafe mechanisms should cut power if an object’s temperature continues rising uncontrollably.**

---

## **2️⃣ Real-World Smart Heater Failures**

### **Govee Smart Heater Recall (2025) – Risk of Fire & Burn Injuries**  
In **January 2025**, the **CPSC announced a recall** of **GoveeLife and Govee Smart Electric Space Heaters** ([link](https://www.cpsc.gov/Recalls/2025/GoveeLife-and-Govee-Smart-Electric-Space-Heaters-Recalled-Due-to-Fire-and-Burn-Hazards-Imported-by-Govee)). 

🚨 **Key Failures:**
- **Internal wiring defects led to overheating, creating fire and burn hazards.**
- **Users reported flames and melting components before recalls were issued.**

💡 **Lessons for Smart Home Automation:**
- **Smart homes must monitor heating device behavior independently of the device's internal safety mechanisms.**
- **Temperature readings alone are insufficient; monitoring power usage spikes can help identify unsafe conditions.**
- **Emergency auto-shutoff should be triggered if excessive heat is detected, even if the heater itself does not report a fault.**

### **Atomi Smart Heater Recall (2024) – Unintended Activation & Fire Risk**  
In **December 2024**, the **Atomi Smart Heater** was recalled due to **fire and burn hazards** caused by **unexpected activation** ([link](https://www.cpsc.gov/Recalls/2024/Atomi-Recalls-Smart-Heaters-Due-to-Fire-and-Burn-Hazards)).

🚨 **Key Failures:**
- **A faulty Wi-Fi module could cause the heater to turn on unexpectedly.**
- **If left unattended, an activated heater could cause overheating and fire hazards.**

💡 **Lessons for Smart Home Automation:**
- **Smart automation must monitor unintended activations and alert users immediately.**
- **Power draw anomalies can indicate an unauthorized activation event.**
- **Home Assistant should allow users to configure conditions that automatically cut power to a heater if it turns on unexpectedly or remains on too long.**

### **The Broader Issue: Space Heater-Related Deaths Every Year**
Space heater fires are not rare, isolated incidents—they are a **leading cause of home fire deaths.** Most fatalities occur **when heaters are left unattended, placed too close to flammable materials, or suffer mechanical or electrical failures.**

🚨 **What This Means for Smart Home Automation:**
- **AI-powered automation must take an active role in safety monitoring.**
- **Smart homes should recognize and respond to anomalies such as unexpected activations, overheating, and power surges.**
- **Failsafe mechanisms should prevent a heater from staying on indefinitely due to automation or software failures.**
- **Users must be given clear alerts and warnings when heater-related anomalies are detected.**

---

## **3️⃣ Proposed Home Assistant Enhancements for Heating Safety**
To integrate these real-world lessons into **smart home automation**, Home Assistant could introduce the following features:

✅ **Fire Prevention Mode:** A user-configurable option that enables:
   - Strict power monitoring for heaters.
   - Auto-shutdown after a defined period.
   - Alerts for unusual power draw or unintended activation.

✅ **AI-Powered Overheat Detection:**
   - If a heater is active and a sensor detects an **unexpected temperature rise**, an emergency **shutdown command is issued.**
   - Uses machine learning to differentiate between normal and unsafe heating patterns.

✅ **Independent Failsafe System:**
   - Even if an automation fails, **power to the heater should cut off if predefined safety conditions are met.**
   - Cross-checking with occupancy and motion sensors before allowing extended use.

✅ **User Alerts & Emergency Notifications:**
   - If a space heater remains on longer than expected, **an urgent notification is sent to the user.**
   - Optionally escalate notifications to emergency contacts if a dangerous condition persists.

🚨 **By integrating these safety features, Home Assistant can help prevent future tragedies and ensure that smart heaters are truly smart—and safe.**

---

[⬅ Back to Main Proposal](README.md)
