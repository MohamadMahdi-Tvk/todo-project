# Django Rest Framework

## REST (Representational State Transfer):

در رست، 4 نوع متد اصلی داریم، برای اینکه بتوانیم ارتباط خودمان را با قسمت بک اند ایجاد کنیم:

get: برای واکشی اطلاعات استفاده میشود
post: برای ایجاد یک اطلاعات جدید استفاده میشود
put: برای ویرایش اطلاعات استفاده میشود
delete: برای حذف اطلاعات استفاده میشود

## Install Django Rest Framework:

1. in terminal : pip install djangorestframework
2. add to INSTALLED_APPS in settings.py : 'rest_framework'
3. add to urlpatterns : `path('api-auth', include('rest_framework.urls'))` in urls.py
   and in url in web browser : http://127.0.0.1:8000/api-auth/login/

مورد سه برای این هست که ما به راحتی بتوانیم ای پی آی های خودمان را تست کنیم
و جنگو اومده همان ای پی آی ها را در قالب ویو برایمان ساخته است، یعنی عملا این آدرس هایی که برای ما
ساخته میشوند، در پس زمینه میتوانیم خیلی راحت با ساختار رست از آنها استفاده کنیم
این یکی از قدرت های جنگو و جنگو رست فریمورک هست.
