---
layout: blog
book: true
title:  "การตั้งค่าเริ่มต้น Docker Swarm"
background: green
background-image: http://ot1cc1u9t.bkt.clouddn.com/17-7-15/78939382.jpg
date:   2018-01-17 12:09:34
category: docker
tags:
- docker
- docker swarm
---

# การ Setup Docker swarm
## ทำความเข้าใจกันก่อน  
docker swarm เป็นระบบจัดการคลัสเตอร์ ของตัว docker มีความสามารถในการ เชื่อมต่อ แต่ละ node ที่กระจายอยู่ในแต่ละเครื่อง เข้าด้วยกัน ทั้งระบบ Network และระบบอื่นๆ ที่จำเป็น มีส่วนประกอบหลักๆ อยู่ 2 ส่วนคือ
1. Master เครื่องที่เป็น Worker จะเชื่อมมายังที่นี่
2. Worker รอรับงานจาก Master งานในที่นี้จะหมายถึง Docker container ที่ถูกส่งต่อมาจาก Master

## การกำหนดค่า
### ที่เครื่อง Master
เลือกเครื่องที่จะเป็น Master แล้วใช้คำสั่ง `docker swarm init`
```shell
$ docker swarm init
Swarm initialized: current node (ki9g3q6ibepqruvno33losn9s) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-4o2nlv5aq8fqkr4fv0w1znz8ykivukyi173xeuu8medzya3j0z-0u7e64mjlb67qup7oie7eb1q5 xxx.xxx.xxx.xxx:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```
เราจะได้ join-token สำหรับเครื่อง Worker มา ให้คัคัดลอกคำสั่งในบรรทัด docker swarm join..... ทั้งในบรรทัด ไปสั่งทำงานใน**อีกเครื่อง**ที่จะเป็น Worker

  > ในกรณีที่ไม่มี Worker ไม่เป็นอะไรครับ ระบบสามารถทำงานด้วย Master เพียงเครื่องเดียวได้ โดยปกติ Master จะทำตัวเป็น Worker อยู่แล้ว เพียงแต่ Master ถูกเพิ่มความสามารถในการจัดการ Worker เข้ามา

### ที่เครื่อง Worker
ที่เครื่อง Worker ให้นำคำสั่งจาก Master ในบรรทัด docker swarm join..... มาสั่งทำงาน


```shell
$ docker swarm join --token SWMTKN-1-4o2nlv5aq8fqkr4fv0w1znz8ykivukyi173xeuu8medzya3j0z-0u7e64mjlb67qup7oie7eb1q5 11.0.0.164:2377
This node joined a swarm as a worker.
```

เสร็จสิ้นการตั้งค่า Docker Swarm
