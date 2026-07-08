\# Day 1 - AWS - Create EC2 Key Pair



\## 📌 المهمة الأصلية (Task)



كجزء من خطة فريق Nautilus DevOps للانتقال التدريجي (Incremental Migration) إلى AWS، وتقسيم المهام الكبيرة إلى مهام صغيرة قابلة للإدارة، كان المطلوب:



> إنشاء \*\*Key Pair\*\* جديد بالمواصفات التالية:

> - \*\*الاسم (Name):\*\* `nautilus-kp`

> - \*\*النوع (Type):\*\* `rsa`

> - \*\*المنطقة (Region):\*\* `us-east-1` فقط



تم تنفيذ المهمة بطريقتين للتوثيق والمقارنة:

1\. عبر AWS Management Console (GUI)

2\. عبر AWS CLI (Terminal)



\---



\## 🖥️ الطريقة 1: عبر الـ AWS Console (GUI)



\### الخطوات:

1\. الدخول على الـ Console URL المرفق مع بيانات الاعتماد (Username / Password).

2\. التأكد من أن الـ Region محدد على \*\*US East (N. Virginia) us-east-1\*\* من أعلى يمين الشاشة.

&#x20;  - \*\*ليه:\*\* أي Resource يتعمل في Region غلط هيعتبر خطأ في التاسك.

3\. الذهاب إلى خدمة \*\*EC2\*\* من شريط البحث.

4\. من القائمة الجانبية: \*\*Network \& Security → Key Pairs\*\*.

5\. الضغط على \*\*Create key pair\*\*.

6\. تعبئة البيانات:

&#x20;  - Name: `nautilus-kp`

&#x20;  - Key pair type: `RSA`

&#x20;  - Format: `.pem`

7\. الضغط على \*\*Create key pair\*\* — يتم تحميل الملف الخاص تلقائيًا، ويظهر الـ Key Pair في القائمة.



\---



\## 💻 الطريقة 2: عبر الـ Terminal (AWS CLI)



\### الأوامر بالترتيب:



```bash

\# 1. جلب بيانات الاعتماد (Access Key / Secret Key) الخاصة بالمستخدم

showcreds



\# 2. تحديد الـ Region الافتراضي لكل الأوامر القادمة

aws configure set region us-east-1



\# 3. إنشاء الـ Key Pair وحفظ المفتاح الخاص في ملف .pem

aws ec2 create-key-pair \\

&#x20;   --key-name nautilus-kp \\

&#x20;   --key-type rsa \\

&#x20;   --query 'KeyMaterial' \\

&#x20;   --output text > nautilus-kp.pem



\# 4. التأكد من إنشاء الـ Key Pair بنجاح

aws ec2 describe-key-pairs --key-names nautilus-kp



\# 5. (اختياري) تقييد صلاحيات ملف المفتاح لمنع تحذيرات SSH لاحقًا

chmod 400 nautilus-kp.pem

```



\### شرح كل أمر:



| الأمر | الغرض منه |

|---|---|

| `showcreds` | أمر خاص بالـ Lab (KodeKloud) لعرض بيانات اعتماد AWS المؤقتة المطلوبة لتسجيل الدخول أو تشغيل الـ CLI |

| `aws configure set region us-east-1` | لضمان أن كل الأوامر التالية تُنفذ في المنطقة المطلوبة بدون الحاجة لتكرار `--region` في كل أمر |

| `aws ec2 create-key-pair --key-name ... --key-type rsa` | ينشئ Key Pair جديد بنوع RSA، ويُعيد المفتاح الخاص (Private Key) في الـ output |

| `--query 'KeyMaterial' --output text` | لاستخراج نص المفتاح الخاص فقط من استجابة JSON الكاملة، بدل الحصول على كل الحقول |

| `> nautilus-kp.pem` | لتوجيه (redirect) المفتاح الخاص وحفظه في ملف بدل طباعته في الشاشة فقط |

| `aws ec2 describe-key-pairs --key-names nautilus-kp` | للتحقق من أن الـ Key Pair اتسجل فعلاً على AWS بالاسم والنوع الصحيحين |

| `chmod 400 nautilus-kp.pem` | لتقييد صلاحيات القراءة/الكتابة على ملف المفتاح، وهي ممارسة أمان قياسية مطلوبة لاحقًا عند استخدام SSH |



\---



\## ⚠️ المشاكل التي واجهتها وحلولها



\### المشكلة: تكرار اسم الـ Key Pair عند تنفيذ الطريقتين (GUI + CLI)

عند محاولة توثيق الطريقتين بنفس الاسم `nautilus-kp`، فإذا تم إنشاؤه أولاً من الـ GUI، فإن تنفيذ نفس الأمر من الـ CLI بعدها يفشل بالخطأ:








\*\*السبب:\*\* AWS لا يسمح بوجود اثنين Key Pair بنفس الاسم في نفس المنطقة (Region) على نفس الحساب.



\*\*الحل:\*\*

\- تنفيذ المهمة الفعلية المطلوبة (`nautilus-kp`) بطريقة واحدة فقط (تم اختيار CLI لأنه الأسرع والأدق للتحقق).

\- لأخذ سكرين شوت توضيحي إضافي لطريقة الـ CLI بدون التعارض مع التاسك، تم استخدام اسم مؤقت مختلف للتوثيق فقط:

```bash

&#x20; aws ec2 create-key-pair \\

&#x20;     --key-name nautilus-kp-cli-demo \\

&#x20;     --key-type rsa \\

&#x20;     --query 'KeyMaterial' \\

&#x20;     --output text > nautilus-kp-cli-demo.pem

```

\- بعد أخذ السكرين شوت المطلوب، تم حذف الـ Key Pair المؤقت لتنظيف الحساب وعدم ترك موارد زائدة:

```bash

&#x20; aws ec2 delete-key-pair --key-name nautilus-kp-cli-demo

```



\*\*الدرس المستفاد:\*\* عند الحاجة لتوثيق نفس المهمة بأكثر من طريقة (GUI/CLI) لأغراض الأرشفة أو الـ Portfolio، يُفضل تنفيذ المهمة الحقيقية مرة واحدة بالاسم المطلوب بالضبط، واستخدام أسماء مؤقتة منفصلة لأي تجارب توثيقية إضافية، مع حذفها بعد الانتهاء لتفادي تراكم Resources غير مستخدمة.



\---



\## ✅ النتيجة النهائية

\- Key Pair باسم `nautilus-kp` من نوع `RSA` تم إنشاؤه بنجاح في منطقة `us-east-1`.

\- تم التحقق من وجوده عبر الـ Console وعبر أمر `describe-key-pairs`.

\- تم توثيق العملية بالكامل بسكرين شوتات من الطريقتين (GUI + CLI).



\## 📷 Screenshots

!\[screenshot](images/screenshot1.png)


