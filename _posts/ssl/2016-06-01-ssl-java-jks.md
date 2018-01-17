---
layout: blog
book: true
title:  "ใบรับรอง SSL สำหรับใช้ใน JAVA"
background: green
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-15/78939382.jpg
date:   2016-06-01 22:27:39
category: ssl
tags:
- ssl
- openssl
- java ssl
- jks
---

# การเก็บ SSL Cert ไว้ใน JKS
การสร้างใบรับรองสำหรับใช้ในระบบของภาษา JAVA จะใช้เก็บไว้ในรูปแบบของ JKS ไฟล์ โดยเราสามารถ ใช้ Root ที่สร้างโดย openssl มารับรองได้

## สิ่งที่ต้องเตรียม
**จำเป็นต้องใช้ Device Cert**
สามารถอ่านส่วนของวิธีการสร้าง Device ได้ที่ [**ใบรับรอง SSL และการรับรอง SSL**][how-root] และทำการรับรอง ให้เรียบร้อย เราจะใช้ไฟล์ device.cer และ device.key  

## เริ่มการสร้าง Device JKS
  1. รวมไฟล์ device.cer และ device.key เข้าด้วยกัน ด้วยคำสั่ง  
  `cat device.cer device.key > device.ppp`  
  2. สร้างใบรับรองแบบ pkcs12 ด้วยคำสั่ง  
  `openssl pkcs12 -export -out device.pkcs12 -in device.ppp`  
  3. สร้าง jks ไฟล์ สำหรับเก็บใบรับรองของเรา ด้วยคำสั่ง  
  `keytool -genkey -alias service -keyalg RSA -keystore device.jks -keysize 4096`
    - จะมีช่องใส่ข้อมูลสำหรับเข้ารหัสขึ้นมา ใส่รหัสให้เรียบร้อย **สำคัญจำให้ได้ด้วย**  
  4. ลบส่วนที่ไม่จำเป็น ที่ถูกสร้างอัตโนมันติขึ้นมาใน service.jks ออก เพราะเราจะใช้เก็บ ssl key ของเรา ด้วคำสั่ง   
  `keytool -delete -alias service -keystore device.jks -storepass $PASSWORD`  
    - เปลี่ยน $PASSWORD เป็นรหัสผ่านในขั้นตอนที่ 3  
  5. นำ Device Key ของเรา ใส่ลงใน jks ด้วยคำสั่ง  
  `keytool -v -importkeystore -srckeystore device.pkcs12 -srcstoretype PKCS12 -destkeystore device.jks -storepass $PASSWORD -deststoretype JKS`  
    - เปลี่ยน $PASSWORD เป็นรหัสผ่านในขั้นตอนที่ 3  
  6. ให้ใช้ device.jks ไปใช้งานใน Tomcat หรือ java service อื่นที่รองรับ  


## สำหรับ Client ที่เป็น java
สำหรับ Client เราจะรับรองโดยใช้ Root ที่เราสร้างขึ้นมาโดยใช้คำสั่ง  
`keytool -import -v -trustcacerts -alias service -file rootCA.cer -keystore publicroot.jks -storepass 111111`
  - **111111** เป็นรหัสผ่าน
  - **publicroot.jks** ใช้ไฟล์นี้สำหรับ java client

## การนำไปใช้งาน

[มีวิธีการใช้งานใน Client คลิก][how-client-programm]  
[การใช้งาน Android รับรอง 2 ทิศทาง][how-android-programm]
[how-root]: https://lionants02.github.io/2015/05/24/create-ssl-cert.html
[how-client-programm]: https://stackoverflow.com/questions/875467/java-client-certificates-over-https-ssl
[how-android-programm]: http://callistaenterprise.se/blogg/teknik/2011/11/24/creating-self-signed-certificates-for-use-on-android/
