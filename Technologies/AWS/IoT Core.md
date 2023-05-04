# IoT Core  

<aside>
🛠 Thing → Policy → Certificate → Communicate!
</aside>

## Things
Create a thing → Single Thing → “Name” → Autogenerate Certificates → “EnvSensor” → Create
“Thing” is a specific IoT device.
“Thing Type” is a group of things. Temperature sensors, TV IoT device, etc..
“Thing Group” is a group of models, or a more specific grouping of “Type”

## Policy
... coming soon.

### Builder
Allow (iot:Connect, iot:Publish, iot:Receive, iot:Subscribe) Resource * (any) → Create
<aside>
⛔ DOWNLOAD ALL THE CERTIFICATES
</aside>

# Arduino
### Sensor Programming
[[ESP32 MicroMod]]
Follow that link to a more comprehensive explanation of the code.

  

- Sample Code

    ```python

    // ESP32, BME680 and Motion Sensor Program

    // Include Files

    #include "Zanshin_BME680.h"    /// The BME680 sensor library

    #include "secrets.h"           // Connection credentials

    #include "WiFi.h"              // WiFi connection library

    #include <WiFiClientSecure.h>  // Enables secure connection over wifi

    #include <MQTTClient.h>        // MQTT Library Source: https://github.com/256dpi/arduino-mqtt

    #include <ArduinoJson.h>       // ArduinoJson Library Source: https://github.com/bblanchon/ArduinoJson

    WiFiClientSecure wifi_client = WiFiClientSecure();

    MQTTClient mqtt_client = MQTTClient(256);  //256 indicates the maximum size for packets being published and received.

    // Constants

    const uint32_t SERIAL_SPEED = 115200;      // Set the baud rate for Serial I/O

    const float SEA_LEVEL_PRESSURE = 1013.25;  // Standard atmosphere sea level pressure

    const uint32_t DATA_INTERVAL = 2000;       // Milliseconds between get data

    // MQTT topics for the device

    #define AWS_IOT_SENSOR_PREFIX "someIoTname"

    #define AWS_IOT_PUBLISH_TOPIC "someIoTname/pub"

    #define AWS_IOT_SUBSCRIBE_TOPIC "someIoTname/sub"

    // Global Variables

    BME680_Class BME680;  // Create an instance of the BME680 class

    int32_t temperature;  // BME680 temperature value

    int32_t humidity;     // BME680 humidity value

    int32_t pressure;     // BME680 pressure value

    int32_t gas;          // BME680 gas resistance value

    int32_t start_pressure;  // Initial pressure reading

    float calcTemperature;  // calculated temperature value

    float calcHumidity;     // calculated humidity value

    float calcPressure;     // calculated pressure value

    float calcGas;          // calculated gas resistance value

    float calcAltitude;     // calculated altitude value

    uint32_t t1;                   // timing variable for json doc creation

    unsigned long lastMillis = 0;  ///< Millis() value for next data get

    void connectAWS() {

      //Begin WiFi in station mode

      WiFi.mode(WIFI_STA);

      WiFi.begin(ssid, password);

      Serial.println("Connecting to Wi-Fi");

      //Wait for WiFi connection

      while (WiFi.status() != WL_CONNECTED) {

        delay(500);

        Serial.print(".");

      }

      Serial.println();

      // Configure wifi_client with the correct certificates and keys

      wifi_client.setCACert(AWS_CERT_CA);

      wifi_client.setCertificate(AWS_CERT_CRT);

      wifi_client.setPrivateKey(AWS_CERT_PRIVATE);

      //Connect to AWS IOT Broker. 8883 is the port used for MQTT

      mqtt_client.begin(AWS_IOT_ENDPOINT, 8883, wifi_client);

      //Set action to be taken on incoming messages

      mqtt_client.onMessage(incomingMessageHandler);

      Serial.print("Connecting to AWS IOT");

      //Wait for connection to AWS IoT

      while (!mqtt_client.connect(THINGNAME)) {

        Serial.print(".");

        delay(100);

      }

      Serial.println();

      if (!mqtt_client.connected()) {

        Serial.println("AWS IoT Timeout!");

        return;

      }

      //Subscribe to a topic

      mqtt_client.subscribe(AWS_IOT_SUBSCRIBE_TOPIC);

      Serial.println("AWS IoT Connected!");

    }

    void publishMessage() {

      //Create a JSON document of size 200 bytes, and populate it

      //See https://arduinojson.org/

      StaticJsonDocument<200> doc;

      doc["elapsed_time"] = millis() - t1;

      doc["value"] = random(1000);

      char jsonBuffer[512];

      serializeJson(doc, jsonBuffer);  // print to mqtt_client

      //Publish to the topic

      mqtt_client.publish(AWS_IOT_PUBLISH_TOPIC, jsonBuffer);

      Serial.println("Sent a message");

    }

    void incomingMessageHandler(String &topic, String &payload) {

      Serial.println("Message received!");

      Serial.println("Topic: " + topic);

      Serial.println("Payload: " + payload);

    }

    void setup() {

      Serial.begin(SERIAL_SPEED);

      t1 = millis();

      connectAWS();

      initEnvSensor();

    }

    void loop() {

      //publishMessage();

      mqtt_client.loop();

      //delay(4000);

      publishSensorData();

    }

    void initEnvSensor() {

      // Initialize BME680

      Serial.println("Initializing BME680 sensor");

      while (!BME680.begin(I2C_STANDARD_MODE)) {  // Start using I2C, use first device found

        Serial.print("-  Unable to find BME680. Trying again in 5 seconds.\n");

        delay(5000);

      }  // of loop until device is located

      Serial.print(F("- Setting 16x oversampling for all sensors\n"));

      BME680.setOversampling(TemperatureSensor, Oversample16);  // Use enumerated type values

      BME680.setOversampling(HumiditySensor, Oversample16);     // Use enumerated type values

      BME680.setOversampling(PressureSensor, Oversample16);     // Use enumerated type values

      Serial.print(F("- Setting IIR filter to a value of 4 samples\n"));

      BME680.setIIRFilter(IIR4);                                                    // Use enumerated type values

      Serial.print(F("- Setting gas measurement to 320\xC2\xB0\x43 for 150ms\n"));  // "�C" symbols

      BME680.setGas(320, 150);                                                      // 320�c for 150 milliseconds

    }

    void publishSensorData() {

      if (millis() > lastMillis) {              // if it is time to get another measurement

        lastMillis = millis() + DATA_INTERVAL;  // set next time to get a reading

        getSensorData();                        // get the BME680 data

        mqtt_client.publish("someIoTname/temperature", String(calcTemperature));

        mqtt_client.publish("someIoTname/humidity", String(calcHumidity));

        mqtt_client.publish("someIoTname/pressure", String(calcPressure));

        mqtt_client.publish("someIoTname/gas", String(calcGas));

        mqtt_client.publish("someIoTname/altitude", String(calcAltitude));

        Serial.print("Temp: ");

        Serial.println(calcTemperature);

        Serial.print("Humidity: ");

        Serial.println(calcHumidity);

        Serial.print("Pressure: ");

        Serial.println(calcPressure);

        Serial.print("Gas: ");

        Serial.println(calcGas);

        Serial.print("Altitude: ");

        Serial.println(calcAltitude);

        Serial.println();

      }

    }

    void getSensorData() {

      BME680.getSensorData(temperature, humidity, pressure, gas);                                  // Get readings

      calcAltitude = 44330.0 * (1.0 - pow(((float)pressure / 100) / SEA_LEVEL_PRESSURE, 0.1903));  // alitude in meters

      calcTemperature = (temperature / 100.0 * 9 / 5) + 32;                                        // temperature in farenheit

      calcHumidity = humidity / 1000.0;                                                            // humidity as one-thousandth of a percent (pcm or milli-pct)

      calcPressure = pressure / 100.0;                                                             // pressure in pascals

      calcGas = gas / 100.0;                                                                       // resistance in milliohms

    }

    ```
### Prerequisites
[Github.com](http://Github.com) → Download Zip from the desired repo → Navigate to Arduino IDE → File → Preferences: Skethbook location: C:\User\Documents\Arduino (usually)

*Once you know the Arduino library location, you can drag the extracted zip file over to this location to import the libraries into Arduino IDE.*