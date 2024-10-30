---
layout: blog
book: true
title:  "ใบรับรอง SSL และการรับรอง SSL"
background: green
background-image: /archives/image/ssl-x509-DALL·E.webp
date:   2015-05-24 08:12:28
category: ssl
tags:
- ssl
- openssl
---

# การสร้างใบรับรอง SSL
ในบทความนี้ จะพูดถึงวิธีการสร้าง ใบรับรองหลักหรือ Root และวิธีการให้ Root รับรองใบรับรองที่ Device สร้างขึ้นมาตามหลัง  
 > ทำไมถึงต้องมีทั้ง Root และ Device เพราะ เวลาเรานำไปใช้งาน เราจะได้นำ Root Cert ไปรับรอบความถูกต้องแค่ไฟล์เดียว เช่น  

 > ใช้ Root รับรอง Device ที่เป็น Server ssl api ทั้งหมด 10 เครื่อง โดยระบบมีการ เข้ารหัส และ ตรวจสอบความน่าเชื่อถือของการเชื่อมต่อ เวลา Client จะใช้งาน เราก็จะอ้างเพียง Public Root แค่ใบเดียว

## การสร้างใบรับรอง
### การสร้าง Root Cert
1. สร้าง Root key ขึ้นมาด้วยคำสั่งด้านล่าง ระบบจะให้ใส่รหัสสำหรับเข้ารหัสไฟล์ key  
`$ openssl genrsa -des3 -out rootCA.key 4096`  
  - **4096** คือขนาดของ key(กุญแจ) ปัจจุบันนิยมใช้ขนาด 4096 กัน เพราะ คนทั่วไปเชื่อว่ามีความปลอดภัย ขนาดใหญ่กว่าก็ต้องใช้เวลาในการสุ่มนานกว่า   

2. นำ Root key มาสร้างใบรับรองของ Public Root cert ขึ้นมาโดยเราจะต้องใส่รายละเอียดต่างๆ เช่น ที่อยู่ ชื่อ บลาๆๆๆ โดยใช้คำสั่งตามด้านล่างนี้  
`$ openssl req -x509 -new -nodes -key rootCA.key -days 5000 -out rootCA.cer`  
  - **5000** คือ จำนวนวันของใบรับรองที่จะหมดอายุ โดยเริ่มนับจากวันที่สร้าง หรือก็คือ เริ่มนับจากวันนี้
  - เวลาต้องการนำไปใช้งานกับ Client เราจะทำการ Trust(เชื่อถือใบอื่น ที่ใบนี้รับรอง) ด้วยไฟล์ **rootCA.cer**

### สร้าง Device Cert ให้อุปกรณ์อื่นๆ
หลังจากเราได้ Root key และ Public Root cert มาแล้ว เราก็สามารถนำ Root มารับรองให้กับ อุปกรณ์ต่างๆ ได้
  1. สร้าง Device key ด้วยคำสั่ง  
  `$ openssl genrsa -out device.key 4096`
  2. สร้าง Device cert ด้วยคำสั่ง  
  `$ openssl req -new -key device.key -out device.csr`  
    - กรอกรายละเอียดให้ครบ ในส่วนของ **Common Name (e.g. server FQDN or YOUR name)** สำคัญมาก ถ้าระบบอ้างโดยโดเมนเนม ให้ใส่ชื่อโดเมนเนมของ Device แต่หากอ้างโดย IP ให้ใส่ IP ไม่งั้นกุญแจจะไม่เขียว  
    - Common Name หากกรอกผิดมีส่วนทำให้เป็น**กุญแจแดง**
  3. สร้าง Public Device cert ที่ผ่านการรับรองด้วย Root โดยใช้คำสั่ง  
  `$ openssl x509 -req -in device.csr -CA rootCA.cer -CAkey rootCA.key -CAcreateserial -out device.cer -days 365`  
  เวลา Device นำไปใช้งาน เช่น ใช้กับ Server ที่เป็น Nginx ssl ให้ใช้
    - device.cer  
    - device.key  
    โดยเครื่อง ที่จะเชื่อมต่อ ต้อง Trust rootCA.cer ก่อน

## อ้างอิง
[https://blog.didierstevens.com/2013/05/08/howto-make-your-own-cert-and-revocation-list-with-openssl/](https://blog.didierstevens.com/2013/05/08/howto-make-your-own-cert-and-revocation-list-with-openssl/)  
