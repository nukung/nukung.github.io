---
layout: post
title: ลองมาเพิ่มความสามารถของ Sonoff ด้วย Sonoff Tasmota!
author: Panu Guyson
---
จากที่เคยเขียนเรื่อง Sonoff Wifi ไปแล้ว ([มาเล่น Wireless Smart Switch(Sonoff) กันเถอะ](/play-with-sonoff-wifi/)) ซึ่งยังต้องใช้งานร่วมกับ Application eWeLink ทำให้ประยุกต์ไปใช้อย่างอื่นได้ยาก เราเลยจะมาลองใช้ Sonoff Tasmota เพื่อที่จะเพิ่มความสามารถของ Sonoff โดยไม่ต้องพึ่ง eWeLink กัน

# Sonoff Tasmota คืออะไร ??

[Sonoff Tasmota](https://github.com/arendst/Sonoff-Tasmota) คือ opensource firmware สำหรับ ESP8266 ซึ่งเป็น Microchip ที่ทาง Sonoff ใช้งาน โดยมีอุปกรณ์ที่รองรับหลายตัว สามารถดูเพิ่มเติมได้ที่ [https://github.com/arendst/Sonoff-Tasmota/wiki](https://github.com/arendst/Sonoff-Tasmota/wiki) โดยหลักๆ Sonoff Tasmota จะเพิ่มความสามารถคร่าวๆดังนี้

* เพิ่ม Web server control
* รองรับ MQTT
* OTA firmware update
* รองรับการใช้งานกับ OpenHAB, HomeAssistant

และอื่นๆอีกมากมาย ลองดูได้ที่  [https://github.com/arendst/Sonoff-Tasmota/wiki](https://github.com/arendst/Sonoff-Tasmota/wiki)

**คำเตือน ถ้าทำการ Flash firmware แล้ว จะกลับไปใช้ eWeLink ไม่ได้อีก**

# อุปกรณ์ที่ต้องใช้
![อุปกรณ์](/images/2018/03/11/IMG_20180311_131305.jpg)
ก่อนทำการ flash firmware ต้องใช้อุปกรณ์ดังนี้
* Sonoff ตามรายชื่อที่ Support ในที่นี้ใช้ Sonoff Wifi
* 3.3 V FTDI USB-to-Serial Converter/Programme
* หัวแร้ง และตะกั่วบัดกรี
* Header pin
* โปรแกรมสำหรับ build firmware และ flash ในที่นี้ใช้ PlatformIO ดูวิธี setup ได้ที่ [https://github.com/arendst/Sonoff-Tasmota/wiki/PlatformIO](https://github.com/arendst/Sonoff-Tasmota/wiki/PlatformIO)

# เตรียม Sonoff Wifi ให้พร้อม

**คำเตือน!!!! ก่อนทำการใดๆ ถอดสายไฟออกจาก Sonoff Wifi ให้หมดก่อน!!!**
เนื่องจาก Sonoff Wifi ใช้ ESP8266 เป็นพื้นฐานในการทำงาน เพราะฉะนั้นจะต้องมี Serial ให้เชื่อมต่อ โดยจะต้องเชื่อมต่อกับ USB-to-Serial Programme เพื่อที่จะทำการ flash แต่ Sonoff Wifi ไม่มี Pin serial มาให้ เราต้องทำการบัดกรีซะก่อน
โดยวิธีการดังนี้

1. ถอดบอร์ด Sonoff Wifi ออกจาก case 
2. วาง Header pin ลงไปตรงตำแหน่ง Serial บน board โดย Pin ที่ใกล้ปุ่มกดคือ 3V3/VCC 
![board](/images/2018/03/11/IMG_20180311_131340.jpg)
3. ใช้แขนจับ และทำการบัดกรีลงไปให้เรียบร้อย
![solder](/images/2018/03/11/IMG_20180311_131656.jpg)

4. เสร็จแล้วพักไว้ก่อน เรามาเตรียมโปรแกรมกันครับ 
![done](/images/2018/03/11/IMG_20180311_131449.jpg)



# เตรียม Program
1. Clone หรือ Download code มาจาก [https://github.com/arendst/Sonoff-Tasmota/](https://github.com/arendst/Sonoff-Tasmota/)
2. Setup PlatformIO และเปิด project ขึี้นมา (ในที่นี้ใช้บน Atom) 
3. เปิดไฟล์ platformio.ini ทำการ uncomment env_default โดยเลือก firmware ที่เราจะใช้ ตัวอย่างจะใช้ "env_default = sonoff"
4. เปิดไฟล์ sonoff/user_config.h ทำการแก้ไขข้อมูล Wifi โดยใช้ IP Address เป็น 0.0.0.0 เพราะ Wifi ทำการแจก DHCP มาให้ และใช้ SSID iot กับ Password 123456789012134
{% highlight c %}
// -- Wifi ----------------------------------------
#define WIFI_IP_ADDRESS        "0.0.0.0"         // [IpAddress1] Set to 0.0.0.0 for using DHCP or IP address
#define WIFI_GATEWAY           "192.168.2.254"   // {IpAddress2] If not using DHCP set Gateway IP address
#define WIFI_SUBNETMASK        "255.255.255.0"   // [IpAddress3] If not using DHCP set Network mask
#define WIFI_DNS               "192.168.2.27"    // [IpAddress4] If not using DHCP set DNS IP address (might be equal to WIFI_GATEWAY)

#define STA_SSID1              "iot"      // [Ssid1] Wifi SSID
#define STA_PASS1              "12345678901234"  // [Password1] Wifi password
#define STA_SSID2              "indebuurt2"      // [Ssid2] Optional alternate AP Wifi SSID
#define STA_PASS2              "VnsqrtnrsddbrN"  // [Password2] Optional alternate AP Wifi password
#define WIFI_CONFIG_TOOL       WIFI_WPSCONFIG    // [WifiConfig] Default tool if wifi fails to connect
                                                 //   (WIFI_RESTART, WIFI_SMARTCONFIG, WIFI_MANAGER, WIFI_WPSCONFIG, WIFI_RETRY, WIFI_WAIT)
{% endhighlight %}
5. build firmware โดยกดปุ่ม build ด้านซ้ายมือ

![build](/images/2018/03/11/build.JPG)
6. เมื่อ build สำเร็จ ก็จะขึ้นข้อความตามรูป

![build](/images/2018/03/11/build-success.JPG)
# อัพโหลดกันเถอะ

1. ทำการเสียบสาย USB-to-Serial กับ Sonoff Wifi ที่เราทำการบัดกรีไว้ ตามตารางด้านล่าง

 USB-toSerial | Sonoff Module
 ------------- |:-------------:
 3V3 | 3V3/VCC 
 TX | RX 
 RX | TX 
 GND | GND 

**Sonoff Wifi ใช้ 3V3 อย่าเผลอไปเสียบ 5V เด็ดขาด!!!**
 ![usb-to-serial](/images/2018/03/11/IMG_20180311_151311.jpg)

2. เสียบ USB-to-Serial เข้ากับ Computer โดยกดปุ่มที่ Sonoff ไว้ด้วย (GPIO00) เพื่อทำให้ Sonoff เข้าอยู่ firmware upload mode
3. upload firmware โดยกดปุ่ม upload ด้านซ้ายมือ
 ![upload](/images/2018/03/11/upload.JPG)
4. ระหว่าง upload ที่ USB-to-Serial จะมีไฟกระพริบ
 ![upload](/images/2018/03/11/line_1520756671628.gif)

5. เมื่อเสร็จแล้ว จะขึ้นข้อความตามด้านล่าง
 ![upload](/images/2018/03/11/upload-success.JPG)
6. ประกอบ Sonoff ลองทดสอบกัน!

# ลองเล่น web server

1. ทำการหา IP ของ Sonoff Wifi โดยใช้ Angry IP Scanner 
 ![ip](/images/2018/03/11/ip.JPG)
2. เข้า http://<ipaddress> ผ่านทาง Web browser จะพบเมนูต่างๆ ยุบยับเต็มไปหมด 

 ![web](/images/2018/03/11/web.JPG)

 
เท่านี้เราก็ Sonoff Wifi ที่สามารถทำอะไรได้มากขึ้นแล้ว ตอนหน้าเราจะมาลองติดตั้ง และ Config HomeAssistant เพื่อที่จะนำ Sonoff Wifi ไปใช้งานร่วมกัน สำหรับรายละเอียดเพิ่มเติมของ Sonoff-Tasmota สามารถอ่านได้ใน [wiki](https://github.com/arendst/Sonoff-Tasmota/wiki) ครับ!
