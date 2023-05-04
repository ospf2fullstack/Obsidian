# SparkFun

## AWS IoT MQTT Communication

-   ESP32, BME680 and Motion Sensor
    
    ```python
    // ESP32, BME680 and Motion Sensor Program
    
    // Include Files
    #include "Zanshin_BME680.h"    /// The BME680 sensor library
    #include "secrets.h"           // Connection credentials
    #include "WiFi.h"              // WiFi connection library
    #include <WiFiClientSecure.h>  // Enables secure connection over wifi
    #include <MQTTClient.h>        // MQTT Library Source: <https://github.com/256dpi/arduino-mqtt>
    #include <ArduinoJson.h>       // ArduinoJson Library Source: <https://github.com/bblanchon/ArduinoJson>
     
    WiFiClientSecure wifi_client = WiFiClientSecure();
    MQTTClient mqtt_client = MQTTClient(256);  //256 indicates the maximum size for packets being published and received.
     
    // Constants
    const uint32_t SERIAL_SPEED = 115200;      // Set the baud rate for Serial I/O
    const float SEA_LEVEL_PRESSURE = 1013.25;  // Standard atmosphere sea level pressure
    const uint32_t DATA_INTERVAL = 2000;       // Milliseconds between get data
     
    // MQTT topics for the device
    #define AWS_IOT_SENSOR_PREFIX "ncams01"
    #define AWS_IOT_PUBLISH_TOPIC "ncams01/pub"
    #define AWS_IOT_SUBSCRIBE_TOPIC "ncams01/sub"
     
    // Global Variables
    BME680_Class BME680;  // Create an instance of the BME680 class
    int32_t temperature;  // BME680 temperature value
    int32_t humidity;     // BME680 humidity value
    int32_t pressure;     // BME680 pressure value
    int32_t gas;          // BME680 gas resistance value
     
    int32_t start_pressure;  // Initial pressure reading
     
    float calcTemperature;  // calculated temperature value
    float calcHumidity;     // calculated humidity value
    float calcPressure;     // calculated pressure value
    float calcGas;          // calculated gas resistance value
    float calcAltitude;     // calculated altitude value
     
    uint32_t t1;                   // timing variable for json doc creation
    unsigned long lastMillis = 0;  ///< Millis() value for next data get
     
     
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
      //See <https://arduinojson.org/>
      StaticJsonDocument<200> doc;
      doc["elapsed_time"] = millis() - t1;
      doc["value"] = random(1000);
      char jsonBuffer[512];
      serializeJson(doc, jsonBuffer);  // print to mqtt_client
     
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
      while (!BME680.begin(I2C_STANDARD_MODE)) {  // Start using I2C, use first device found
        Serial.print("-  Unable to find BME680. Trying again in 5 seconds.\\n");
        delay(5000);
      }  // of loop until device is located
      Serial.print(F("- Setting 16x oversampling for all sensors\\n"));
      BME680.setOversampling(TemperatureSensor, Oversample16);  // Use enumerated type values
      BME680.setOversampling(HumiditySensor, Oversample16);     // Use enumerated type values
      BME680.setOversampling(PressureSensor, Oversample16);     // Use enumerated type values
      Serial.print(F("- Setting IIR filter to a value of 4 samples\\n"));
      BME680.setIIRFilter(IIR4);                                                    // Use enumerated type values
      Serial.print(F("- Setting gas measurement to 320\\xC2\\xB0\\x43 for 150ms\\n"));  // "�C" symbols
      BME680.setGas(320, 150);                                                      // 320�c for 150 milliseconds
    }
     
    void publishSensorData() {
      if (millis() > lastMillis) {              // if it is time to get another measurement
        lastMillis = millis() + DATA_INTERVAL;  // set next time to get a reading
        getSensorData();                        // get the BME680 data
     
        mqtt_client.publish("ncams01/temperature", String(calcTemperature));
        mqtt_client.publish("ncams01/humidity", String(calcHumidity));
        mqtt_client.publish("ncams01/pressure", String(calcPressure));
        mqtt_client.publish("ncams01/gas", String(calcGas));
        mqtt_client.publish("ncams01/altitude", String(calcAltitude));
     
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
      BME680.getSensorData(temperature, humidity, pressure, gas);                                  // Get readings
      calcAltitude = 44330.0 * (1.0 - pow(((float)pressure / 100) / SEA_LEVEL_PRESSURE, 0.1903));  // alitude in meters
      calcTemperature = (temperature / 100.0 * 9 / 5) + 32;                                        // temperature in farenheit
      calcHumidity = humidity / 1000.0;                                                            // humidity as one-thousandth of a percent (pcm or milli-pct)
      calcPressure = pressure / 100.0;                                                             // pressure in pascals
      calcGas = gas / 100.0;                                                                       // resistance in milliohms
    }
    ```
    

---

# IoT _Thing_ Setup

## IoT Thing

