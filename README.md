### First steps
We start with configuring Django react framework

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

### Frontend

So now we gonna work with the rest api for frontend

Create a new app called frontend with `python manage.py startapp frontend`, and add it to `settings.py`

We also want to create theese `mkdir -p .\frontend\src\components`, `mkdir -p .\frontend\static\frontend`, `mkdir -p .\frontend\templates\frontend`

Node.js needs to be installed

And now we gonna install all the webpack and stuff

Next thing we wanna do, in root dir run `npm init -y` which creates a json file with all your javascript dependencies

Also `npm i -D webpack webpack-cli`, `npm i -D @babel/core babel-loader @babel/preset-env @babel/preset-react babel-plugin-transform-class-properties`, `npm i react react-dom prop-types`

Next, in the root create file `.babelrc`
```
{
    "presets": ["@babel/preset-env", "@babel/preset-react"],
    "plugins": ["transform-class-properties"]
}
```
In the root create `webpack.config.js`
```
module.exports = {
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: "babel-loader"
                }
            }
        ]
    }
}
```

Now inside `package.json` we want a couple scripts, so delte the test script and put this in
```
"scripts": {
    "dev": "webpack --mode development ./frontend/src/index.js --output ./frontend/static/frontend/main.js",
    "build": "webpack --mode development ./frontend/src/index.js --output ./frontend/static/frontend/main.js"
  },
```
Inside `frontend/src` create `index.js` and all we gonna do in this file is load app componenet `import App from './components/App';`

And in `frontend/src/components` create `App.js`
```
import React, { Component } from 'react';
import ReactDOM from 'react-dom';

class App extends Component {
    render() {
        return <h1>React App</h1>
    }
}

ReactDOM.render(<App />, document.getElementById('app'));
```

Now in `frontend/templates/frontend` create `index.html`, we wanna use bootstrap, so go to bootswatch.com and pick a theme there, and also go to bootstrap.com --> get started and copy the 3 JS files there
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <link rel="stylesheet" href="https://bootswatch.com/4/cosmo/bootstrap.min.css">
    <title>Lead Manager</title>
</head>
<body>
    <div id="app"></div>
    {% load static %}
    <script src="{% static 'frontend/main.js' %}"></script>
    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>
</body>
</html>
```
there is no main.js yet, but it's gonna be created when we run npm run dev

Go to the `frontend/views.py` to point to the templates folder
```
from django.shortcuts import render

def index(request):
    return render(request, 'frontend/index.html')
```
And link a url to it, so create urls.py in frontend app
```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index)
]
```
Finally add it to the main urls.py and make sure to add it above the leads.urls
```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('', include('frontend.urls')),
    path('', include('leads.urls'))
]
```

There we go `npm run dev`

If all worked lets create a `Header.js` in `frontend/src/components/layout`, as well go to bootstrap -> get started -> components -> navbar and copy "Hidden brand" (to create React template type rce and "Tab")
```
import React, { Component } from 'react'

export class Header extends Component {
    render() {
        return (
            <nav className="navbar navbar-expand-sm navbar-light bg-light">
                <button className="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarTogglerDemo01" aria-controls="navbarTogglerDemo01" aria-expanded="false" aria-label="Toggle navigation">
                    <span className="navbar-toggler-icon"></span>
                </button>
                <div className="collapse navbar-collapse" id="navbarTogglerDemo01">
                    <a className="navbar-brand" href="#">My Nigga</a>
                    <ul className="navbar-nav mr-auto mt-2 mt-lg-0">
                    
                    </ul>
    
                    
                </div>
            </nav>
        )
    }
}

export default Header
```

And import it in the App.js `import Header from './layout/Header';`

Also in App.js change the return to 
```
...........
class App extends Component {
    render() {
        return(
            <Header />
        )
    }
...........
```
Note that you have to run `npm run dev` every time you change smth in theese files to see changes