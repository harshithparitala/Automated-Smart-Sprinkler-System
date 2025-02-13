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

Working Code
---

Central -Unit 
````
#include <ch32v00x.h>
#include <stdio.h>  
#include <string.h> 
#include <stdlib.h> 

#define LED_PIN GPIO_Pin_2   
#define LED_PORT GPIOD
#define RX_PIN GPIO_Pin_6    
#define TX_PIN GPIO_Pin_5    

void UART_Init(void);
void UART_SendString(const char *str);
void UART_SendChar(char c);
char UART_ReceiveChar(void);
void Blink_LED(void);
void Delay_ms(uint32_t ms);

#define BUFFER_SIZE 128  
char rxBuffer[BUFFER_SIZE];  
uint8_t rxIndex = 0;  

void setup() {
    SystemInit();
    UART_Init();

    RCC_APB2PeriphClockCmd(RCC_APB2Periph_GPIOD, ENABLE);

    GPIO_InitTypeDef GPIO_InitStructure;
    GPIO_InitStructure.GPIO_Pin = LED_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(LED_PORT, &GPIO_InitStructure);

    UART_SendString("VSD Mini Ready\r\n");
}

void loop() {
    char receivedChar = UART_ReceiveChar();

    if (receivedChar) {
        if (receivedChar == '\n' || rxIndex >= BUFFER_SIZE - 1) {  
            rxBuffer[rxIndex] = '\0';  
            Blink_LED();  

            char *token = strtok(rxBuffer, ","); 
            if (token && strcmp(token, "WEATHER") == 0) {  
                char *condition = strtok(NULL, ",");  
                char *temperature = strtok(NULL, ",");
                char *humidity = strtok(NULL, ",");
                char *windSpeed = strtok(NULL, ",");
                char *windDirection = strtok(NULL, ",");
                char *rainfall = strtok(NULL, ",");
                char *moistureLabel = strtok(NULL, ",");  
                char *moisture1 = strtok(NULL, ",");  
                char *moisture2 = strtok(NULL, ",");  

                if (condition && temperature && humidity && windSpeed && windDirection && rainfall &&
                    moistureLabel && moisture1 && moisture2) {

                    int tempVal = atoi(temperature);
                    int humidVal = atoi(humidity);
                    int rainVal = atoi(rainfall);
                    int moist1Val = atoi(moisture1);
                    int moist2Val = atoi(moisture2);

                    // Default: Pumps OFF
                    char pump1Command[5] = "OFF1";
                    char pump2Command[5] = "OFF2";

                    if (rainVal > 5) {
                        UART_SendString("INFO: Rain detected, all pumps OFF\r\n");
                    } else {
                        if (moist1Val < 54) {
                            strcpy(pump1Command, "ON1");
                        }
                        if (moist2Val < 54) {
                            strcpy(pump2Command, "ON2");
                        }
                    }

                    UART_SendString(pump1Command);
                    UART_SendString("\r\n");
                    UART_SendString(pump2Command);
                    UART_SendString("\r\n");

                    if (humidVal > 80 && tempVal < 30) {
                        UART_SendString("INFO: High humidity, reducing irrigation\r\n");
                    } else if (tempVal > 35) {
                        UART_SendString("INFO: High temperature, increasing irrigation\r\n");
                    }
                }
            }

            rxIndex = 0;
        } else {
            rxBuffer[rxIndex++] = receivedChar;  
        }
    }
}

void UART_Init(void) {
    RCC_APB2PeriphClockCmd(RCC_APB2Periph_USART1 | RCC_APB2Periph_GPIOD, ENABLE);

    GPIO_InitTypeDef GPIO_InitStructure;

    GPIO_InitStructure.GPIO_Pin = TX_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_AF_PP;
    GPIO_InitStructure.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    GPIO_InitStructure.GPIO_Pin = RX_PIN;
    GPIO_InitStructure.GPIO_Mode = GPIO_Mode_IN_FLOATING;
    GPIO_Init(GPIOD, &GPIO_InitStructure);

    USART_InitTypeDef USART_InitStructure;
    USART_InitStructure.USART_BaudRate = 9600;
    USART_InitStructure.USART_WordLength = USART_WordLength_8b;
    USART_InitStructure.USART_StopBits = USART_StopBits_1;
    USART_InitStructure.USART_Parity = USART_Parity_No;
    USART_InitStructure.USART_Mode = USART_Mode_Tx | USART_Mode_Rx;
    USART_Init(USART1, &USART_InitStructure);

    USART_Cmd(USART1, ENABLE);
}

