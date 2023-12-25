# ESP32-Servo-Control-with-Web-Server
Control a servo motor using an ESP32 and a web server
#include <WiFi.h>
#include <ESPAsyncWebServer.h>
#include <Servo.h>

const char *ssid = "your-ssid";
const char *password = "your-password";

Servo myservo;
const int SERVO_PIN = 9;

void setup() {
    Serial.begin(115200);
    WiFi.begin(ssid, password);

    while (WiFi.status() != WL_CONNECTED) {
        delay(1000);
        Serial.println("Connecting to WiFi...");
    }

    Serial.println("Connected to WiFi");

    myservo.attach(SERVO_PIN);

    // Start web server
    AsyncWebServer server(80);
    server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
        request->send(200, "text/plain", "Hello, World!");
    });

    server.on("/setservo", HTTP_POST, [](AsyncWebServerRequest *request){
        String angleValue = request->arg("angle");
        int angle = angleValue.toInt();
        myservo.write(angle);
        request->send(200, "text/plain", "Servo angle set to: " + angleValue);
    });

    server.begin();
}

void loop() {
    // Additional loop logic here
}
