#include <WiFi.h>
#include <HTTPClient.h>

#include <DHT.h> 
#define DHTPIN 15 
#define DHTTYPE DHT22 
DHT dht22(DHTPIN, DHTTYPE); 

String URL = "http://ip address /file project /file code.php";

const char* ssid = "the internet name "; 
const char* password = " the password "; 

int temperature = 0;
int humidity = 0;

void setup() {
  Serial.begin(115200);
  dht11.begin();   
}

void loop() {
  if(WiFi.status() != WL_CONNECTED) {
    WiFi.mode(WIFI_OFF);
  delay(1000);
 
  WiFi.mode(WIFI_STA);
  
  WiFi.begin(ssid, password);
  Serial.println("Connecting to WiFi");
  
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
   Serial.println("");
  }
  temperature = dht11.readTemperature(); 
  humidity = dht11.readHumidity();
  if (isnan(temperature) || isnan(humidity)) {
    Serial.println("Failed to read from DHT sensor!");
    temperature = 0;
    humidity = 0;
  }
 
  String postData = "temperature=" + String(temperature) + "&humidity=" + String(humidity);
  
  HTTPClient http;
  http.begin(URL);
  http.addHeader("Content-Type", "application/x-www-form-urlencoded");
  
  int httpCode = http.POST(postData);
  String payload = "";

  if(httpCode > 0) {
    
    if(httpCode == HTTP_CODE_OK) {
      String payload = http.getString();
      Serial.println(payload);
    } else {
      
      Serial.printf("[HTTP] GET... code: %d\n", httpCode);
    }
  } else {
    Serial.printf("[HTTP] GET... failed, error: %s\n", http.errorToString(httpCode).c_str());
  }
  
  http.end();  

  Serial.print("URL : "); Serial.println(URL); 
  Serial.print("Data: "); Serial.println(postData);
  Serial.print("httpCode: "); Serial.println(httpCode);
  Serial.print("payload : "); Serial.println(payload);
  Serial.println("--------------------------------------------------");
  delay(5000);
}


![IOT3](https://github.com/1HALA1/IOT3/assets/128305211/3e77639f-35a6-4bc4-918b-a6d580358c9d)
