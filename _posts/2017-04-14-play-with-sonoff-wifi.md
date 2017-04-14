---
layout: post
title: มาเล่น Wireless Smart Switch(Sonoff) กันเถอะ
author: Panu Guyson
---
เมษาหน้าร้อน อากาศร้อน คนก็ร้อน เนื่องจากที่บ้านมีแมว :cat: ที่อาศัยอยู่ในบ้าน บวกกับกลางวันไปทำงานไม่มีใครอยู่บ้าน เลยคิด project ว่าจะทำ Smart Switch มาคอยควบคุมพัดลมให้ช่วยระบายความร้อนในบ้านเวลาไปทำงาน หาไปหามาเลยได้เจ้า Sonoff WiFi มาลองใช้งาน เลยมาลองกันว่าจะใช้งานได้อย่างที่คิดหรือเปล่า ??

# Sonoff คืออะไร ??
<p align="center"><img src="/images/2017/04/14/Sonoff_designs_1.jpg">
</p>

Sonoff เป็น WiFi Wireless Smart Switch ของบริษัท Itead ซึ่งใช้ ESP8266 โดยมี spec ดังนี้

## Specification
- Voltage range: 90-250v AC(50/60Hz)
- Max current: 10A
- Max Wattage: 2200 watts
- Dimensions: 88*38*23mm (L*W*H)
- Color: White
- Humidity: 5%-90%RH, Non-condensing
- Frequency: 2.4Ghz
- Operating Temperature: 0ºC-40ºC(32°F-104°F)

## การเชื่อมต่อบนบอร์ด Sonoff
![Sonoff Diagram](/images/2017/04/14/sonoff-parts-without-433.jpg)


ซึ่ง Sonoff เริ่มต้นแกะกล่องมาจะมาพร้อมกับ firmware ของ Itead เอง ซึ่งการใช้งานจะต้องใช้ร่วมกับ Application eWeLink มีใน iOS และ Android ครับ
![eWeLink](/images/2017/04/14/ewelinkappdownload.jpg)

แต่! จากรูปบอร์ดเราจะเป็น Serial-TTL ซึ่งเราสามารถเขียน firmware ลงไปได้ตามความต้องการ ซึ่งวิธีนี้จะมีเขียนในโพสหน้า(หรือโพสนู้นนนนน) ตอนนี้มาลองแบบ firmware ของ Itead กันก่อนดีกว่า

# เตรียมอุปกรณ์
ในตัวอย่างจะใช้ Sonoff ทำหน้าที่เป็นตัวสั่งงานปลั๊กที่มีการต่อพ่วงพัดลมไว้ ตาม Diagram ด้านล่าง
![Sonoff-diagram-fan](/images/2017/04/14/Sonoff-diagram-fan.jpg)
- Sonoff WiFi 1 ตัว
- เต้ารับ 1 ชุด (จะใช้ปลั๊กพ่วงมาตัดต่อสายก็ได้)
- สายไฟ พร้อมปลั๊กตัวผู้ 1 เส้น
- กล่องพลาสติก (ใช้ในการซ่อน Sonoff)
- คัตเตอร์ (ใช้ในการปอกสายไฟ)
- คีมปากนกแก้ว (ใช้ในการปอกสายไฟ)
- ไขควง
![Equipment](/images/2017/04/14/equipment.jpg)

# ทำ Input
- ตัดสายไฟออกมาเป็นสองส่วน ส่วนที่ 1 พร้อมปลั๊กตัวผู้ ใช้สำหรับฝั่ง Input ส่วน สายไฟเปล่าๆใช้สำหรับ Output
- นำสายไฟฝั่ง Input เสียบเข้า Sonoff โดยจำสีสายไฟของ N กับ L ไว้ด้วย เพราะต้องตรงกันในฝั่ง Output

![Sonoff device](/images/2017/04/14/sonoff-device.jpg)

# ฝั่ง Output บ้าง
- ทำฝั่งเต้ารับ เนื่องจากตัวอย่างซื้อแบบ 2 เต้ารับมา จึงพ่วงสายเข้าด้วยกัน ที่เต้ารับจะระบุตำแหน่งสาย N กับ L ให้นำสายมาเสียบให้ถูก ตามสีที่ทำในฝั่ง Input
![Output](/images/2017/04/14/output.jpg)

# เสร็จแล้ว!!
- พอไขน๊อตให้แน่นแล้ว เช็คสายที่ติดตั้งถูกต้อง ตามที่ระบุไว้ ก็นำไปเสียบปลั๊กได้เลย ถ้ามีไฟสีเขียวติดถือว่าใช้งานได้ตามเป้าหมาย
![Finished](/images/2017/04/14/finished.jpg)

- ทำการติดตั้งและ Add device เข้า โดยใช้วิธีการจาก [Wiki](https://www.itead.cc/blog/user-guide-for-sonoff-slampher)

เมื่อใช้งานได้ก็จะขึ้นให้เราเปิดปิด และ config ค่าต่างๆได้ รวมถึงทำ timer ให้ทำงานอัตโนมัติ
![example](/images/2017/04/14/example.gif)

เสร็จเรียบร้อยก็ใส่กล่อง เสียบพัดลมเข้ากับปลั๊กนำไปใช้งานได้เลย :smile_cat: สามารถนำไปประยุกต์ใช้งานอื่นๆได้อีกหลายแบบ ขึ้นอยู่กับไอเดียเลยนะ สำหรับถัดไปก็จะสั่งให้ Sonoff ทำงานอัตโนมัติตามอุณหภูมิได้ เพื่อให้ชีวิตง่ายขึ้น ไม่ต้องทำ timer :smile:

รอบหน้ามาต่อกันด้วย flash firmware Sonoff ของตัวเองลงไป เพื่อที่จะนำไปใช้บน Platform ของตัวเองครับ 
