### First steps
Create folder for your project

`pip install pipenv`, `pipenv shell`, `pipenv install django djangorestframework django-rest-knox`

`django-admin startproject leadmanager`

`python manage.py startapp leads`

in settings.py add the app to the INSTALLED_APPS, also add 'rest_framework'

### Models
in leads/models.py
```
from django.db import models

class Lead(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(max_length=100, unique=True)
    message = models.CharField(max_length=500, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
```
in terminal `python manage.py makemigrations leads`, `python manage.py migrate`

### Creating serializers
in leads create serializers.py with this contents:
```
from rest_framework import serializers
from leads.models import Lead

# Lead Serializer
class LeadSerializer(serializers.ModelSerializer):
    class Meta:
        model = Lead
        fields = '__all__'
```
### API
create api.py in leads:
```
from leads.models import Lead
from rest_framework import viewsets, permissions
from .serializers import LeadSerializer

# Lead Viewset
class LeadViewSet(viewsets.ModelViewSet):
    queryset = Lead.object.all()
    permission_classes = [
        permissions.AllowAny
    ]
    serializer_class = LeadSerializer
```

### Create urls
In leads.py
```
from rest_framework import routers
from .api import LeadViewSet

router = routers.DefaultRouter()
router.register('api/leads', LeadViewSet, 'leads')

urlpatterns = router.urls
```
And in main urls.py
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('', include('leads.urls')),
]

```