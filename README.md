# Automated Smart Sprinkler System


---  

### **Automated Smart Sprinkler System**  

Our solution is an efficient and automated sprinkler system designed to optimize water usage through intelligent irrigation. The system integrates real-time data from **soil moisture sensors** and **weather APIs**, ensuring precise water distribution based on environmental conditions.  

#### **Key Features & Architecture:**  

1. **Modular Grid-Based Irrigation:**  
   - Fields are divided into **modular grids**, each managed by an **ESP8266** module.  
   - Each grid communicates via **multiple MQTT topics** for seamless data transmission.  

2. **Centralized Control & Communication:**  
   - The **ESP32** serves as the main control unit, receiving sensor data from multiple ESP8266 nodes.  
   - The ESP32 then forwards this data to the **VSD Squadron Mini RISC-V controller**, which processes the information and sends optimized irrigation commands back to the ESP32.  

3. **Real-Time Monitoring & Automation:**  
   - **Weather data integration** allows dynamic adjustments based on forecasts.  
   - **Soil moisture sensors** provide live feedback to prevent over- or under-watering.  
   - **Automated irrigation scheduling** ensures efficient water distribution.  

4. **Remote Access via Mobile App:**  
   - Users can **monitor and control** the entire system remotely.  
   - The app provides **real-time data visualization**, manual overrides, and notifications.
  

### **Technical Architecture & Requirements for the Automated Smart Sprinkler System**  

## **1. System Architecture**  

The automated sprinkler system consists of multiple interconnected components for efficient water management and remote monitoring.  

### **A. Hardware Architecture**  
#### **1. Field-Level Nodes (ESP8266)**
   - Each **ESP8266** module manages a specific **modular grid**.  
   - Collects data from **soil moisture sensors** and transmits it via **MQTT**.  
   - Controls local sprinkler valves based on commands from the central ESP32.  

#### **2. Central Control Unit (ESP32)**
   - Acts as the **main controller**, handling communication with multiple ESP8266 nodes.  
   - Aggregates sensor data and sends it to the **VSD Squadron Mini RISC-V Controller**.  
   - Receives irrigation commands from VSD and relays them to the respective ESP8266 nodes.  

#### **3. VSD Squadron Mini RISC-V Controller**
   - Processes real-time **sensor data** and **weather API inputs**.  
   - Optimizes irrigation schedules and **sends control commands** to the ESP32.  
   - Ensures efficient water distribution by adjusting valve timing.  

#### **4. Cloud & Mobile App Integration**
   - **MQTT Broker** (e.g., Mosquitto, AWS IoT, or EMQX) facilitates communication.  
   - Weather data fetched from an **external API** (e.g., OpenWeather, NOAA).  
   - Mobile app for **remote monitoring, scheduling, and manual control**.  

### **B. Communication Flow**
1. **ESP8266 nodes** → Send sensor data via **MQTT topics**.  
2. **ESP32** → Collects data and forwards it to **VSD Squadron Mini**.  
3. **VSD Controller** → Processes data, optimizes irrigation, and sends commands to ESP32.  
4. **ESP32** → Relays commands to **ESP8266 nodes**, activating/deactivating sprinklers.  
5. **Mobile App** → Fetches real-time data from ESP32 and provides user control options.  

---

## **2. System Requirements**  

### **A. Hardware Requirements**
- **Microcontrollers:**  
  - ESP8266 (for field-level communication)  
  - ESP32 (as the main control unit)  
  - VSD Squadron Mini RISC-V (for processing and decision-making)  

- **Sensors & Actuators:**  
  - Soil Moisture Sensors (Capacitive or Resistive)  
  - Temperature & Humidity Sensors (DHT22, SHT30)  
  - Weather API integration for external data  
  - Relay Modules for valve control  

- **Power Supply & Communication:**  
  - 5V & 3.3V DC power sources  
  - WiFi for MQTT-based communication  

---

### **B. Software Requirements**
- **Firmware Development:**  
  - ESP8266 (Arduino IDE)  
  - ESP32 (Arduino IDE)  
  - VSD Squadron Mini (Embedded C)  

- **Networking & Communication:**  
  - MQTT Protocol (Mosquitto)  
  - WebSockets for mobile app communication  

- **Cloud & Mobile Integration:**  
  - Firebase/AWS for database storage  
  - Android/iOS mobile app   

---

### **C. Functional Requirements**
1. **Real-time Monitoring & Data Collection**  
   - Collect sensor readings at **regular intervals**.  
   - Store & process data in **VSD Squadron Mini Controller**.  

2. **Irrigation Control & Optimization**  
   - Use soil moisture thresholds & weather data for decision-making.  
   - Optimize water distribution using predictive analytics.  

3. **Remote Access & User Control**  
   - Provide mobile app dashboard for **monitoring & manual control**.  
   - Send alerts/notifications for system faults or extreme conditions.  

4. **Reliability & Scalability**  
   - Support multiple modular grids with seamless MQTT communication.  
   - Ensure robust error handling & fail-safe mechanisms.
  

---


##  Goals and Objectives 
   - Optimize water usage in agricultural fields by **30%**.  
   - Automate irrigation based on **real-time soil moisture** and **weather conditions**.  
   - Provide a **user-friendly mobile app** for monitoring and control.  
   - Ensure **scalability** and adaptability for different field sizes and conditions.  

##  Target Audience
  -  **Farmers** in water-scarce regions.  
  - **Agricultural organizations** aiming to improve irrigation efficiency.  
  -  **Environmental agencies** promoting sustainable water usage.

Progress link
----
[Product progress](https://drive.google.com/drive/folders/1lNDfcyyO_2TOxpG34E3Xn3UyG2EeoMmQ?usp=drive_link)



