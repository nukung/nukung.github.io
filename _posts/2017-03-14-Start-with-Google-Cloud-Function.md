---
layout: post
title: เริ่มต้นใช้งาน Google Cloud Function บน Firebase
author: Panu Guyson
---

หลังจากที่ Google ได้ทำการเปิดตัว Cloud Function ซึ่งสำหรับนักพัฒนาที่ใช้ Firebase ก็จะสามารถใช้งาน function ต่างๆที่นอกเหนือจากใน Firebase โดยไม่ต้องติดตั้ง Server เพิ่มเติม เช่นการเข้ารหัส หรือ resize image สามารถลดงานไปได้พอสมควร ก็สมควรที่จะลองใช้ดู

post นี้จะถือว่าทุกคนติดตั้ง Firebase CLI อยู่แล้ว ถ้ายังไม่ได้ติดตั้ง ลองดูตัวอย่างที่ [https://firebase.google.com/docs/cli](https://firebase.google.com/docs/cli)

# Initial Function project

Login Firebase ด้วยคำสั่ง
{% highlight bash %}
firebase login
{% endhighlight %}
สร้าง Function project ด้วยคำสั่ง
{% highlight bash %}
firebase init functions
{% endhighlight %}
จะได้ project ที่มีโครงสร้างดังนี้
{% highlight bash %}
myproject
 +- .firebaserc   
 |
 +- firebase.json 
 |
 +- functions/     # Directory ที่เก็บ function code
      |
      +- package.json 
      |
      +- index.js      #  code ของ function
      |
      +- node_modules/ 
{% endhighlight %}

# Writing Code
ลองเขียน function ที่ไฟล์ functins/index.js โดยตัวอย่างจะทำ function addMessage() ที่ทำงานโดยเพิ่มข้อความจาก query string ลงไปยัง realtime database ที่ path /messages/:pushId/original
{% highlight js %}
var functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp(functions.config().firebase);
// Take the text parameter passed to this HTTP endpoint and insert it into the
// Realtime Database under the path /messages/:pushId/original
exports.addMessage = functions.https.onRequest((req, res) => {
  // Grab the text parameter.
  const original = req.query.text;
  // Push it into the Realtime Database then send a response
  admin.database().ref('/messages').push({original: original}).then(snapshot => {
    // Redirect with 303 SEE OTHER to the URL of the pushed object in the Firebase console.
    res.redirect(303, snapshot.ref);
  });
});
{% endhighlight %}

# Deploy
ทำการ deploy function ด้วยคำสั่ง
{% highlight bash %}
firebase deploy --only functions
{% endhighlight %}
เมื่อ deploy สำเร็จจะได้ url ของ function มา
{% highlight bash %}
Function URL (addMessage): https://us-central1-[your-project].cloudfunctions.net/addMessage
{% endhighlight %}

# Test
ทำการทดสอบ function ด้วยคำสั่ง
{% highlight bash %}
curl https://us-central1-[your-project].cloudfunctions.net/addMessage\?text\=FirebaseFunctions
{% endhighlight %}
เมื่อดูใน realtime database ก็จะเห็น message ที่ทำการ request เข้ามา
![Firebase functions result](/images/2017/03/14/functions-result.png "Firebase Function Result")
# Conclusion
Firebase Functions เหมือนกับเปิดขีดจำกัดของ Firebase ให้เพิ่มขึ้นอีก และเพิ่มความสะดวกสายในการบริหารจัดการด้วย 

**เพียงแต่ตอนนี้ยังเป็น Beta อยู่ ยังไม่ควรใช้ใน Production โดยเด็ดขาด** 

ยกเว้นรับความเสี่ยงได้
สำหรับตัวอย่างอื่นๆสามารถดูได้ที่ [https://github.com/firebase/functions-samples](https://github.com/firebase/functions-samples)

Reference: [https://firebase.google.com/docs/functions/write-firebase-functions](https://firebase.google.com/docs/functions/write-firebase-functions)