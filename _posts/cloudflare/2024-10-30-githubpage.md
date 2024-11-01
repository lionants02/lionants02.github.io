---
layout: blog
book: true
title:  "การใช้ Github page ใน cloudflare"
background: green
background-image: /archives/image/githubpage-cloudflare-DALL·E.webp
date:   2024-10-30 06:09:15
category: cloudflare
tags:
- cloudflare
- github
- github page
---

# การใช้ Github page ใน cloudflare

From ChatGPT  

การตั้งค่า Cloudflare เพื่อให้ทำงานร่วมกับ GitHub Pages ของคุณสามารถทำได้โดยการชี้โดเมนของคุณจาก Cloudflare ไปยัง GitHub Pages ตามขั้นตอนดังนี้:

## 1. ตั้งค่าบน GitHub Pages
1. ไปที่ Repository ที่คุณต้องการตั้งค่า
2. เข้าไปที่ Settings > Pages
3. ในส่วน Custom Domain ให้ใส่โดเมนของคุณ เช่น ทองคำ.ไทย (โดยไม่ต้องมี "www") แล้วกด Save
4.  GitHub Pages จะตรวจสอบโดเมนและสร้างไฟล์ CNAME ใน Repository ของคุณ  

> หากยังไม่เคยใช้โดเมนใน GitHub Pages มาก่อน อาจต้องรอเวลาสักพักให้ GitHub ทำการตรวจสอบและเชื่อมต่อ

## 2. การตั้งค่า DNS ใน Cloudflare
1. ล็อกอินเข้า Cloudflare และเลือกโดเมนของคุณ
2. ไปที่ DNS ในแถบเมนู
   > ตั้งค่า A Record สำหรับ Root Domain (เช่น ทองคำ.ไทย):  
      Type: A  
      Name: @  
      IPv4 address: ใช้ IP ของ GitHub Pages ได้แก่ 185.199.108.153, 185.199.109.153, 185.199.110.153, 185.199.111.153 หรือดูได้จาก [DocGithub](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain)  
      Proxy status: DNS Only (ปิด Proxy หรือให้เป็นรูป "เมฆสีเทา")  

   > ตั้งค่า CNAME Record สำหรับ www (เช่น www.ทองคำ.ไทย):  
      Type: CNAME  
      Name: www  
      Target: ใส่โดเมนของคุณ (เช่น ทองคำ.ไทย)
      Proxy status: DNS Only  

   > เพิ่ม Subdomains หรือ Path หากต้องการ (เช่น blog.ทองคำ.ไทย):  
      Type: CNAME  
      Name: blog  
      Target: GitHub Pages URL ของคุณ เช่น username.github.io  
      Proxy status: DNS Only  
   
หมายเหตุ: Cloudflare ไม่ควรเปิด Proxy ให้กับ DNS ของ GitHub Pages เพราะอาจทำให้เกิดการ Redirect Loop หรือ SSL Error ได้  

## 3. ตั้งค่า SSL/TLS ใน Cloudflare
ในแถบ SSL/TLS ให้ตั้งค่าประเภท SSL เป็น Flexible หรือ Full เพื่อให้ทำงานร่วมกับ GitHub Pages SSL  
## 4. ทดสอบการตั้งค่า
1. เมื่อทำการตั้งค่าเสร็จสิ้นแล้ว รอสักครู่เพื่อให้ DNS ปรับปรุง (ใช้เวลา 24-48 ชั่วโมงในบางครั้ง)
2. ทดสอบเข้าเว็บของคุณผ่านโดเมนที่ตั้งไว้
