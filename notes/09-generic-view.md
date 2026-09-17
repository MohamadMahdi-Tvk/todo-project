# Generic View

این جنریک ویو ها میان مفهوم میکسین ها را داخل دی آر اف رو تکمیل تر میکند و باعث میشود کدنویسی ما باز هم کمتر شود یعنی ما
حتی میتوانیم فانشکن های خود مثل گت و پست داخل کلاس را برداریم و این به کمک جنریک ها قابل پیاده سازی است.

1. کلاس جدید ایجاد میکنیم و این بار میایم از جنریکس ارث بری میکنیم و اگر . رو بزنیم به نوع های مختلف درخواست دسترسی
   داشته باشیم، مثلا اگر لیست به همراه ایجاد آیتم جدید بخواهیم از لیست کریت ای پی آی ویو استفاده میکنیم یا اگر فقط لیست
   رو بخواهیم از لیست ای پی آی ویو استفاده میکنیم و هرنوع که بخواهیم را در خودش دارد.
2. درون کلاس فقط لازم هست کوئری ست و کلاس سریالایزر خود را معرفی کنیم و چیز دیگری لازم نیست.
```
class TodosGenericApiView(generics.ListCreateAPIView):
    queryset = Todo.objects.order_by('priority').all()
    serializer_class = TodoSerializer
```

3. افزودن آدرس:

`path('generics/', views.TodosGenericApiView.as_view())`

4. برای درخواست های گت بای آیدی، پوت و دیلیت:

```
class TodosGenericDetailApiView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Todo.objects.order_by('priority').all()
    serializer_class = TodoSerializer
```

5. آدرس آن:

`path('generics/<pk>', views.TodosGenericDetailApiView.as_view())`