Please, do this step first! You need certificate information from AWS IoT before you can compile the Arduino sketch for upload to the ESP32 module.

1.  Login to AWS
2.  Navigate to AWS _IoT Device Management → Manage → Security → Policies_
    1.  Create Policy, if there's not one for you to use already.
3.  Navigate to AWS _IoT Device Management → Manage → All devices → Things_ back
4.  _Now, click "Create things" → "Create single thing" → Add Thing name._
    1.  _No additional (optional) attributes information is necessary._
    2.  _"Auto-generate a new certificate (recommended)"_
    3.  _Create Policy or attach this 'thing' to an existing policy that you previously created._
5.  _When prompted, download all certificate information (ca1.pem, ca3.pem, .crt, public key and private key)_
    1.  SparkFun MicroMod Programming

The following is code used to connect the MicroMod with the Environmental sensor to AWS. Before using this code install the following Libraries.

-   MQTT by Joel Gaehwiler: [](https://github.com/256dpi/arduino-mqtt)[https://github.com/256dpi/arduino-mqtt](https://github.com/256dpi/arduino-mqtt)
-   ArduinoJson by Benoit Blanchon: [](https://github.com/bblanchon/ArduinoJson)[https://github.com/bblanchon/ArduinoJson](https://github.com/bblanchon/ArduinoJson)
-   BME680 by Zanshin: [](https://github.com/Zanduino/BME680)[https://github.com/Zanduino/BME680](https://github.com/Zanduino/BME680)

## **Dependencies**

1.  Open Arduino IDE
2.  Navigate to the Library Manager (_Sketch → Include Library → Manage Libraries_)
3.  Search for the following and install (_you may need to remove 'by' to query correctly_):
    1.  _MQTT by Joel Gaehwiler_: [](https://github.com/256dpi/arduino-mqtt)[https://github.com/256dpi/arduino-mqtt](https://github.com/256dpi/arduino-mqtt)
    2.  _ArduinoJson by Benoit Blanchon_: [](https://github.com/bblanchon/ArduinoJson)[https://github.com/bblanchon/ArduinoJson](https://github.com/bblanchon/ArduinoJson)
    3.  _BME680 by Zanshin_: [](https://github.com/Zanduino/BME680)[https://github.com/Zanduino/BME680](https://github.com/Zanduino/BME680)
4.  You may also need the ESP32 Board Library installed.
    1.  On Windows: [Installing — Arduino-ESP32 2.0.6 documentation (espressif.com)](https://docs.espressif.com/projects/arduino-esp32/en/latest/installing.html)

## **Program Hardware**

1.  The below code block encompasses everything you need for the environment sensors to connect to Wi-Fi, collect data and send it to AWS IoT
    
2.  Make sure you update the _Thing_ name _//<-------- UPDATE THING NAME_ indicated below:
    
    ### Compile Sketch
    
    ```cpp
    // Include Files
    #include "Zanshin_BME680.h"    /// The BME680 sensor library
    #include "secrets.h"           // Connection credentials
    #include "WiFi.h"              // WiFi connection library
    #include <WiFiClientSecure.h>  // Enables secure connection over wifi
    #include <MQTTClient.h>        // MQTT Library Source: <https://github.com/256dpi/arduino-mqtt>
    #include <ArduinoJson.h>       // ArduinoJson Library Source: <https://github.com/bblanchon/ArduinoJson>
     
    WiFiClientSecure wifi_client = WiFiClientSecure();
    MQTTClient mqtt_client = MQTTClient(256);  //256 indicates the maximum size for packets being published and received.
     
    // Constants
    const uint32_t SERIAL_SPEED = 115200;      // Set the baud rate for Serial I/O
    const float SEA_LEVEL_PRESSURE = 1013.25;  // Standard atmosphere sea level pressure
    const uint32_t DATA_INTERVAL = 2000;       // Milliseconds between get data
     
    // MQTT topics for the device
    #define AWS_IOT_SENSOR_PREFIX "someIoTname"   // <-------- UPDATE THING NAME
    #define AWS_IOT_PUBLISH_TOPIC "someIoTname/pub" // <--------- UPDATE THING NAME
    #define AWS_IOT_SUBSCRIBE_TOPIC "someIoTname/sub" // <--------- UPDATE THING NAME
     
    // Global Variables
    BME680_Class BME680;  // Create an instance of the BME680 class
    int32_t temperature;  // BME680 temperature value
    int32_t humidity;     // BME680 humidity value
    int32_t pressure;     // BME680 pressure value
    int32_t gas;          // BME680 gas resistance value
     
    int32_t start_pressure;  // Initial pressure reading
     
    float calcTemperature;  // calculated temperature value
    float calcHumidity;     // calculated humidity value
    float calcPressure;     // calculated pressure value
    float calcGas;          // calculated gas resistance value
    float calcAltitude;     // calculated altitude value
     
    uint32_t t1;                   // timing variable for json doc creation
    unsigned long lastMillis = 0;  ///< Millis() value for next data get
     
     
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
      //See <https://arduinojson.org/>
      StaticJsonDocument<200> doc;
      doc["elapsed_time"] = millis() - t1;
      doc["value"] = random(1000);
      char jsonBuffer[512];
      serializeJson(doc, jsonBuffer);  // print to mqtt_client
     
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
      while (!BME680.begin(I2C_STANDARD_MODE)) {  // Start using I2C, use first device found
        Serial.print("-  Unable to find BME680. Trying again in 5 seconds.\\n");
        delay(5000);
      }  // of loop until device is located
      Serial.print(F("- Setting 16x oversampling for all sensors\\n"));
      BME680.setOversampling(TemperatureSensor, Oversample16);  // Use enumerated type values
      BME680.setOversampling(HumiditySensor, Oversample16);     // Use enumerated type values
      BME680.setOversampling(PressureSensor, Oversample16);     // Use enumerated type values
      Serial.print(F("- Setting IIR filter to a value of 4 samples\\n"));
      BME680.setIIRFilter(IIR4);                                                    // Use enumerated type values
      Serial.print(F("- Setting gas measurement to 320\\xC2\\xB0\\x43 for 150ms\\n"));  // "�C" symbols
      BME680.setGas(320, 150);                                                      // 320�c for 150 milliseconds
    }
     
    void publishSensorData() {
      if (millis() > lastMillis) {              // if it is time to get another measurement
        lastMillis = millis() + DATA_INTERVAL;  // set next time to get a reading
        getSensorData();                        // get the BME680 data
     
        mqtt_client.publish("someIoTname/temperature", String(calcTemperature)); // <------------ UPDATE THING NAME
        mqtt_client.publish("someIoTname/humidity", String(calcHumidity)); // <---------------- UPDATE THING NAME
        mqtt_client.publish("someIoTname/pressure", String(calcPressure)); // <---------------- UPDATE THING NAME
        mqtt_client.publish("someIoTname/gas", String(calcGas)); // <---------------- UPDATE THING NAME
        mqtt_client.publish("someIoTname/altitude", String(calcAltitude)); // <------------- UPDATE THING NAME
     
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
      BME680.getSensorData(temperature, humidity, pressure, gas);                                  // Get readings
      calcAltitude = 44330.0 * (1.0 - pow(((float)pressure / 100) / SEA_LEVEL_PRESSURE, 0.1903));  // alitude in meters
      calcTemperature = (temperature / 100.0 * 9 / 5) + 32;                                        // temperature in farenheit
      calcHumidity = humidity / 1000.0;                                                            // humidity as one-thousandth of a percent (pcm or milli-pct)
      calcPressure = pressure / 100.0;                                                             // pressure in pascals
      calcGas = gas / 100.0;                                                                       // resistance in milliohms
    }
    ```
    
3.  Create a separate file called '_secrets.h_', this is where we will plug in the certificate information and wifi information from earlier:
    
    1.  Look for the indicators below (// <--------------- UPDATE -----------------------) or ( // <-------------------ADD-------------------)
    2.  Obtain AWS_IOT_ENDPOINT address from _AWS IoT → Settings → Endpoint_
    
    ### Secrets.h
    
    ```cpp
    #include <pgmspace.h>
    #define THINGNAME "NCAM_EnvSensor"
    const char ssid[]="   "; // <--------------- UPDATE ----------------------
    const char password[] ="   "; // <--------------- UPDATE ----------------------
    const char AWS_IOT_ENDPOINT[]="   "; // <--------------- UPDATE ----------------------  
    // Amazon Root CA 1
    static const char AWS_CERT_CA[] PROGMEM = R"EOF(
    //-----BEGIN CERTIFICATE----- // <-------------------ADD CA1 certificate information-------------------
    //-----END CERTIFICATE-----
    )EOF";
     
    // Device Certificate
    static const char AWS_CERT_CRT[] PROGMEM = R"KEY(
    //-----BEGIN CERTIFICATE----- // <-------------------ADD CRT certificate information -------------------
    //-----END CERTIFICATE-----
    )KEY";
     
    // Device Private Key
    static const char AWS_CERT_PRIVATE[] PROGMEM = R"KEY(
    //-----BEGIN RSA PRIVATE KEY----- // <-------------------ADD PRIVATE key information-------------------
    //-----END RSA PRIVATE KEY-----
    )KEY";
    ```
    

Compile and ship.

# Validate Communication

## **Serial**

While the MicroMod is still connected via USB (COM#) serial connection, you can use the Arduino serial monitor to view the output (Tools → Serial Monitor):

## **MQTT test client**

If you want to truly test the cloud communication with the MQTT broker, navigate back to your _AWS Console window → AWS IoT Device Manager → Test → MQTT test client_

1.  Topic Filter:
    1.  ThingName/pressure
    2.  ThingName/temperature
    3.  ThingName/Humidity
    4.  ThingName/Gas
    5.  ThingName/Altitude
2.  Subscribe