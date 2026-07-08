\# Day 1 - Azure - Create SSH Key Pair



\## 📋 التاسك الأصلي (Original Task)



> The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition.

>

> For this task, create an SSH key pair with the following requirements:

> - The name of the SSH key pair should be `devops-kp`.

> - The key pair `type` must be `rsa`.



\*\*الهدف باختصار:\*\* إنشاء SSH key pair على Azure باسم `devops-kp` من نوع `RSA`.



\---



\## 🖥️ الأوامر بالترتيب (Commands in Order)



```bash

\# المحاولة الأولى (فشلت) - login مباشر بالـ username/password

az login -u "<username>" -p "<password>"



\# الطريقة اللي نجحت - Device Code Login

az login --use-device-code



\# عرض الـ Resource Groups المتاحة

az group list --output table



\# إنشاء الـ SSH Key Pair

az sshkey create --name devops-kp --resource-group kml\_rg\_main-0b36a04361cf43a4

```



\---



\## 🔍 شرح كل خطوة



\### `az login -u ... -p ...`

\*\*بيعمل إيه:\*\* بيحاول تسجيل الدخول لحساب Azure مباشرة بتمرير الـ username والـ password كـ parameters في نفس الأمر.



\*\*ليه استخدمته:\*\* الطريقة الأسرع نظريًا للـ login من غير خطوات وسيطة، ومناسبة للـ automation/scripts.



\*\*ليه فشلت:\*\* Azure CLI بيرفض الـ non-interactive login في حالات فيها MFA أو conditional access policies على الـ tenant. الرسالة اللي ظهرت:







\*\*ليه استخدمته:\*\* بيحقق المطلوب مباشرة (الاسم + النوع) من غير خطوات إضافية زي `ssh-keygen` يدوي.



\---



\## 🐛 المشاكل اللي واجهتها وإزاي حليتها



| المشكلة | السبب | الحل |

|---|---|---|

| فشل `az login -u ... -p ...` برسالة `AADSTS50126` | MFA أو conditional access policy على الـ tenant | استخدمت `az login --use-device-code` بدل الطريقة المباشرة |

| كتابة الباسورد لوحدها بالغلط في الترمينال بعد فشل أول أمر، ظهرت `command not found` | الترمينال كان لسه واقف في حالة انتظار بعد فشل الأمر السابق | تجاهلت الخطأ وبدأت من جديد بـ `az login --use-device-code` |

| كتابة أمر `az group list` كإجابة على prompt اختيار الـ subscription، ظهرت `Invalid selection` | الـ prompt بيستنى رقم أو Enter بس مش أمر كامل | ضغطت Enter عادي (subscription واحد بس متاح كـ default) وبعدين كتبت الأمر في الـ prompt العادي |



\---



\## ✅ النتيجة النهائية



اتعمل الـ SSH key pair بنجاح جوه الـ resource group `KML\_RG\_MAIN-0B36A04361CF43A4`:



```json

{

&#x20; "id": "/subscriptions/<subscription-id>/resourceGroups/KML\_RG\_MAIN-0B36A04361CF43A4/providers/Microsoft.Compute/sshPublicKeys/devops-kp",

&#x20; "location": "eastus",

&#x20; "name": "devops-kp",

&#x20; "resourceGroup": "KML\_RG\_MAIN-0B36A04361CF43A4"

}

```



\- \*\*Name:\*\* `devops-kp` ✔️

\- \*\*Type:\*\* RSA (ظاهر من الـ prefix `ssh-rsa` في الـ public key) ✔️



\---



\## 💡 اللي اتعلمته من اليوم ده

\- الفرق بين طرق الـ authentication المختلفة في Azure CLI ومتى تستخدم كل واحدة.

\- إن `az sshkey create` بيقدر يولّد key pair كامل تلقائيًا لو مش هتدّيله public key جاهز.

\- أهمية القراءة الكويسة لرسائل الأخطاء بدل ما تحاول تكرر نفس الأمر بشكل أعمى.



\## 📷 Screenshots

!\[screenshot](images/screenshot1.png)





