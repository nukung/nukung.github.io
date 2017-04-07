---
layout: post
title: รัน go app แบบ background ด้วย supervisor บน Ubuntu 16.04
author: Panu Guyson
---
ในการนำ go มาใช้เป็น REST API หรือ Web Server เวลา Production จำเป็นต้องรันในลักษณะ background process
ซึ่งถ้าเริ่มต้นก็อาจจะใช้ **nohup** รันขึ้นมา ซึ่งจะเป็น command ทำให้การจัดการยุ่งยาก และไม่มี logrotate

แต่อีกทางเลือกคือใช้ **supervisor** มารันเป็น service ครอบ process ของ go application และจัดการเรื่อง logrotate ให้เลย

# Supervisor
[Supervisor](http://supervisord.org) คือ procress control system สำหรับควบคุม process และ จัดการ log โดยลดความยุ่งยากจากเรียก command ปรกติ หรือการใช้ nohup (เวลา restart ก็ไม่ต้อง kill PID ด้วย) เราเลยเลือกใช้ **supervisor** มาควบคุม go application ของเราแทน โดยขั้นตอนไม่ยุ่งยาก

## Install Supervisor บน Ubuntu 16.04

{% highlight bash %}
sudo apt-get install supervisor
{% endhighlight %}

## สร้าง supervisor group
{% highlight bash %}
sudo addgroup --system supervisor
sudo adduser <goappuser> supervisor
{% endhighlight %}

## Edit supervisor config
เพิ่ม config ของ supervisor ให้ group สามารถ read/write ได้
{% highlight txt %}
[unix_http_server]
file=/var/run/supervisor.sock   
chmod=0770                       # chmod เปลี่ยนเป็น 0770 จาก 0700
chown=root:supervisor            # chown ให้ group เป็น owner

[supervisord]
logfile=/var/log/supervisor/supervisord.log
pidfile=/var/run/supervisord.pid
childlogdir=/var/log/supervisor

[rpcinterface:supervisor]
supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock

[include]
files = /etc/supervisor/conf.d/*.conf
{% endhighlight %}

## Restart and check
{% highlight bash %}
sudo service supervisor restart
sudo tail /var/log/supervisor/supervisord.log # check log ว่าสามารถทำงานได้
{% endhighlight %} 

# สร้าง config สำหรับ go application
ทำการสร้างไฟล์ที่ /etc/supervisor/conf.d/goapp.conf
{% highlight bash %}
sudo vi /etc/supervisor/conf.d/goapp.conf
{% endhighlight %}

ใส่ config ตามตัวอย่าง
{% highlight txt %}
[program:goapp]
command=/home/<goappuser>/goapp # ที่อยู่ของ exec ไฟล์
autostart=true
autorestart=true
startretries=10
user=<goappuser> # user ที่รัน application
directory=/home/<goappuser>goapp # รัน applcation จาก directory
environment=APP_DB="mysql" # environmental variables
redirect_stderr=true
stdout_logfile=/var/log/supervisor/goapp.log # ที่อยู่ของ log
stdout_logfile_maxbytes=50MB
stdout_logfile_backups=10
{% endhighlight %}

# Test!
ลองทดสอบด้วยการ reload และ status
{% highlight bash %}
sudo service supervisor reload
sudo service supervisor status goapp
{% endhighlight %}

สามารถเช็คการทำงานได้ที่ log /var/log/supervisor/supervisord.log 

เท่านี้ก็สามารถจัดการ background process ได้ง่ายๆแล้ว supervisor สามารถประยุกต์ไปใช้ได้อีกเยอะ ไม่ใช่เฉพาะแต่ golang เท่านั้น ลองใ้ช้งานกันดูครับ :)