void UART_SendString(const char *str) {
    while (*str) {
        UART_SendChar(*str++);
    }
}

void UART_SendChar(char c) {
    while (!(USART1->STATR & USART_STATR_TXE));  
    USART1->DATAR = c;
}

char UART_ReceiveChar(void) {
    if (USART1->STATR & USART_STATR_RXNE) {  
        return USART1->DATAR;  
    }
    return 0;  
}

void Blink_LED(void) {
    GPIO_WriteBit(LED_PORT, LED_PIN, Bit_SET);
    Delay_ms(200);
    GPIO_WriteBit(LED_PORT, LED_PIN, Bit_RESET);
}

void Delay_ms(uint32_t ms) {
    for (volatile uint32_t i = 0; i < ms * 8000; i++);
}
``````

Esp8266 (Grids)

````
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>

#define PUMP_PIN D1  
#define MOISTURE_SENSOR_PIN A0  // Moisture sensor connected to A0

const char* ssid = "8a";  
const char* password = "87654321";  
const char* mqtt_server = "test.mosquitto.org";  

WiFiClient espClient;
PubSubClient client(espClient);

void callback(char* topic, byte* payload, unsigned int length) {
    Serial.print("Message received on topic: ");
    Serial.println(topic);

    String message = "";
    for (int i = 0; i < length; i++) {
        message += (char)payload[i];
    }

    Serial.print("Command received: ");
    Serial.println(message);

    if (message == "ON1") {
        digitalWrite(PUMP_PIN, HIGH);
        Serial.println("Pump ON");
    } else if (message == "OFF1") {
        digitalWrite(PUMP_PIN, LOW);
        Serial.println("Pump OFF");
    }
}

// Function to reconnect to MQTT if disconnected
void reconnect() {
    while (!client.connected()) {
        Serial.print("Attempting MQTT connection...");
        if (client.connect("NodeMCU")) {
            Serial.println("Connected to MQTT broker");
            client.subscribe("pump/control/node1");  
            Serial.println("Subscribed to: pump/control/node1");
        } else {
            Serial.print("Failed, rc=");
            Serial.println(client.state());
            Serial.println(" Retrying in 5 seconds...");
            delay(5000);
        }
    }
}

// Function to read moisture sensor and publish to MQTT
void publishMoistureData() {
    int rawValue = analogRead(MOISTURE_SENSOR_PIN);
    
    // Convert raw value (0-1023) to moisture percentage (0-100%)
    int moisturePercentage = map(rawValue, 1023, 0, 0, 100);
    
    Serial.print("Moisture Level: ");
    Serial.print(moisturePercentage);
    Serial.println("%");

    // Create JSON payload
    DynamicJsonDocument doc(128);
    doc["sensor1"] = moisturePercentage;
    
    String jsonData;
    serializeJson(doc, jsonData);

    // Publish to MQTT
    client.publish("moisture/data", jsonData.c_str());
    Serial.println("Published to MQTT: " + jsonData);
}

void setup() {
    Serial.begin(115200);
    pinMode(PUMP_PIN, OUTPUT);
    digitalWrite(PUMP_PIN, LOW);  

    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }
    Serial.println("\nWiFi connected!");

    client.setServer(mqtt_server, 1883);
    client.setCallback(callback);
    reconnect();
}

