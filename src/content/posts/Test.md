---
title: "Connecting ESP8266 to an MQTT Broker using TLS"
pubDate: 2024-09-22 #Y-M-D
description: "A step-by-step guide to connect your ESP8266 to an MQTT broker securely using TLS."
author: "Arnab"
image: { url: "/logo.webp", alt: "ESP8266 MQTT" }
---

## Introduction

In this tutorial, we'll explore how to connect the ESP8266 microcontroller to an MQTT broker using Transport Layer Security (TLS) for secure communication. MQTT (Message Queuing Telemetry Transport) is a lightweight messaging protocol ideal for IoT applications. TLS adds an extra layer of security to protect your data during transmission.

## Prerequisites

- ESP8266 development board (e.g., NodeMCU, Wemos D1 Mini)
- Arduino IDE with ESP8266 board support installed
- An MQTT broker that supports TLS (e.g., CloudMQTT, AWS IoT)
- Basic understanding of Arduino programming

## Step 1: Setting Up Your Environment

1. **Install the Required Libraries**: Open the Arduino IDE and go to `Sketch` > `Include Library` > `Manage Libraries`. Search for and install the following libraries:
   - `PubSubClient`
   - `ESP8266WiFi`
   - `WiFiClientSecure`

2. **Create an MQTT Broker**: Sign up for a free account on an MQTT broker service that supports TLS, such as CloudMQTT. Obtain your broker's hostname, port (usually 8883 for TLS), username, and password.

## Step 2: Code Explanation

Here’s the complete code for connecting the ESP8266 to the MQTT broker securely using TLS:

```cpp
#include <ESP8266WiFi.h>
#include <WiFiClientSecure.h>
#include <PubSubClient.h>

// Replace these with your network credentials
const char* ssid = "YOUR_SSID";
const char* password = "YOUR_PASSWORD";

// Replace these with your MQTT broker details
const char* mqtt_server = "your_broker_url";
const int mqtt_port = 8883; // Default port for MQTT over TLS
const char* mqtt_user = "YOUR_MQTT_USERNAME";
const char* mqtt_password = "YOUR_MQTT_PASSWORD";
const char* mqtt_topic = "test/topic";

WiFiClientSecure espClient;
PubSubClient client(espClient);

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("Connected to WiFi");
  
  espClient.setInsecure(); // Use this for testing. In production, use certificate-based verification.
  client.setServer(mqtt_server, mqtt_port);
}

void reconnect() {
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    
    if (client.connect("ESP8266Client", mqtt_user, mqtt_password)) {
      Serial.println("connected");
      client.subscribe(mqtt_topic);
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      delay(2000);
    }
  }
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();
  
  // Publish a message every 5 seconds
  static unsigned long lastMsg = 0;
  unsigned long now = millis();
  if (now - lastMsg > 5000) {
    lastMsg = now;
    String msg = "Hello MQTT";
    client.publish(mqtt_topic, msg.c_str());
    Serial.println("Message sent: " + msg);
  }
}
```

### Code Breakdown
* Libraries: We include the necessary libraries for Wi-Fi and MQTT functionalities.
* Wi-Fi Setup: Replace YOUR_SSID and YOUR_PASSWORD with your Wi-Fi credentials.
* MQTT Setup: Input your MQTT broker details including the URL and credentials.
* Connecting to Wi-Fi: The setup() function connects the ESP8266 to Wi-Fi and the MQTT broker.
* Publishing Messages: In the loop() function, the ESP8266 publishes a message every 5 seconds.

### Step 3: Upload the Code
* Connect your ESP8266 to your computer via USB.
* In the Arduino IDE, select your board and port from Tools.
* Click on the upload button to upload the code to the ESP8266.
### Step 4: Monitor the Output
Open the Serial Monitor in the Arduino IDE (Ctrl + Shift + M) to see the connection status and messages being sent to the MQTT broker.

### Conclusion
You’ve successfully connected your ESP8266 to an MQTT broker using TLS! This setup ensures secure communication for your IoT applications. Experiment with different topics and payloads to expand your project's functionality. Happy coding!

Additional Resources[https://github.com/Arnab-m1/esp8266-tls-client/blob/main/esp_mqtt_tls.ino]
MQTT Protocol Overview[https://mqtt.org/]
ESP8266 Documentation[https://arduino-esp8266.readthedocs.io/en/latest/]
