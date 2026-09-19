# Validation

ما در مدل خود یکسری اعتبارسنجی هایی رو درنظر گرفتیم، مثلا تایتل حتما باید ماکسیمم رشته 300 را داشته باشد و موارد دیگر؛
پس در این حالت اگر موارد اعتبارسنجی داخل خود کلاس مدل را رعایت نکنیم وقتی دکمه سند یا ثبت رو بزنیم، با خطا روبرو میشیم و
متن خطا هم به ما میدهد.

نکته مهم:

اگر مقدار مقدار بلنک رو در کلاس مدل روی ترو قرار بدهیم نیازی نیست مایگریشن بزنیم و در زمان ثبت اطلاعات، یک رشته خالی
قرار میدهد ولی اگر مقدار فیلدی را روی نال قرار بدیم حتما باید مایگریشن جدید ایجاد کنیم:

`content = models.TextField(blank=True)` : نیاز به مایگریشن ندارد

`content = models.TextField(null=True)` : نیاز به مایگریشن دارد

اما این سیستم اعتبارسنجی داره از روی کلاس مدل اعتبارسنجی خود را انجام میدهد، اما میتوان یک سیستمی رو طراحی کرد که براساس
نیاز های ما اعتبارسنجی را انجام دهد؛ ما میتوانیم این کار را در سریالایزر انجام دهیم.

## Create validation for priority field in Todo:

میخواهیم فقط برای فیلد اولویت، یک سیستم اعتبارسنجی پیاده سازی کنیم؛ مثلا میخواهیم مقدار اولویت فقط بین 10 تا 20 مورد
قبول باشد؛ برای اینکار باید بیایم از متدهای داخلی خود سریالایزر استفاده کنیم:

1. اسم فانشکنی که قرار هست یک فیلد مشخصی را اعتبارسنجی کند باید بصورت زیر باشد:

`validate_اسم فیلد مورد نظر`

2. ساختار کلی و پایه ای فانکشن به این صورت میشود:

```
def validate_priority(self, priority):
    return priority
```

3. حالا به راحتی بررسی میکنیم اگر مقدار فیلد بین 10 تا 20 نبود بیاد خطا بده و اجازه ثبت شدن ندهد:

```
def validate_priority(self, priority):
    if priority < 10 or priority > 20:
        raise serializers.ValidationError('Priority must be between 10 and 20')
    return priority
```

## Create validate:

میتوان یک فانکشن به اسم ولیدیت ایجاد کرد و با استفاده از ای تی تی آر اس، کل دیتا را میتونیم داخل یک فانشکن داشته باشیم و
نیازی نیست به ازای هر کدام از فیلد ها یک فانکشن ایجاد کنیم:

```
def validate(self, attrs):
    print(attrs)
    return super().validate(attrs)
```

اما از لحاظ سادگی کار بهتر هست فانکشن های اعتبارسنجی برای هر فیلد جدا نوشته شوند و اینجوری کد ما تمیزتر هم هست.

# Swagger

ای پی آی که آماده میشود، به چندین روش میتواند برای برنامه نویس فرانت قابل استفاده باشد، ما در خود دی آر اف صفحه ای رو
برای ای پی آی ها داشتیم و توانستیم دیتاها را ببینیم یا دیتای جدید ایجاد کنیم و ...، اما معمولا در کار کردن با ای پی آی
شاید این صفحه دی آر اف خیلی برای برنامه نویس فرانت آشنا نباشد، ما یکسری استاندارد هایی برای خروجی دادن ای پی آی داریم به
یک برنامه نویس فرانت؛ این استاندارد یکسری ویژگی ها و خصوصیاتی دارد که با پلتفرم های مختلفی میشه آنها را پیاده سازی کرد،
یکی از این ابزار ها سووگر هست که برای پیاده سازی یک دیزاین کاملا حرفه ای برای ای پی آی های ما است. جنگو یک پکیجی دارد که
با نصب آن، خیلی راحت میتوانیم از امکانات سووگر استفاده کنیم.

## drf-spectacular package:

`https://drf-spectacular.readthedocs.io/en/latest/readme.html`

این پکیج امکانات زیادی دارد، یکی از موارد آن پیاده سازی سووگر یوآی هست و به طریق زیر نصب و فعالسازی میشود:

1. `pip install drf-spectacular`

2. افزودن آن به اینستالد اپس در ستینگز:

`'drf_spectacular'`

3. باید داخل متغییر رست فریمورک در ستینگز اسکیما کلاس را تعریف کنیم:

`'DEFAULT_SCHEMA_CLASS': 'drf_spectacular.openapi.AutoSchema',`

4. قرار دادن تنظیمات دیگر پکیج داخل فایل ستینگز:

```
SPECTACULAR_SETTINGS = {
    'TITLE': 'Your Project API',
    'DESCRIPTION': 'Your project description',
    'VERSION': '1.0.0',
    'SERVE_INCLUDE_SCHEMA': False,
    # OTHER SETTINGS
}
```

5. برای استفاده نیاز داریم که سووگر یوآی رو داشته باشیم، یعنی یک آدرسی باید وجود داشته باشد که بتوانیم آنرا مشاهده کنیم؛
   پس آدرس های زیر را در پروژه قرار میدهیم:

```
from drf_spectacular.views import SpectacularAPIView, SpectacularSwaggerView

path('api/schema/', SpectacularAPIView.as_view(), name='schema'),
# Optional UI:
path('api/schema/swagger/', SpectacularSwaggerView.as_view(url_name='schema'), name='swagger-ui')
```

6. حال به آدرس زیر رفته و میتوانیم ای پی آی های خود را در قالب سووگر مشاهده کنیم:

`http://127.0.0.1:8000/api/schema/swagger/`

### @extend_schema:

برای حالت های پیاده سازی جنریکس، میکسینز و ویوستس در سووگر یک حالت پیش نمایش دیتا بصورت پیش فرض نمایش داده میشود، اما
اگر خواسته باشیم روی حالت های پیاده سازی دیگر هم این امکان وجود داشته باشد، میتونیم از این دکوریتور استفاده کنیم:

```
from drf_spectacular.utils import extend_schema

class TodosListApiView(APIView):
    @extend_schema(
        request=TodoSerializer,
        responses={201: TodoSerializer},
        description='this api is used for get all todos list'
    )
    def get(self, request: Request):
        todos = Todo.objects.order_by('priority').all()
        todo_serializer = TodoSerializer(todos, many=True)
        return Response(todo_serializer.data, status.HTTP_200_OK)

    def post(self, request: Request):
        serializer = TodoSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status.HTTP_201_CREATED)
        else:
            return Response(None, status.HTTP_400_BAD_REQUEST)

```