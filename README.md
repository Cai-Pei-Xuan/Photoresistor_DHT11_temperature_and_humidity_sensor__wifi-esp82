# Photoresistor_DHT11_temperature_and_humidity_sensor__wifi-esp82
光敏電阻 + DHT11 (溫溼度感應器) + wifi-esp8266

## 光敏電阻
### 目的：利⽤光敏電阻，感測目前環境之亮度。
### 材料：

| #   | 名稱          | 數量 |
| --- | ------------- | ---- |
| 1   | 麵 包 板      | * 1  |
| 2   | Arduino UNO   | * 1  |
| 3   | 光敏電阻      | * 1  |
| 4   | 10 k ohm 電阻 | * 1  |
| 5   | 單⼼線        | * N  |

![](https://i.imgur.com/x60FfbC.png)

### 接線:
光敏電阻⼀⽀腳接收5V，另⼀⽀腳串接⼀顆10k電阻接收模擬引腳2
![](https://i.imgur.com/9CEi0oG.png)
### 程式碼
```cpp=
// 讀取光敏電阻
int lightPin = 2; // 光敏電阻 (photocell) 接在 anallog pin 2
int Val = 0; // photocell variable

void setup() {
 Serial.begin(9600);
}

void loop() {
 // 讀取光敏電阻並輸出到 Serial Port
 Val = analogRead(lightPin);
 Serial.println(Val);
 Serial.print("\n");
}
```
### 執行結果
![](https://i.imgur.com/j940Duw.png)

## DHT11 (溫溼度感應器)
### 目的：利⽤溫溼度感應器，感測目前環境之溫溼度。
### 材料：

| #   | 名稱        | 數量 |
| --- | ----------- | ---- |
| 1   | 麵 包 板    | * 1  |
| 2   | Arduino UNO | * 1  |
| 3   | DHT11       | * 1  |
| 4   | NCR18650B   | * 1  |
| 5   | 單⼼線      | * N  |

![](https://i.imgur.com/opawVsd.png)

### 接線:
![](https://i.imgur.com/n98TJ2M.png)
### 下載DHT11模組
![](https://i.imgur.com/hkWKKsg.png)
### 程式碼
```cpp=
//溫溼度感應器
#include "DHT.h"
#define dhtPin 11   //讀取DHT11 Data
#define dhtType DHT11 //選用DHT11   
DHT dht(dhtPin, dhtType); // 初始化 DHT 

float h = 0;
float t = 0;

void setup() {
    //啟動DHT
    dht.begin();
}

void loop() {
  // 讀取溫溼度感應器
  h = dht.readHumidity();//讀取濕度
  t = dht.readTemperature();//讀取攝氏溫度

  //檢查是否能收到DHT值
  if (isnan(h) || isnan(t)) {
    Serial.println("無法從DHT傳感器讀取！");
    return;
  }
  Serial.print("濕度: ");
  Serial.print(h);
  Serial.print("%\t");
  Serial.print("攝氏溫度: ");
  Serial.print(t);
  Serial.print("*C\n");
}
```
### 執行結果
![](https://i.imgur.com/6Tx3oVz.png)

## WIFI-ESP8266
### 目的：利⽤ WIFI 模組連接無線網路，上傳感測到的光敏電阻值和溫溼度值。
### 材料：

| #   | 名稱        | 數量 |
| --- | ----------- | ---- |
| 1   | 麵 包 板    | * 1  |
| 2   | Arduino UNO | * 1  |
| 3   | WIFI 模組   | * 1  |
| 4   | NCR18650B   | * 1  |
| 5   | 單⼼線      | * N  |

![](https://i.imgur.com/JgkuZzh.png)

### 接線:
![](https://i.imgur.com/xqo4Exm.png)
### AT Command
![](https://i.imgur.com/caBuFMn.png)
### 程式碼
```cpp=
//Wi-Fi
#include <SoftwareSerial.h>
#define DEBUG true

//connect
String SID = "wifi 名 稱 ";
String PWD = "wifi 密 碼 ";
String IP = "server 位 置 ";
String file ="接收php檔案";
boolean upload = false;

// connect 8 to TX of wifi-esp8266
// connect 9 to RX of wifi-esp8266
SoftwareSerial esp8266(8,9); // RX, TX

void setup() {
    // enable debug seriala
    Serial.begin(9600); 
    // enable software serial
    esp8266.begin(115200);
    //Reset esp8266,change mode,connect wifi
    init_wifi();
}

void loop() {
  // 傳送command給esp8266
  while(Serial.available())
  {
     char input = Serial.read();
      
     if(input =='o')//uplao
     {
        Serial.println("|---Start Upload---|");
        upload = true;
     }
     else if(input =='x')
     {
        Serial.println("|---Stop Upload---|");
        upload = false;
     }
     else if(input == 'r')
     {
        init_wifi();
     }
     else  
        Serial.print(input);   
  }
 
  // esp8266回覆狀態
  while(esp8266.available())
  {
     Serial.write(esp8266.read());
  }
   
  // 是否上傳
  if(upload)
  { 
     uploadData();
  }
  
}
```
### 函⽰說明
![](https://i.imgur.com/19jEZ5j.png)
### 執行結果
![](https://i.imgur.com/bEAUMwQ.png)
![](https://i.imgur.com/ZeP7kyB.png)
![](https://i.imgur.com/ElajxvE.png)

## 完整實作圖
![](https://i.imgur.com/DHb2K1X.jpg)

## 完整程式碼
```cpp=
//Wi-Fi
#include <SoftwareSerial.h>
#define DEBUG true

//溫溼度感應器
#include "DHT.h"
#define dhtPin 11   //讀取DHT11 Data
#define dhtType DHT11 //選用DHT11   
DHT dht(dhtPin, dhtType); // 初始化 DHT 

// light
int lightPin = 2;        //讀取 光敏電阻 Data
int Val = 0;
float h = 0;
float t = 0;

//connect
String SID = "wifi 名 稱 ";
String PWD = "wifi 密 碼 ";
String IP = "server 位 置 ";
String file ="接收php檔案";
boolean upload = false;


// connect 8 to TX of wifi-esp8266
// connect 9 to RX of wifi-esp8266
SoftwareSerial esp8266(8,9); // RX, TX

void setup() {
    // enable debug seriala
    Serial.begin(9600); 
    // enable software serial
    esp8266.begin(115200);
    //啟動DHT
    dht.begin();
    //Reset esp8266,change mode,connect wifi
    init_wifi();
}
 
void loop() {
  
  // 讀取光敏電阻
  Val = analogRead(lightPin);
  // 讀取溫溼度感應器
  h = dht.readHumidity();//讀取濕度
  t = dht.readTemperature();//讀取攝氏溫度

  //檢查是否能收到DHT值
  if (isnan(h) || isnan(t)) {
    Serial.println("無法從DHT傳感器讀取！");
    return;
  }
  Serial.print("光敏電阻: ");
  Serial.print(Val);
  Serial.print("濕度: ");
  Serial.print(h);
  Serial.print("%\t");
  Serial.print("攝氏溫度: ");
  Serial.print(t);
  Serial.print("*C\n");
 
  // 傳送command給esp8266
  while(Serial.available())
  {
     char input = Serial.read();
      
     if(input =='o')//uplao
     {
        Serial.println("|---Start Upload---|");
        upload = true;
     }
     else if(input =='x')
     {
        Serial.println("|---Stop Upload---|");
        upload = false;
     }
     else if(input == 'r')
     {
        init_wifi();
     }
     else  
        Serial.print(input);   
  }
 
  // esp8266回覆狀態
  while(esp8266.available())
  {
     Serial.write(esp8266.read());
  }
   
  // 是否上傳
  if(upload)
  { 
     uploadData();
  }
  
}


// reset ESP8266
void init_wifi(){
  Serial.println("|---Reset---|");
  sendCommand("AT+RST\r\n",5000,DEBUG); // reset module
  sendCommand("AT+CWMODE=1\r\n",2000,DEBUG); // configure as access point
  sendCommand("AT+CWJAP=\""+SID+"\",\""+PWD+"\"\r\n",5000,DEBUG);
  sendCommand("AT+CIPMUX=0\r\n",2000,DEBUG); // configure for single connections
  Serial.println("|---Reset Finish---|");
}

// send command
String sendCommand(String command, const int timeout, boolean debug)
{
    String response = "";
    
    esp8266.print(command); // send the read character to the esp8266
    
    long int time = millis();
    
    while( (time+timeout) > millis())
    {
      while(esp8266.available())
      {    
        // The esp has data so display its output to the serial window 
        char c = esp8266.read(); // read the next character.
        response+=c;
      }  
    }
    
    if(debug)
    {
      Serial.print(response);
    }
    Serial.print(response);
    return response;
}

// upload data
void uploadData()
{
  // convert to string
  // TCP connection
  String cmd = "AT+CIPSTART=\"TCP\",\"";
  cmd += IP; //host
  cmd += "\",Port";
  esp8266.println(cmd);
   
  if(esp8266.find("Error")){
    Serial.println("AT+CIPSTART error");
    return;
  }
  
  // prepare GET string
  String getStr = "GET /"+file+"?light="+String(Val)+"&humi="+String(h)+"&temp="+String(t);
  getStr +=" HTTP/1.1\r\nHost:"+IP;
  getStr += "\r\n\r\n";

  // send data length
  cmd = "AT+CIPSEND=";
  cmd += String(getStr.length());
  esp8266.println(cmd);

  if(esp8266.find(">")){
    esp8266.print(getStr);
  }
  else{
    esp8266.println("AT+CIPCLOSE");
    // alert user
    Serial.println("AT+CIPCLOSE");
  }
  delay(1000);  
}
```