void loop() {
    if (!client.connected()) {
        reconnect();
    }
    client.loop();

    // Publish moisture data every 5 seconds
    publishMoistureData();
    delay(5000);
}
`````

ESP-32 communicating with VSDSquadron Mini in cenral unit 
````
#include <ESP8266WiFi.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>

#define PUMP_PIN D1  
#define MOISTURE_SENSOR_PIN A0  // Moisture sensor connected to A0

const char* ssid = "8a";  
const char* password = "87654321";  
const char* mqtt_server = "test.mosquitto.org";  

WiFiClient espClient;
PubSubClient client(espClient);

void callback(char* topic, byte* payload, unsigned int length) {
    Serial.print("Message received on topic: ");
    Serial.println(topic);

    String message = "";
    for (int i = 0; i < length; i++) {
        message += (char)payload[i];
    }

    Serial.print("Command received: ");
    Serial.println(message);

    if (message == "ON1") {
        digitalWrite(PUMP_PIN, HIGH);
        Serial.println("Pump ON");
    } else if (message == "OFF1") {
        digitalWrite(PUMP_PIN, LOW);
        Serial.println("Pump OFF");
    }
}

// Function to reconnect to MQTT if disconnected
void reconnect() {
    while (!client.connected()) {
        Serial.print("Attempting MQTT connection...");
        if (client.connect("NodeMCU")) {
            Serial.println("Connected to MQTT broker");
            client.subscribe("pump/control/node1");  
            Serial.println("Subscribed to: pump/control/node1");
        } else {
            Serial.print("Failed, rc=");
            Serial.println(client.state());
            Serial.println(" Retrying in 5 seconds...");
            delay(5000);
        }
    }
}

// Function to read moisture sensor and publish to MQTT
void publishMoistureData() {
    int rawValue = analogRead(MOISTURE_SENSOR_PIN);
    
    // Convert raw value (0-1023) to moisture percentage (0-100%)
    int moisturePercentage = map(rawValue, 1023, 0, 0, 100);
    
    Serial.print("Moisture Level: ");
    Serial.print(moisturePercentage);
    Serial.println("%");

    // Create JSON payload
    DynamicJsonDocument doc(128);
    doc["sensor1"] = moisturePercentage;
    
    String jsonData;
    serializeJson(doc, jsonData);

    // Publish to MQTT
    client.publish("moisture/data", jsonData.c_str());
    Serial.println("Published to MQTT: " + jsonData);
}

void setup() {
    Serial.begin(115200);
    pinMode(PUMP_PIN, OUTPUT);
    digitalWrite(PUMP_PIN, LOW);  

    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) {
        delay(500);
        Serial.print(".");
    }
    Serial.println("\nWiFi connected!");

    client.setServer(mqtt_server, 1883);
    client.setCallback(callback);
    reconnect();
}

void loop() {
    if (!client.connected()) {
        reconnect();
    }
    client.loop();

    // Publish moisture data every 5 seconds
    publishMoistureData();
    delay(5000);
}
`````
Working Description
---


## *Project Overview: Efficient Sprinkler System with Cloud & Autonomous Control*

### *System Components*
1. *ESP32*: 
   - Acts as the *communication hub* between modules, RISC-V, and the cloud.
   - Retrieves *real-time weather data* from a weather API (such as OpenWeather).
   - Handles all communication between the various *nodes* (sensor modules, RISC-V, cloud) via *MQTT*.
   - Publishes and subscribes to relevant MQTT topics for controlling pumps, retrieving sensor data, and sending commands.

2. *RISC-V (VSDSquadron)*:
   - Functions as the *brain* of the system, processing sensor data and weather data.
   - Runs a basic *Machine Learning algorithm* to decide whether to turn on or off specific sprinkler pumps based on moisture levels and weather conditions (e.g., rainy day, high moisture → turn off).
   - Receives data from the ESP32 via UART and sends back optimized control commands for pumps.

3. *Sensor Modules (Water Pumps, Moisture Sensors)*:
   - *Moisture Sensors*: These modules send moisture readings to the ESP32 every 10 seconds. The readings indicate soil moisture levels, which help in decision-making.
   - *Water Pumps (DC motors)*: Connected to relays, these modules are turned on or off based on the decision from the RISC-V processor.
   - *Relays*: These control the activation of the water pumps.

