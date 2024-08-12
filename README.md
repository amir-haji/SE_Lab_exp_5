# آزمایشگاه مهندسی نرم‌افزار - گزارش آزمایش پنجم
[لینک مستندات آزمایش پنجم](https://github.com/ssc-public/Software-Engineering-Lab/blob/main/courseworks/experiments/docker-v2.md)

## اعضای گروه:

امیرحسین حاجی محمد رضایی - 99109252

علی رازقندی - 99109296

سید‌رضا قمقام - 99170542

## گزارش بخش استقرار پروژه
در ابتدا برای استقرار پروژه باید فایل‌های مربوط به داکر و همچنین تنظیمات مربوط به django برای اتصال به دیتابیس postgres را ایجاد کرد. در ابتدا برای اینکه خود django بتواند دیتابیس را تشخیص دهد و به آن متصل شود، در فایل settings.py اطلاعات مربوط به دیتابیس را به صورت زیر ایجاد می‌کنم:

```python
DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.postgresql',
		'NAME': os.environ.get('POSTGRES_NAME'),
		'USER': os.environ.get('POSTGRES_USER'),
		'PASSWORD': os.environ.get('POSTGRES_PASSWORD'),
		'HOST': 'db',
		'PORT': 5432, #default port you don't need to mention in docker-compose
	}

}

```

که در آن اطلاعات مربوط به پورت و هاست دیتابیس و همچنین اطلاعات مربوط به ادمین دیتابیس را تعیین کرده (خود این اطلاعات بعدا در یک فایل دیگر به سیستم ورودی داده می‌شود). در قدم بعدی، ابتدا فایل docker-compose.yml را با تنظیمات زیر ایجاد می‌کنم:
```
version: "3.9"
   
services:
  db:
    image: postgres
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - "8000:8000"
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
    depends_on:
      - db
```

این فایل تنظیمات بین دو سرویس و همچنین تنظیمات مربوط به خود آنها را تعیین می‌کند. یک سرویس که خود دیتابیس postgres می‌باشد و دیگری مربوط به سرویس django می‌باشد که برای این سرویس نیز اطلاعاتی را که در مورد ادمین دیتابیس نیز نیاز دارد در بخش environment به آن ایجاد می‌کنم. در قدم بعدی نیز Dockerfile برای انتقال فایل‌های کد به کانتینر و نصب کتابخانه‌های لازم برای ران کردن کد را ایجاد می‌کنم:

```
syntax=docker/dockerfile:1
FROM python:3
WORKDIR /code
COPY requirements.txt /code/
RUN pip install -r requirements.txt
COPY . /code/
```

حال در مرحله آخر برای اجرا داکر، در ابتدا با استفاده از دستور docker-compose up طبق تصوری زیر مشاهده می‌کنیم که در ابتدا Image های مورد‌نیاز هرکدام از سرویس‌ها از داکرهاب pull می‌شوند و بعد کانتینر مربوط به سیستم‌ها ایجاد شده و دستورات در فایل Dockerfile نیز اجرا می‌شوند:

![](https://github.com/amir-haji/SE_Lab_exp_5/blob/main/screenshots/1.png)

در آخر، برای ورود به سیستم نیاز است که یک سوپریوزر برای ورود به سیستم django ایجاد شود. برای اینکار ما در اینجا از اعمال دستور createsuperuser در خود کانتینر‌ها استفاده کرده‌ایم. در ابتدا می‌توان شماره کانتینری که django در آن اجرا می‌شود را با دستور docker containers ls -a پیدا کرد و بعد دستورات زیر را در محیط ترمینال آن ایجاد کرد تا این superuser ایجاد شود:

![](https://github.com/amir-haji/SE_Lab_exp_5/blob/main/screenshots/2.png)

حال بعد از این مرحله با زدن آدرس http://localhost:8000/admin با نام کاربری و رمز superuser ایجاد‌شده وارد سیستم شد. البته به طورکلی روش درست برای ایجاد superuser در این حالت این است که اطلاعات این کاربر به صورت یک فایل رمز‌شده وجود داشته باشد و در هر هنگام اجرای کانتینر این اطلاعات به صورت اتوماتیک خوانده و اجرا شود. در اینجا برای راحتی این کار به صورت دستی انجام شده است.

## درخواست زدن به سرور
در این بخش از postman استفاده میکنیم در ابتدا یک کاربر طبق خواسته سوال میسازیم
![alt text](screenshots/image.png)
سپس با این کاربر لاگین میکنیم.
![alt text](screenshots/image-1.png)
حال باید دو یادداشت خواسته شده را درست کنیم.
![alt text](screenshots/image-2.png)
![alt text](screenshots/image-3.png)
حال یادداشت های این کاربر را نشان میدهیم.
![alt text](screenshots/image-4.png)

## تعامل با داکر 
۱.
عکس زیر ایمج های این ازمایش را نشان میدهد 
![alt text](screenshots/image-5.png)
ایمج اول مربوط به بکاند جنگو است 
ایمج دوم برای دیتابیس است
ایمج سوم بیس ایمج بکاند جنگپ است که در فایل داکر آن مشخص شده است.

عکس زیر تمام کانتیر های مربوط به این پروژه را نشان میدهد.
![alt text](screenshots/image-6.png)

۲.
دستور مربوط به شاخت دیتابیس در جنگو را میزنیم. 
![alt text](screenshots/image-7.png)

## پاسخ سوالات

# سوال اول


１.	Container: 
یکی از اهدافی که سیستم‌های جدید توسعه نرم افزار دنبال می‌کنند، این است که برنامه‌ها در یک محیط، اما به صورت ایزوله و جدا از هم نگهداری شوند. به این ترتیب فعالیت آنها بر روی یکدیگر تاثیر نداشته و جدا از هم کار می‌کنند. البته اجرای این فرآیند به خاطر استفاده از پکیج ها، کتابخانه‌ها و دیگر کامپونتت‌های نرم افزاری می‌تواند پیچیده شود.
یکی از راه‌های پیاده کردن این تکنولوژی استفاده از ماشین مجازی (Virtual Machine) است که برنامه‌ها را روی یک سخت افزار اما کاملا جدا از هم نگه می‌دارد. پس در این حالت کامپوننت‌های ما تداخل خاصی با هم نداشته و رقابت برای استفاده از منابع سخت افزاری هم به حداقل می‌رسد. اما ماشین‌های مجازی مشکلاتی هم دارند. اول از همه اینکه نرم افزارهای سنگینی بوده و سخت افزار نسبتا قدرتمندی می‌خواهند. همینطور هر برنامه نیاز به سیستم عامل جداگانه دارد که ممکن است این سیستم عامل‌ها حجم‌های چند گیگابایتی داشته باشند. و اینکه ممکن است نگهداری و بروزرسانی آنها دشوار شود.
در مقابل Container قرار دارد که می‌تواند جایگزین مناسبی برای ماشین‌های مجازی باشد. Container محیط‌های اجرایی را جدا کرده و هسته سیستم عامل را به اشتراک می‌گذارد. حجم آنها معمولا به مگابایت بوده و نسبت به ماشین‌های مجازی از منابع کمتری استفاده می‌کند. همینطور برخلاف ماشین‌های مجازی که برای اجرا نیاز به زمان نسبتا زیادی دارند، Containerها بلافاصله اجرا می‌شوند.
زمانی که Container را با ماشین مجازی مقایسه می‌کنیم یعنی با یک شبیه ساز طرف حساب هستیم. اما دقیقا چه چیزی را شبیه سازی می‌کنیم؟ برای درک بهتر موضوع بهتر است از یک مثال استفاده کنیم. فرض کنید در شرکتی مشغول به کار هستید و ناهار خود را هر روز در خانه درست کرده و آن را داخل یک ظرف به شرکت می‌برید تا آنجا میل بفرمایید. دیگر لازم نیست داخل شرکت شروع به پختن غذا کنید چون احتمالا زمان زیادی را از شما می‌گیرد. کار Container هم تا حدودی شبیه به این است. شما پروژه خود را (غذا) داخل Container (ظرف غذا) قرار داده و آن را هر کجا که دوست داشتید (مثلا شرکت) می‌برید.



２.	Docerfile:
هر Container داکر با یک فایل داکر شروع به کار می‌کند. Dockerfile یک فایل متنی بوده که داخل آن با یک سینتکس ساده و قابل فهم دستورالعمل‌های ساخت Docker Image قرار داده شده است (کمی جلوتر این مفهوم را بررسی خواهیم  کرد) این فایل اطلاعات بسیار مهمی را در برمی گیرد که برای راه اندازی داکر استفاده از آنها ضروری است. در واقع Dockerfile مشخص می‌کند که پشت Container ما چه سیستم عاملی قرار بگیرد، همینطور از چه زبان ها، متغیرهای محلی، پورت‌های شبکه یا غیره استفاده شود. و مهم‌تر از همه اینکه مشخص کند Container ما بعد از اینکه واقعا اجرا شد قرار است چه کاری انجام دهد.


３.	Image:
زمانی که کار نوشتن Dockerfile را تمام کردید، یک قابلیت به اسم Docker Build را فراخوانی می‌کنید که وظیفه دارد یک Image بر اساس محتویات Dockerfile شما بسازد. Dockerfile شامل یک سری دستورالعمل برای ساختن یک Image است، در حالی که Docker Image یک فایل قابل حمل است که شامل یک سری دستورالعمل بوده که مشخص می‌کند Container کدام کامپوننت‌های نرم افزاری را اجرا کند و اینکه چطور آنها اجرا شوند. به احتمال زیاد Dockerfile بخواهد تعدادی فایل را از مخزن‌های مختلف (Repository) دانلود کند و اینجا باید به طور واضح مشخص کنید که کدام نسخه‌ها دریافت شوند. همینطور Image ساخته شده استاتیک می‌باشد، یعنی یک بار ساختن آن کافی بوده و نیازی به تغییر آن ندارید. همانطور که از اسم آنها می‌توانید حدس بزنید، Image یک تصویر از سیستم عامل اصلی می‌باشد
.


# سوال دوم

کاربرد اصلی کوبرنتیس پیاده‌سازی و مدیریت آسان برنامه‌های کانتینر شده است. اگر به‌دنبال بهینه‌سازی فرایند توسعه اپ برای ابر باشید، این ابزار پلتفرم قدرتمندی را برای زمان‌بندی و اجرای کانتینرها در کلاسترهای ماشین مجازی یا فیزیکی دراختیارتان می‌گذارد.
Kubernetes در پیاده‌سازی کامل زیرساخت مبتنی‌بر کانتینر در محیط توسعه به شما کمک می‌کند. ازآن‌‌جا‌‌که این پلتفرم برای خودکارسازی تسک‌های عملیاتی طراحی شده است، به شما اجازه می‌دهد تا بسیاری از کارهای دیگر را نیز روی کانتینرها انجام دهید. توسعه‌دهندگان با ابزارهای خاص الگوهای کوبرنتیس می‌توانند برنامه‌های ویژه زیرساخت ابری را به‌عنوان پلتفرم ران‌تایم تولید کنند. دیگر کاربردهای کوبرنتیز عبارت‌اند از:


•	هماهنگ‌سازی کانتینرها در چندین سیستم میزبان

•	کنترل و خودکارسازی روند استقرار و به‌روزرسانی اپلیکیشن‌ها

•	ارتقای حافظه برای اجرای اپ‌های حالتمند (Stateful)

•	افزایش آنی مقیاس اپ‌های کانتینری و منابع آن‌ها

•	اطمینان از اجرای صحیح و دقیق اپ‌های مستقر

•	بررسی و اصلاح خودکار اپ‌ها با قابلیت‌های ارتقا و مقیاس‌پذیری خودکار
کوبرنتیس برای ارائه کامل این خدمات به پروژه‌های متن‌باز دیگر اتکا دارد. تعدادی از این اپ‌ها بدین‌شرح‌اند:

•	رجیستری ازطریق پروژه‌هایی مثل Docker Registry

•	شبکه‌سازی ازطریق پروژه‌های OpenvSwitch و مسیریابی لبه هوشمند

•	تله‌متری ازطریق Kibana و Hawkular و Elastic

•	امنیت ازطریق پرو‌ژه‌هایی مثل LDAP ،SELinux ،‌RBAC و OAUTH با لایه‌‌های چندمستأجری (Multitenancy)

•	خودکارسازی با افزودن پلی‌بوک‌های Ansible برای نصب و مدیریت چرخه عمر کلاستر


ارتباط با داکر:
داکر  هم ابزاری متن‌باز برای مدیریت کانتینرهاست و طرفداران زیادی دارد. نسخه متن‌باز داکر که Community Edition نیز نامیده می‌شود، کاملاً رایگان است؛ اما در‌کنار آن نسخه‌ای پولی به نام Enterprise Edition هم ارائه شده است که امکانات اضافه‌ای برای مدیریت کانتینرها و پشتیبانی دارد.

قابلیت‌های این دو ابزار تا حدودی متفاوت است؛ اما درمجموع، داکر از‌نظر امکانات یک رده پایین‌تر از Kubernetes قرار دارد. بسیاری Kubernetes را به‌دلیل ویژگی ماژولار و انعطاف‌پذیرتری بیشتر و سازگاری بیشتر با نیازهای سرویس‌دهنده‌های وب ترجیح می‌دهند.

داکر از‌نظر امنیت در سطح بهتری قرار دارد و کوبرنتیس از اکوسیستم متنوع‌تری برای مدیریت کانتینرها برخوردار است. برای مثال، کوبرنتیس می‌تواند از داکر برای افزایش قابلیت‌های مدیریتی و به‌عنوان ران‌تایم کانتینر استفاده کند.

وقتی Kubernetes یک پاد را برای گره زمان‌بندی می‌کند، سرویس Kubelet در آن گره فرمان راه‌اندازی کانتینرهای خاصی را به داکر می‌دهد. سپس، Kubelet به‌صورت پیوسته وضعیت آن کانتینرها را از داکر دریافت و در سطح کنترل جمع‌آوری می‌کند. داکر کانتینرها را به درون نود هدایت می‌کند و آن‌ها را شروع و خاتمه می‌دهد. بدین‌ترتیب، استفاده از کوبرنتیز با داکر بخشی از وظایف ادمین را به سیستم محول می‌کند.
