# Class Base View

## API for GET, POST

فانکشن بیس ویو ها گزینه ی مناسبی هستند برای زمانی که آن ساختاری که ما داریم، ساختار نسبتا ساده ای باشد و آنچنان پیچیدگی
خاصی نداشته باشد؛ اما زمانی که کدنویسی بیشتر شود و بخواهیم منطق های بیشتری را در هر کدام از دستورات گت، پست، پوت و دیلیت
پیاده سازی کنیم، یک مقدار شاید در بحث فانکشن بیس ویو ها با مشکل روبرو شویم، یعنی از نظر تمیزی کد شاید خوب نباشد و گزینه
های به مراتب بهتری هم برای وجود دارند. برای این که کد ما تمیز تر باشد، میتوانیم از ساختار کلاس بیس ویو ها استفاده کنیم
ساختار ساده و جالبی که داخل دی آر اف قرار داده شده و مقدمه ورود به مبحث ما به میکسین ها و ویو ست ها هست.

1. برای فانکشن بیس ویوها، از دکوریتور ای پی آی ویو استفاده میکردیم؛ برای کلاس بیس ویو لازم نیست این کار را انجام دهیم
2. from rest_framework.views import APIView : این ماژول را ایمپورت میکنیم تا کلاسی که ایجاد میکنیم از آن ارث بری کند
3. در کلاس بیس ویو ها ما میتوانیم از طریق متد داخل کلاس، رکوئست هارا مشخص کنیم، مثلا فانکشن گت، یعنی رکوئست از جنس گت را
   مدیریت میکنیم:

```
class TodosListApiView(APIView):
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

4. حالا آدرس ها را در فایل یوآرالز مشخص میکنیم:

`path('cbv/', views.TodosListApiView.as_view())`

در پتس، چون کلاس بیس ویو هست، باید از دستور ازویو استفاده کنیم تا جنگو متوجه ساختار آن بشود که از کلاس بیس ویو استفاده
است.

5. آدرس آن میشود:

http://127.0.0.1:8000/todos/cbv/

و ما لیست آیتم ها را داریم و همچنین میتوانیم یک آیتم جدید به مدل اضافه کنیم.

## API for GET, PUT, DELETE

برای این درخواست ها، باید یک کلاس جداگانه ایجاد کنیم، چونکه باید آیدی مدل هم دریافت کنیم و برای تمیزی کد و داشتن یک
ساختار شی گرا، باید متد دریافت آیتم توسط آیدی را در یک فانشکن جداگانه مثلا به اسم گت آبجکت ایجاد کنیم و از آن در فانکشن
های دیگر استفاده کنیم:

```
class TodosDetailApiView(APIView):
    def get_object(self, todo_id: int):
        try:
            todo = Todo.objects.get(pk=todo_id)
            return todo
        except Todo.DoesNotExist:
            return Response(None, status.HTTP_404_NOT_FOUND)

    def get(self, request: Request, todo_id: int):
        todo = self.get_object(todo_id)
        serializer = TodoSerializer(todo)
        return Response(serializer.data, status.HTTP_200_OK)

    def put(self, request: Request, todo_id: int):
        todo = self.get_object(todo_id)
        serializer = TodoSerializer(todo, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status.HTTP_202_ACCEPTED)
        return Response(None, status.HTTP_400_BAD_REQUEST)

    def delete(self, request: Request, todo_id: int):
        todo = self.get_object(todo_id)
        todo.delete()
        return Response(None, status.HTTP_204_NO_CONTENT)
```

آدرس آن هم ایجاد میکنیم:

`path('cbv/<int:todo_id>', views.TodosDetailApiView.as_view())`

و مثلا برای آیدی با شماره 4 میتوان عملیات های گت، پوت و دیلیت را انجام داد:

http://127.0.0.1:8000/todos/cbv/4