4. *Mobile App (MIT App Inventor)*:
   - Visualizes the data in *real-time* by fetching updates from the cloud (Firebase).
   - *Displays moisture levels* of each sensor module (quadrants of a 2x2 grid).
   - *Manual control option* allows the user to override the automated system and turn pumps on or off for specific modules.
   - *Status display* indicates whether the water pump is on, off, or manually controlled.

5. *Cloud (Firebase)*:
   - Acts as the data storage and real-time data sync platform between the ESP32 and the mobile app.
   - Stores *moisture sensor data* and *sprinkler control statuses*.
   - Allows real-time data synchronization for *live updates* on the mobile app.

---

## *Project Flow*

### *1. Data Collection and Communication*
- *Moisture Data*: The moisture sensors send data to the ESP32 every 10 seconds. This data includes the current moisture level of each field square.
- *Weather Data: ESP32 fetches **real-time weather data* from a weather API, such as OpenWeather, to determine whether conditions like rainfall, sunny weather, or cloudy conditions are present.
- The ESP32 communicates with the *RISC-V* board via *UART*, sending weather data and moisture sensor values for processing.

### *2. Decision Making on the RISC-V Board*
- *Machine Learning Algorithm*: The RISC-V board processes the received weather and moisture data. It runs an algorithm (simple rule-based or ML-based) to decide whether the irrigation system should be activated.
  - *Example Decision Logic*:
    - If *rainy* and moisture levels are already *high, the pump is **turned off*.
    - If *sunny* or *cloudy, and moisture is **low, the pump is **turned on*.
- Once the RISC-V makes the decision, it sends a *command* back to the ESP32 to turn on or off the respective pump.

### *3. Cloud Communication and Real-Time Updates*
- ESP32 publishes the *control commands* to an *MQTT topic* and stores the *moisture readings* and *pump statuses* in *Firebase*.
- The mobile app, using *Firebase* as the backend, *fetches real-time data* and displays:
  - *Moisture levels* (represented in 4 quadrants for each module).
  - The *current status* of each pump: whether it's *on, **off, or **being manually controlled*.
- *Manual Override: The mobile app allows the user to send commands (ON/OFF) to the ESP32, which **overrides automated decisions*.

### *4. Control Command Flow*
- *User Command via App: When the user interacts with the app to **manually control* any module (turning the pump ON or OFF), this command is published to an MQTT topic.
  - *Overrides automation*: The manual command takes priority and directly affects the corresponding water pump.

### *5. Real-Time Monitoring*
- The *mobile app* continuously fetches updated data from *Firebase* and displays it in real-time.
  - Visual representations like *colored circles* or *indicators* (green for "on", red for "off", yellow for "manual control") show the *status* of each module's pump.
  - The app gives *status feedback* to the user based on the data from Firebase (e.g., "Moisture Level: 70%").

---

## *System Architecture *

1. *ESP32*: 
   - Handles *all MQTT communication* between the system modules and cloud.
   - Retrieves *weather data* from the API and sends it to the RISC-V.
   - Publishes *status and moisture data* to *Firebase*.
   - Receives *manual control commands* from the mobile app and sends them to the RISC-V.

2. *RISC-V*:
   - Processes incoming *weather data* and *moisture data*.
   - Makes decisions based on predefined rules (or ML algorithm).
   - Sends back *ON/OFF commands* to the ESP32 to control water pumps.

3. *Mobile App*:
   - Retrieves data from *Firebase* in *real-time*.
   - Displays *moisture levels* and *pump statuses*.
   - Allows the user to *manually control* the pumps (override automated decisions).

4. *Cloud (Firebase)*:
   - *Stores real-time data*: Moisture readings, pump status, etc.
   - *Syncs data* between ESP32, mobile app, and the backend.



Demostration :
-
[Demostration](https://drive.google.com/drive/folders/1S8dyAjouMhKNCINMEEeU5fCrLpTjBWv_?usp=sharing)

Mobile app :
---
[Applink](https://drive.google.com/file/d/1aN2L2rVlaMM5e7xs0923USwjFQGpyZnE/view?usp=sharing)
