# Pagination

## 1. PageNumberPagination

یکی از مواردی که به پرفرمنس اپلیکیشن ما کمک میکند، صفحه بندی است؛ ما اگر بخواهیم در یک درخواست برای نمایش لیست محصولات،
ما بخواهیم همه ی این محصولات را یکجا برگردانیم که کاربر ببیند، فشار خیلی زیادی به سرور میاید، فرض کنید 1000 نفر در هر
ثانیه وارد سایت شوند و یک درخواست برای نمایش لیست محصولات ارسال کنند، دیتای خیلی زیادی واکشی میشود و یکجورایی انگار به
سرور ما اتک زده اند و عملا ما با مشکل مواجه میشویم، یکی از بهترین راه حل های حل این معضل، پیاده سازی سیستم صفحه بندی
است. دی آر اف بصورت پیش فرض، دوتا ساختار اصلی رو برای صفحه بندی درنظر گرفته است؛ خیلی ساده اند ولی میتوان کانفیگ های
خیلی متنوعی رو برایش درنظر بگیریم؛ مراحل ایجاد صفحه بندی برای پروژه به شکل زیر است:

1. به فایل ستینگ پروژه رفته، یک متغییر بنام رست فریمورک را ایجاد میکنیم که یک دیکشنری میگیرد و ما اگر بخواهیم تنظیمی را
   برای دی آر اف درنظر بگیریم، میتونیم بصورت گلوبال و پابلیک، داخل این دیکشنری پیاده سازی کنیم؛ پس تنظیمات مربوط به صفحه
   بندی را در اینجا مشخص میکنیم
2. باید آدرس کلاس داخل رست فریمورک پیجینیشن را بعنوان تنظیمات پاس بدیم به دیفالت پیجینیشن کلاس
3. مورد بعدی، باید مشخص کنیم مقدار پیج سایز چقدر باشد، یعنی در هر صفحه چند آیتم به کاربر نمایش داده شود

```
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 2
}
```

این تنظیمات فقط روی پیاده سازی با روش میکسین ها، جنریک ویو ها و ویوست ها برقرار میشود و روی آنها صفحه بندی اعمال میشود؛
و در کل روی کلاس بیس ویوها این رو داریم. در خروجی ساختار و نمایش ای پی آی های ما هم تغییر میکند، یعنی در قالب جیسان، یک
کانت اضافه کرده که تعداد کل آیتم هاست، نکست آدرس صفحه بعدی را مشخص میکند، پریویس آدرس صفحه قبلی و داخل ریزالتس دیتای
خودمان را نمایش میدهد.

```
{
    "count": 8,
    "next": "http://127.0.0.1:8000/todos/generics/?page=3",
    "previous": "http://127.0.0.1:8000/todos/generics/",
    "results": [
        {
            "id": 1,
            "title": "Todo 1",
            "content": "this is first todo",
            "priority": 3,
            "is_done": false,
            "user": 1
        },
        {
            "id": 4,
            "title": "Todo from postman",
            "content": "this is fourth todo",
            "priority": 3,
            "is_done": false,
            "user": 1
        }
    ]
}
```

## 2. LimitOffsetPagination

ساختار دیگری برای صفحه بندی است و مقداری از نظر عملکردی و کارکرد با ساختار اول متفاوت است:

```
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 2
}
```

در خروجی، موارد ساختار اول را داریم، اما تفاوت در این هست که در بخش نکست و پریویس، کوئری پارامتر عوض شده است، یعنی داره
با لیمیت و آفست بصورت کوئری پارامتر کار میکند؛ لیمیت مشخص میکند چندتا دیتا را به ما نشان دهد و آفست مشخص میکند چندتا
آیتم را پرش کند:

```
{
    "count": 8,
    "next": "http://127.0.0.1:8000/todos/generics/?limit=2&offset=4",
    "previous": "http://127.0.0.1:8000/todos/generics/?limit=2",
    "results": [
        {
            "id": 1,
            "title": "Todo 1",
            "content": "this is first todo",
            "priority": 3,
            "is_done": false,
            "user": 1
        },
        {
            "id": 4,
            "title": "Todo from postman",
            "content": "this is fourth todo",
            "priority": 3,
            "is_done": false,
            "user": 1
        }
    ]
}  
```

## تنظیم نوع صفحه بندی در ویوها

ما میتونیم در کلاس بیس ویوهای خود بیایم و مشخص کنیم که نوع صفحه بندی به چه صورتی باشد؛ مثلا:

```
class TodosGenericApiView(generics.ListCreateAPIView):
    queryset = Todo.objects.order_by('priority').all()
    serializer_class = TodoSerializer
    pagination_class = PageNumberPagination
```

و برای کلاس با پیاده سازی ویوست:

```
class TodosViewSetApiView(viewsets.ModelViewSet):
    queryset = Todo.objects.order_by('priority').all()
    serializer_class = TodoSerializer
    pagination_class = LimitOffsetPagination
```

## تنظیم پیج سایز برای هر کلاس بیس ویو

برای اینکه پیج سایز دلخواه خود را برای کلاس ویو خود مشخص کنیم خیلی راحت میتوانیم داخل همان فایل ویوز، یک کلاس جدید ایجاد
کرده و از پیج نامبر پیجینیشن ارث بری کنیم، سپس مقدار دلخواه پیج سایز را داخل کلاس تعیین میکنیم و از آنطرف در کلاس بیس
ویوی خود بجای آنکه مستقیما پیجینیشن کلاس را روی پیج نامبر پیجینیشن قرار دهیم، روی کلاسی قرار میدهیم که خودمان ساخته ایم:

```
class TodosGenericApiViewPagination(PageNumberPagination):
    page_size = 3
```

سپس در کلاس بیس ویوی خود:

```
class TodosGenericApiView(generics.ListCreateAPIView):
    queryset = Todo.objects.order_by('priority').all()
    serializer_class = TodoSerializer
    pagination_class = TodosGenericApiViewPagination
```