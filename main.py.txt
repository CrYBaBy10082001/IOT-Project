from machine import Pin, I2C        #importing relevant modules & classes
import bme280        #importing BME280 library
import network
import time
import BlynkLib
from time import sleep
 
i2c=I2C(0,sda=Pin(0), scl=Pin(1), freq=400000)    #initializing the I2C method 
 
# WiFi credentials
WIFI_SSID = “12345678”
WIFI_PASSWORD = "12345678"
 
# Blynk authentication token
BLYNK_AUTH ="uR48KTdCoQVuBQoRIbizXn9NpHOC8q93"
 
 
# Connect to WiFi network
wifi = network.WLAN(network.STA_IF)
wifi.active(True)
wifi.connect(WIFI_SSID, WIFI_PASSWORD)
 
# Wait for the connection to be established
while not wifi.isconnected():
    sleep(1)
 
# Initialize Blynk
blynk = BlynkLib.Blynk(BLYNK_AUTH)
 
# Run the main loop
while True:
    # Read BME280 sensor data
    bme = bme280.BME280(i2c=i2c)
    temperature, pressure, humidity = bme.read_compensated_data()
 
    # Print sensor data to console
    print('Temperature: {:.1f} C'.format(temperature/100))
    print('Humidity: {:.1f} %'.format(humidity/1024))
    print('Pressure: {:.1f} hPa'.format(pressure/25600))
 
    # Send sensor data to Blynk
    blynk.virtual_write(0, temperature/100)  # virtual pin  for temperature
    blynk.virtual_write(2, humidity/1024)    # virtual pin  for humidity
    blynk.virtual_write(1, pressure/25600)   # virtual pin  for pressure
 
    # Run Blynk
    blynk.run()
 
    # Delay for 10 seconds
    time.sleep(5)