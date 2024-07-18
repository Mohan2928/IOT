#include <BLEDevice.h>
#include <BLEUtils.h>
#include <BLEServer.h>
#include <BLEAdvertisedDevice.h>
#include <BLEUUID.h>
#include <DHT.h>

#define SERVICE_UUID "00000002-0000-0000-FDFD-FDFDFDFDFDFD"
#define TEMPERATURE_CHARACTERISTIC_UUID "00002A1C-0000-1000-8000-00805F9B34FB"
#define HUMIDITY_CHARACTERISTIC_UUID "00002A6F-0000-1000-8000-00805F9B34FB"

#define DHTPIN 4      // Define the pin where the DHT sensor is connected
#define DHTTYPE DHT22 // Define the type of DHT sensor (DHT11 or DHT22)

DHT dht(DHTPIN, DHTTYPE);

BLEServer *pServer = NULL;
BLECharacteristic *pTempCharacteristic = NULL;
BLECharacteristic *pHumidityCharacteristic = NULL;

float getTemperature() {
  return dht.readTemperature();
}

float getHumidity() {
  return dht.readHumidity();
}

class ServerCallbacks : public BLEServerCallbacks {
  void onConnect(BLEServer* pServer) {
    Serial.println("Connected");
  }

  void onDisconnect(BLEServer* pServer) {
    Serial.println("Disconnected");
  }
};

void setup() {
  Serial.begin(115200);

  dht.begin();

  BLEDevice::init("ESP32_BLE_Service");
  Serial.println("BLE Device Initialized");

  pServer = BLEDevice::createServer();
  pServer->setCallbacks(new ServerCallbacks());

  BLEUUID serviceUUID = BLEUUID(SERVICE_UUID);
  BLEService *pService = pServer->createService(serviceUUID);

  BLEUUID tempCharacteristicUUID = BLEUUID(TEMPERATURE_CHARACTERISTIC_UUID);
  pTempCharacteristic = pService->createCharacteristic(tempCharacteristicUUID, BLECharacteristic::PROPERTY_READ | BLECharacteristic::PROPERTY_NOTIFY);

  BLEUUID humidityCharacteristicUUID = BLEUUID(HUMIDITY_CHARACTERISTIC_UUID);
  pHumidityCharacteristic = pService->createCharacteristic(humidityCharacteristicUUID, BLECharacteristic::PROPERTY_READ | BLECharacteristic::PROPERTY_NOTIFY);

  pService->start();

  pServer->getAdvertising()->addServiceUUID(serviceUUID);
  pServer->getAdvertising()->setScanResponse(false);
  pServer->getAdvertising()->start();

  Serial.println("Advertising started");
}

void loop() {
  float temperature = getTemperature();
  float humidity = getHumidity();

  pTempCharacteristic->setValue(temperature);
  pHumidityCharacteristic->setValue(humidity);

  pTempCharacteristic->notify();
  pHumidityCharacteristic->notify();

  delay(1000); // Delay for 1 second.
}
