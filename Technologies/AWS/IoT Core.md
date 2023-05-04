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

  

### Sample Code: 
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

---

# reference: 
[Original Source](https://aws.amazon.com/blogs/compute/building-an-aws-iot-core-device-using-aws-serverless-and-an-esp32/)
Using a simple Arduino sketch, an AWS Serverless Application Repository application, and a microcontroller, you can build a basic serverless workflow for communicating with an AWS IoT Core device.

A microcontroller is a programmable chip and acts as the brain of an electronic device. It has input and output pins for reading and writing on digital or analog components. Those components could be sensors, relays, actuators, or various other devices. It can be used to build remote sensors, home automation products, robots, and much more. The [ESP32](https://www.espressif.com/en/products/hardware/esp32/overview) is a powerful low-cost microcontroller with Wi-Fi and Bluetooth built in and is used this walkthrough.

The [Arduino](https://www.arduino.cc/) IDE, a lightweight development environment for hardware, can be used to program an ESP32. There is a large collection of community and officially supported libraries, from [addressable LED strips](https://github.com/FastLED/FastLED) to [spectral light analysis](https://github.com/sparkfun/SparkFun_AS7265x_Arduino_Library).

Architecture diagram of the serverless application

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c18faff7-3e9b-495c-b965-6bae6c68c24c/Screen-Shot-2020-01-08-at-3.20.35-PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c18faff7-3e9b-495c-b965-6bae6c68c24c/Screen-Shot-2020-01-08-at-3.20.35-PM.png)

The following demonstrates connecting an ESP32 to [AWS IoT Core](https://aws.amazon.com/iot-core/) to allow it to publish and subscribe to topics. This means that the device can send any arbitrary information, such as sensor values, into AWS IoT Core while also being able to receive commands.

## Solution overview

This post walks through deploying an application from the [AWS Serverless Application Repository](https://aws.amazon.com/serverless/serverlessrepo/). This allows an [AWS IoT](https://aws.amazon.com/iot/) device to be messaged using a REST endpoint powered by [Amazon API Gateway](https://aws.amazon.com/api-gateway/) and [AWS Lambda](https://aws.amazon.com/lambda/). The AWS SAR application also configures an AWS IoT rule that forwards any messages published by the device to a Lambda function that updates an [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) table, demonstrating basic bidirectional communication.

The last section explores how to build an IoT project with real-world application. By connecting a thermal printer module and modifying a few lines of code in the example firmware, the ESP32 device becomes an AWS IoT–connected printer.

All of this can be accomplished within the [AWS Free Tier](https://aws.amazon.com/free/), which is necessary for the following instructions.

An example of an AWS IoT project using an ESP32, AWS IoT Core, and an Arduino thermal printer

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a4f33aa2-0ccd-4ec3-be49-e8366bdcf26b/Picture1.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a4f33aa2-0ccd-4ec3-be49-e8366bdcf26b/Picture1.png)

## Required steps

To complete the walkthrough, follow these steps:

-   Create an AWS IoT device.
-   Install and configure the Arduino IDE.
-   Configure and flash an ESP32 IoT device.
-   Deploying the lambda-iot-rule AWS SAR application.
-   Monitor and test.
-   Create an IoT thermal printer.

## Creating an AWS IoT device

To communicate with the ESP32 device, it must connect to AWS IoT Core with device credentials. You must also specify the topics it has permissions to publish and subscribe on.

1.  In the [AWS IoT console](https://aws.amazon.com/iot/), choose **Register a new thing**, **Create a single thing**.
    
2.  Name the new thing **MyNewESP32**. Leave the remaining fields set to their defaults. Choose **Next**.
    
3.  Choose **Create certificate**. Only the _thing cert_, _private key_, and _Amazon Root CA_ 1 downloads are necessary for the ESP32 to connect. Download and save them somewhere secure, as they are used when programming the ESP32 device.
    
4.  Choose **Activate**, **Attach a policy**.
    
5.  Skip adding a policy, and choose **Register Thing**.
    
6.  In the AWS IoT console side menu, choose **Secure**, **Policies**, **Create a policy**.
    
7.  Name the policy **Esp32Policy**. Choose the **Advanced** tab.
    
8.  Paste in the following policy template.
    
    ```
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": "iot:Connect",
          "Resource": "arn:aws:iot:REGION:ACCOUNT_ID:client/MyNewESP32"
        },
        {
          "Effect": "Allow",
          "Action": "iot:Subscribe",
          "Resource": "arn:aws:iot:REGION:ACCOUNT_ID:topicfilter/esp32/sub"
        },
    	{
          "Effect": "Allow",
          "Action": "iot:Receive",
          "Resource": "arn:aws:iot:REGION:ACCOUNT_ID:topic/esp32/sub"
        },
        {
          "Effect": "Allow",
          "Action": "iot:Publish",
          "Resource": "arn:aws:iot:REGION:ACCOUNT_ID:topic/esp32/pub"
        }
      ]
    }
    ```
    
9.  Replace **REGION** with the matching AWS Region you’re currently operating in. This can be found on the top right corner of the AWS console window.
    
10.  Replace **ACCOUNT_ID** with your own, which can be found in [Account Settings](https://console.aws.amazon.com/billing/home?#/account).
    
11.  Choose **Create**.
    
12.  In the AWS IoT console, choose **Secure, Certification.** Select the one created for your device and choose **Actions,** **Attach policy**.
    
13.  Choose **Esp32Policy**, **Attach**.
    

Your AWS IoT device is now configured to have permission to connect to AWS IoT Core. It can also publish to the topic **esp32/pub** and subscribe to the topic **esp32/sub**. For more information on securing devices, see [AWS IoT Policies](https://docs.aws.amazon.com/en_pv/iot/latest/developerguide/iot-policies.html).

## Installing and configuring the Arduino IDE

The Arduino IDE is an open-source development environment for programming microcontrollers. It can support a continuously growing number of platforms including most ESP32-based modules. It must be installed along with the ESP32 board definitions, MQTT library, and ArduinoJson library.

1.  [Download the Arduino installer](https://www.arduino.cc/en/Main/Software) for the desired operating system.
2.  Start Arduino and open the **Preferences** window.
3.  For **Additional Board Manager URLs**, add `https://dl.espressif.com/dl/package_esp32_index.json`.
4.  Choose **Tools**, **Board**, **Boards Manager**.
5.  Search `esp32` and install the latest version.
6.  Choose **Sketch**, **Include Library**, **Manage Libraries**.
7.  Search `MQTT`, and install the latest version by Joel Gaehwiler.
8.  Repeat the library installation process for `ArduinoJson`.

The Arduino IDE is now installed and configured with all the board definitions and libraries needed for this walkthrough.

## Configuring and flashing an ESP32 IoT device

A collection of various ESP32 development boards.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0981caa3-7929-42fb-bd76-28e116b8c782/9A794A9A-36B8-4A9F-9F11-EC73318ED200.jpg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0981caa3-7929-42fb-bd76-28e116b8c782/9A794A9A-36B8-4A9F-9F11-EC73318ED200.jpg)

For this section, you need an ESP32 device. To check if your board is compatible with the Arduino IDE, see the [boards.txt](https://github.com/espressif/arduino-esp32/blob/master/boards.txt) file. The following code connects to AWS IoT Core securely using [MQTT](https://docs.aws.amazon.com/iot/latest/developerguide/mqtt.html), a publish and subscribe messaging protocol.

This project has been tested on the following devices:

1.  Install the required serial drivers for your device. Some boards use different USB/FTDI chips for interfacing. Here are the most commonly used with links to drivers.
    
2.  Open the Arduino IDE and choose **File**, **New** to create a new sketch.
    
3.  Add a new tab and name it `secrets.h`.
    
4.  Paste the following into the secrets file.
    
    ```
    #include <pgmspace.h>
    
    #define SECRET
    #define THINGNAME ""
    
    const char WIFI_SSID[] = "";
    const char WIFI_PASSWORD[] = "";
    const char AWS_IOT_ENDPOINT[] = "xxxxx.amazonaws.com";
    
    // Amazon Root CA 1
    static const char AWS_CERT_CA[] PROGMEM = R"EOF(
    -----BEGIN CERTIFICATE-----
    -----END CERTIFICATE-----
    )EOF";
    
    // Device Certificate
    static const char AWS_CERT_CRT[] PROGMEM = R"KEY(
    -----BEGIN CERTIFICATE-----
    -----END CERTIFICATE-----
    )KEY";
    
    // Device Private Key
    static const char AWS_CERT_PRIVATE[] PROGMEM = R"KEY(
    -----BEGIN RSA PRIVATE KEY-----
    -----END RSA PRIVATE KEY-----
    )KEY";
    ```
    
5.  Enter the name of your AWS IoT thing, **MyNewESP32**, in the field **THINGNAME**.
    
6.  To connect to Wi-Fi, add the **SSID** and **PASSWORD** of the desired network. _Note: The network name should not include spaces or special characters_.
    
7.  The **AWS_IOT_ENDPOINT** can be found from the Settings page in the AWS IoT console.
    
8.  Copy the **Amazon Root CA 1**, **Device Certificate**, and **Device Private Key** to their respective locations in the `secrets.h` file.
    
9.  Choose the tab for the main sketch file, and paste the following.
    
    ```
    #include "secrets.h"
    #include <WiFiClientSecure.h>
    #include <MQTTClient.h>
    #include <ArduinoJson.h>
    #include "WiFi.h"
    
    // The MQTT topics that this device should publish/subscribe
    #define AWS_IOT_PUBLISH_TOPIC   "esp32/pub"
    #define AWS_IOT_SUBSCRIBE_TOPIC "esp32/sub"
    
    WiFiClientSecure net = WiFiClientSecure();
    MQTTClient client = MQTTClient(256);
    
    void connectAWS()
    {
      WiFi.mode(WIFI_STA);
      WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
    
      Serial.println("Connecting to Wi-Fi");
    
      while (WiFi.status() != WL_CONNECTED){
        delay(500);
        Serial.print(".");
      }
    
      // Configure WiFiClientSecure to use the AWS IoT device credentials
      net.setCACert(AWS_CERT_CA);
      net.setCertificate(AWS_CERT_CRT);
      net.setPrivateKey(AWS_CERT_PRIVATE);
    
      // Connect to the MQTT broker on the AWS endpoint we defined earlier
      client.begin(AWS_IOT_ENDPOINT, 8883, net);
    
      // Create a message handler
      client.onMessage(messageHandler);
    
      Serial.print("Connecting to AWS IOT");
    
      while (!client.connect(THINGNAME)) {
        Serial.print(".");
        delay(100);
      }
    
      if(!client.connected()){
        Serial.println("AWS IoT Timeout!");
        return;
      }
    
      // Subscribe to a topic
      client.subscribe(AWS_IOT_SUBSCRIBE_TOPIC);
    
      Serial.println("AWS IoT Connected!");
    }
    
    void publishMessage()
    {
      StaticJsonDocument<200> doc;
      doc["time"] = millis();
      doc["sensor_a0"] = analogRead(0);
      char jsonBuffer[512];
      serializeJson(doc, jsonBuffer); // print to client
    
      client.publish(AWS_IOT_PUBLISH_TOPIC, jsonBuffer);
    }
    
    void messageHandler(String &topic, String &payload) {
      Serial.println("incoming: " + topic + " - " + payload);
    
    //  StaticJsonDocument<200> doc;
    //  deserializeJson(doc, payload);
    //  const char* message = doc["message"];
    }
    
    void setup() {
      Serial.begin(9600);
      connectAWS();
    }
    
    void loop() {
      publishMessage();
      client.loop();
      delay(1000);
    }
    ```
    
10.  Choose **File**, **Save**, and give your project a name.
    

## Flashing the ESP32

1.  Plug the ESP32 board into a USB port on the computer running the Arduino IDE.
2.  Choose **Tools**, **Board**, and then select the matching type of ESP32 module. In this case, a [Sparkfun ESP32 Thing](https://learn.sparkfun.com/tutorials/esp32-thing-hookup-guide) was used.
3.  Choose **Tools**, **Port**, and then select the matching port for your device.
4.  Choose **Upload**. Arduino reads **Done uploading** when the upload is successful.
5.  Choose the magnifying lens icon to open the **Serial Monito**r. Set the baud rate to **9600**.

Keep the **Serial Monitor** open. When connected to Wi-Fi and then AWS IoT Core, any messages received on the topic **esp32/sub** are logged to this console. The device is also now publishing to the topic **esp32/pub.**

The topics are set at the top of the sketch. When changing or adding topics, remember to [add permissions in the device policy](https://docs.aws.amazon.com/iot/latest/developerguide/pub-sub-policy.html).

```
// The MQTT topics that this device should publish/subscribe
#define AWS_IOT_PUBLISH_TOPIC   "esp32/pub"
#define AWS_IOT_SUBSCRIBE_TOPIC "esp32/sub"
```

Within this sketch, the relevant functions are **publishMessage()** and **messageHandler()**.

The **publishMessage()** function creates a JSON object with the current time in milliseconds and the analog value of pin A0 on the device. It then publishes this JSON object to the topic **esp32/pub**.

```
void publishMessage()
{
  StaticJsonDocument<200> doc;
  doc["time"] = millis();
  doc["sensor_a0"] = analogRead(0);
  char jsonBuffer[512];
  serializeJson(doc, jsonBuffer); // print to client

  client.publish(AWS_IOT_PUBLISH_TOPIC, jsonBuffer);
}
```

The **messageHandler()** function prints out the topic and payload of any message from a subscribed topic. To see all the ways to parse JSON messages in Arduino, see the [deserializeJson()](https://arduinojson.org/v6/api/json/deserializejson/#example) example.

```
void messageHandler(String &topic, String &payload) {
  Serial.println("incoming: " + topic + " - " + payload);

//  StaticJsonDocument<200> doc;
//  deserializeJson(doc, payload);
//  const char* message = doc["message"];
}
```

Additional topic subscriptions can be added within the **connectAWS()** function by adding another line similar to the following.

```
// Subscribe to a topic
  client.subscribe(AWS_IOT_SUBSCRIBE_TOPIC);

  Serial.println("AWS IoT Connected!");
```

## Deploying the lambda-iot-rule AWS SAR application

Now that an ESP32 device has been connected to AWS IoT, the following steps walk through deploying an AWS Serverless Application Repository application. This is a base for building serverless integration with a physical device.

1.  On the [lambda-iot-rule](https://serverlessrepo.aws.amazon.com/applications/arn:aws:serverlessrepo:us-east-2:826492788183:applications~lambda-iot-rule) AWS Serverless Application Repository application page, make sure that the Region is the same as the AWS IoT device.
2.  Choose **Deploy**.
3.  Under **Application settings**, for **PublishTopic**, enter **esp32/sub**. This is the topic to which the ESP32 device is subscribed. It receives messages published to this topic. Likewise, set **SubscribeTopic** to **esp32/pub**, the topic on which the device publishes.
4.  Choose **Deploy**.
5.  When creation of the application is complete, choose **Test app** to navigate to the application page. Keep this page open for the next section.

## Monitoring and testing

At this stage, two Lambda functions, a DynamoDB table, and an [AWS IoT rule](https://docs.aws.amazon.com/iot/latest/developerguide/iot-rules.html) have been deployed. The IoT rule forwards messages on topic **esp32/pub** to TopicSubscriber, a Lambda function, which inserts the messages on to the DynamoDB table.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fa7786f3-e198-4d96-aa48-91723d9883fe/Picture7.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fa7786f3-e198-4d96-aa48-91723d9883fe/Picture7.png)

1.  On the application page, under **Resources**, choose **MyTable**. This is the DynamoDB table that the TopicSubscriber Lambda function updates.
2.  Choose **Items.** If the ESP32 device is still active and connected, messages that it has published appear here.

The TopicPublisher Lambda function is invoked by the API Gateway endpoint and publishes to the AWS IoT topic **esp32/sub**.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/409b8c20-1db9-4a77-b3c4-f9c9997a88e7/Picture8.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/409b8c20-1db9-4a77-b3c4-f9c9997a88e7/Picture8.png)

1.  On the application page, find the **Application endpoint**.
    
2.  To test that the TopicPublisher function is working, enter the following into a terminal or command-line utility, replacing **ENDPOINT** with the URL from above.
    

```
curl -d '{"text":"Hello world!"}' -H "Content-Type: application/json" -X POST <https://ENDPOINT/publish>
```

Upon success, the request returns a copy of the message.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00d88c56-183b-4e40-87d2-bed306db9c6a/Picture9.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00d88c56-183b-4e40-87d2-bed306db9c6a/Picture9.png)

Back in the **Serial Monitor**, the message published to the topic **esp32/sub** prints out.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/af0d0329-0e03-49b2-8da2-e956b16116ba/Picture10.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/af0d0329-0e03-49b2-8da2-e956b16116ba/Picture10.png)

## Creating an IoT thermal printer

With the completion of the previous steps, the ESP32 device currently logs incoming messages to the serial console.

The following steps demonstrate how the code can be modified to use incoming messages to interact with a peripheral component. This is done by wiring a thermal printer to the ESP32 in order to physically print messages. The REST endpoint from the previous section can be used as a webhook in third-party applications to interact with this device.

A wiring diagram depicting an ESP32 connected to a thermal printer.

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00b090de-2ce2-47b0-9d46-d6120dbe879e/Picture111.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/00b090de-2ce2-47b0-9d46-d6120dbe879e/Picture111.png)

1.  Follow the [product instructions](https://learn.adafruit.com/mini-thermal-receipt-printer/overview) for powering, wiring, and installing the correct Arduino library.
    
2.  Ensure that the thermal printer is working by holding the power button on the printer while connecting the power. A sample receipt prints. On that receipt, the default baud rate is specified as either **9600** or **19200**.
    
    ![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8e976ab4-9b36-4f2d-8c4c-91d73ae23dac/Picture12-300x179.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8e976ab4-9b36-4f2d-8c4c-91d73ae23dac/Picture12-300x179.png)
    
3.  In the Arduino code from earlier, include the following lines at the top of the main sketch file. The second line defines what interface the thermal printer is connected to. **&Serial2** is used to set the third hardware serial interface on the ESP32. For this example, the pins on the Sparkfun ESP32 Thing, **GPIO16/GPIO17**, are used for **RX/TX** respectively.
    
    ```
    #include "Adafruit_Thermal.h"
    
    Adafruit_Thermal printer(&Serial2);
    ```
    
4.  Replace the `setup()` function with the following to initialize the printer on device bootup. Change the baud rate of `Serial2.begin()` to match what is specified in the test print. The default is **19200**.
    
    ```
    void setup() {
      Serial.begin(9600);
    
      // Start the thermal printer
      Serial2.begin(19200);
      printer.begin();
      printer.setSize('S');
    
      connectAWS();
    }
    
    ```
    
5.  Replace the `messageHandler()` function with the following. On any incoming message, it parses the JSON and prints the message on the thermal printer.
    
    ```
    void messageHandler(String &topic, String &payload) {
      Serial.println("incoming: " + topic + " - " + payload);
    
      // deserialize json
      StaticJsonDocument<200> doc;
      deserializeJson(doc, payload);
      String message = doc["message"];
    
      // Print the message on the thermal printer
      printer.println(message);
      printer.feed(2);
    }
    ```
    
6.  Choose **Upload**.
    
7.  After the firmware has successfully uploaded, open the **Serial Monitor** to confirm that the board has connected to AWS IoT.
    
8.  Enter the following into a command-line utility, replacing **ENDPOINT**, as in the previous section. `curl -d '{"message": "Hello World!"}' -H "Content-Type: application/json" -X POST <https://ENDPOINT/publish`>
    

If successful, the device prints out the message “Hello World” from the attached thermal printer. This is a fully serverless IoT printer that can be triggered remotely from a webhook. As an example, this can be used with [GitHub Webhooks](https://developer.github.com/webhooks/) to print a physical readout of events.

## Conclusion

Using a simple Arduino sketch, an AWS Serverless Application Repository application, and a microcontroller, this post demonstrated how to build a basic serverless workflow for communicating with a physical device. It also showed how to expand that into an IoT thermal printer with real-world applications.

With the use of AWS serverless, advanced compute and extensibility can be added to an IoT device, from machine learning to translation services and beyond. By using the Arduino programming environment, the vast collection of open-source libraries, projects, and code examples open up a world of possibilities. The next step is to explore [what can be done with an Arduino](https://create.arduino.cc/projecthub) and the capabilities of [AWS serverless](https://aws.amazon.com/serverless/). The sample Arduino code for this project and more can be found at this [GitHub repository](https://github.com/aws-samples/aws-iot-esp32-arduino-examples).

#aws