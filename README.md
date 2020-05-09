### Python in PyCharm

[Step 1. Create and run your first Python project](https://www.jetbrains.com/help/pycharm/creating-and-running-your-first-python-project.html)

[Step 2. Debug your first Python application](https://www.jetbrains.com/help/pycharm/debugging-your-first-python-application.html)

[Step 3. Test your first Python application](https://www.jetbrains.com/help/pycharm/testing-your-first-python-application.html)


### Django in PyCharm

[Step 4. Create and Run your first Django project](https://www.jetbrains.com/help/pycharm/creating-and-running-your-first-django-project.html)

[Configure PyCharm for Python/Django](https://medium.com/@srijan.pydev_21998/configure-pycharm-for-python-django-and-introduction-to-django-rest-framework-f9c1a7cb4ba0)


Now go to Tools -> Run manage.py Task… Here you can run all the manage.py commands just by typing in their [options]. Run 

```bash
makemigrations 
```

inside manage.py@YourApp.


Now run 

```bash
migrate 
```

to create schema in your SQLite database. 


Press the play button on the top to start your server. This is same as running below command on terminal: 

```bash
python manage.py runserver
```


Open localhost in your browser to see your working demo app.


Now, let’s run the app in PyCharm’s Debug Mode by pressing the bug symbol on the top.



### Django Rest Framework in PyCharm

[Introduction to Django Rest Framework](https://medium.com/@srijan.pydev_21998/configure-pycharm-for-python-django-and-introduction-to-django-rest-framework-f9c1a7cb4ba0)


Install Django Rest Framework. Run below command:

```bash
pip install django-rest-framework
```


Don’t forget to add ‘rest_framework’ in the list of installed apps in settings.py


Create a Test Model in models.py.

## models.py


```bash

from django.db import models

# Create your models here.


class Tech(models.Model):
    short_name = models.CharField(max_length=10)
    name = models.CharField(max_length=200)
	
```


Create a file named serializer.py inside your app directory and make a model serializer.

## serializer.py

```bash

from rest_framework import serializers
from .models import Tech


class TechSerializer(serializers.ModelSerializer):

    class Meta:
        model = Tech
        fields = '__all__'

```


Now, write two class based views inside views.py. One which returns a list of all the entries and the second which returns a single entry.

	- List API View — Inherit from this class to list all the entries. Also, you can use this view to create a post function which will allow to create new entry in the database.
	
	- API View — Inherit from this class if you want to perform CRUD operations (Create, Read, Update and Delete). I used this class to read and delete entries from the database.


## views.py

```bash

from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from rest_framework.generics import ListAPIView
from .models import Tech
from .serializer import TechSerializer
# Create your views here.


class AllTech(ListAPIView):

    queryset = Tech.objects.all()
    serializer_class = TechSerializer

    def post(self, request, format=None):
        serializer = TechSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class TechView(APIView):

    def get(self, request, pk, format=None):
        try:
            tech = Tech.objects.get(pk=pk)
            serializer = TechSerializer(tech)
            return Response(serializer.data)
        except:
            return Response(status=status.HTTP_404_NOT_FOUND)

    def delete(self, request, pk, format=None):
        tech = Tech.objects.get(pk=pk)
        tech.delete()
        return Response(status=status.HTTP_200_OK)

```
	
	
Now add routes for your views in urls.py. Note that now we don’t have url function instead we have path and re_path in Django 2.0.


## urls.py

```bash

from django.urls import path, re_path
from django.contrib import admin
import polls.views as test_app

urlpatterns = [
    path(r'admin/', admin.site.urls),
    path(r'', test_app.AllTech.as_view()),
    re_path(r'(?P<pk>\d+)', test_app.TechView.as_view()),
]

```


Start your server in Debug mode again and visit your localhost in your browser to see Django Rest List API view. Add some test entities.


## Note: In case of any error, run below command inside manage.py@YourApp and re-run the app: 

```bash
makemigrations 
```

```bash
migrate 
```



