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


