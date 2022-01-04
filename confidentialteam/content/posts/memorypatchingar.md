---
title: "Memory Patching (AMSI Bypass)"
date: 2021-12-25T21:56:40-05:00
---

## Introduction

<DIV dir="rtl" align="right">
ألسلام عليكم
</DIV>


<DIV dir="rtl" align="right">
معظمنا واجه ال (Antimalware Scan Interface) أو المعروفة ب `AMSI`. وغالباً واجهتم مشاكل في اي وقت حاولتم تضيفون powershell module مثل `Mimikatz` وغيرها. ال `AMSI` رح يصيرل له load في أي Application يستخدم ال `WIN32 API`. تقدرون تطلعون على ال `AMSI architecture` من الصورة الي بالأسفل
</DIV>

![](../../img/memorypatching/readme/1.png)

## Overview

<DIV dir="rtl" align="right">
من الصورة نقدر نلاحظ ان ال function المسؤل عن التشيك على الملفات الخبيثة هو `AmsiScanBuffer()` و `AmsiScanString()`. نقدر نستخدم `Process Hacker` ونشوف ايش ال Modules الي جالسة تشتغل مع اي برنامج. فمثلاً في ال Powershell نقدر نشوف ان ال `amsi.dll` صاير له load في ال memory.
</DIV>

![](../../img/memorypatching/readme/2.png)

<DIV dir="rtl" align="right">
نقدر بنفس الوقت نشوف ال exported functions الي ذكرناهم سابقاً. تركيزنا رح يكون على ال `AmsiScanBuffer()`
</DIV>

![](../../img/memorypatching/readme/3.png)

## AmsiScanBuffer()

<DIV dir="rtl" align="right">
اشتغلنا على ال reverse engineer لهذا ال function وفي نهايته شفنا هذه ال instructions:
</DIV>

![](../../img/memorypatching/readme/4.png)

## Playing with WinDBG

<DIV dir="rtl" align="right">
نقدر نلاحظ أن ال process الخاصة بال scanning موجودة في اليسار. وفي المربع اليمين نقدر نشوف ال القيمة `0x0070057` والي هي عبارة عن error code من Microsoft. طيب ايش ممكن يصير اذا عدلنا على ال `AmsiScanBuffer()` في ال memory وخليناه دائماً يرجع لنا ال `0x80070057`. نقدر نجربها عن طريق WinDBG:
</DIV>

![](../../img/memorypatching/readme/5.gif)

<DIV dir="rtl" align="right">
نقدر نشوف ال instructions الخاصة ب `AmsiScanBuffer()` وبنفس الوقت نقدر نشوف ال bytecode المقابل ل `mov eax,80070057h` والي هي `b857000780`. نقدر الأن نعدل على البداية الخاصة بال function عن طريق هذه ال instructions:
</DIV>


```assembly
b857000780          mov eax,0x80070057
c3                  ret
```

<DIV dir="rtl" align="right">
الي رح يصير هو الأتي رح نودي `0x80070057` الى `EAX` والي رح تكون ال return value ل `AmsiScanBuffer()`. نقدر نسوي هذا الكلام عن طريق WinDBG أول شي شغل powershell وسوي لها attach في WinDBG. حاول تسوي load لاي شي ممكن ال AMSI يوقفه:
</DIV>

![](../../img/memorypatching/readme/6.png)

<DIV dir="rtl" align="right">
الأن لازم نسوي reverse لل byte-code `mov eax,0x80070057 | ret` فتصير: `c380070057b8`. نقدر نستخدمها ونعدل على ال function عن طريق هذا الكوماند: 
</DIV>

```
eq amsi!AmsiScanBuffer c380070057b8
```

<DIV dir="rtl" align="right">
وبعدها رح تقدر تسوي load لاي module تحتاجها.
</DIV>


![](../../img/memorypatching/readme/7.gif)


## Demo

We at Confidential Team belive in developing our own tools. You can find below a demo of our own script that patch the memory for you. The code will be shared later in 2022.

![](../../img/memorypatching/readme/8.gif)


## References

- https://rastamouse.me/memory-patching-amsi-bypass/
- https://github.com/rasta-mouse/AmsiScanBufferBypass
