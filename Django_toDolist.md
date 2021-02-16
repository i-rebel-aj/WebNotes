# Django To Do list Tutorial
To Do list are considered as basic web Dev project that one must do once getting started with a new framework.

### Installation
We expect you have Django set up in your machine based on the website, we currently are using Django 2.x

# Creating ToDo list application with Django
## 1. Initialize the project, in order to create a Django project use the following command
```
    django-admin startproject <project_name>
```
This will create a directory <project_name> which will have following structure

![Directory Structure]("./assets/DjangoStartprojectDirectoryStructure.JPG")

You can read about what each file does [here](https://docs.djangoproject.com/en/3.1/intro/tutorial01/)

Although we are more interested in ```manage.py``` currently. As per the documentation

```manage.py``` is a command-line utility that lets you interact with this Django project in various ways.

You can test if everything is fine by using **check** wiith the command line utility
```
    python3 manange.py check
```
It shall give output as
```
System check identified no issues (0 silenced).
```
You are good to go! Now to start the server 
```
 python3 manage.py runserver
```
This starts the server at default port 8000, you can also change this port by 
```
python3 manage.py runserver 0:3000
``` 
Here 0 shall mean the local host and 3000 is port number

After running the server, you might be getting out like
```diff
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

- You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
+ Run 'python manage.py migrate' to apply them.

Django version 2.2.18, using settings 'project_name.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
Ignore the warning in red for the time being, you will get the Django home screen in your browser.

This is how Django displays default page, now suppose instead of default page we want to display home page of our list application.

For this we need to tell django what to do when he encounters path '/' i.e localhost:8000, this is achieved via routing.

## 2. Creating List Application
Before we start we want to address a simple question what are applications and projects in Django are they same or different?

### What is a Project?
A project refers to the entire application (website) and all its parts.
### What is an app?
An app refers to a submodule of the project. It's self-sufficient and not intertwined with the other apps in the project such that, in theory, you could pick it up and plop it down into another project without any modification.
### For Example
You have a youtube clone ready with you that entire clone is a website, however ability to add comments and like em etc is an application that is submodule of the project you will be writing pretty much similar logic if you want comments in another website such as Reddit clone 

### Starting the application
Use the following command to start an app
```
 python3 manage.py startapp <app_name>
```
we kept our app_name as List

It created a directory List with structure as follows

![Directory_Image]()

### Understaning the MVT (Model View Template) functionality in Django

Django follows the MVT architecture, in short it works as follows
- Models - This is the databse model , it defines how a particular resource is defined. Example- A user will have username, password, email they will all be character string etc.
- Views - They constitute of the logic that we apply on models, example hashing the password string before it is stored in the database. 
- Template - It defines how your particular "view" or each similar resource will look on the application, example no matter who the user is on youtube the layout of homepage will be same, that is trending videos card, like subscribe button at same places etc.

### Defining views in our list app
We need to change "/" path from default django page to a page which simply says **Hello World**

#### Note- You can either have functional based views or class based views we will be using the former approach

In the views.py of your List (i.e application) directory let's define an index view.

```python
from django.http import HttpResponse
# This is view name
def index(request):
    # It simply sends a text saying, Hello from list application 
    return HttpResponse("Hello from list application")
```
You have defined the view now you will have to tell the app to use this view at a particular url.

In app directory create a file by the name of urls.py and include the following code

```python
from django.urls import path
from . import views
urlpatterns = [
    path('', views.index, name='index'),
]
```
What we are essentially doing here is we are telling the app whenever app gets '/' route **on it's app URL** go to view.index that is index funxtion in views which send back the response. 

Run the server and check if it works!

You will notice that noting has changed but why is that?

The thing is when you hit a URL it is the responsibility of your project to redirect that URL to an app after that app takes over. We have not redirected our URL to app which is List in this case and to do that go into your project directory's urls.py and change code to this.

```python
#In <project_name>/<project_name>/urls.py

from django.contrib import admin
from django.urls import path, include
#Keep the order of path in mind, it's really important
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('List.urls'))
]
```

Now basically what happens whenever a users a url it goes to project's urls.py and look for the right all, followed by which it is responsibility of the app to redirect him.

Now if you would see you will get your desired message!

In the above code segment we mentioned that order is really important, if we place ```path('', include('List.urls'))``` before admin, we will not be able to redirect to admin as all traffic in this case will de directed to List app.

### Congratulations you just did routing in Django, play around with it if you like.

## 3. What the hell is admin and migrations?

With Django you can easily manage SQL databases, by creating your own models etc, django by default uses SQLlite Database, however for our application we shall be learning how we can connect Django to Maria DB.
### Configuring Django to use Maria DB (If you want to use default database you can skip this step all together)
1. Make sure maria DB is installed on your machine, installing Maria DB [here]() 
2. Make sure to install necessary dependencies for Django to use MySQL
    ```
    sudo apt-get install python3-dev libmysqlclient-dev
    pip3 install mysqlclient
    ```
3. Create a databse by any name you like, we will use mytodolist, in mysql prompt
    ```mysql
    > CREATE DATABASE mytodolist;
    ``` 
    Note- Make sure your user has all permissions on the database.
    ```mysql
    > GRANT ALL PRIVILEGES ON mytodolist.* to 'user'@'localhost' IDENTIFIED BY '<password>';
    ```
4. Now head to <project_name>/settings.py, the setting file is responsible for managing your database,apps etc. Search for ```DATABASES``` variable and change it as follows 
    ```python
        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': 'mytodolist',
                'USER': 'user',
                'PASSWORD': 'password',
                'HOST': 'localhost'
            }
        }
    ```
### Woohoo! you have connected to the database sucessfully.

### What the hell is migration?
- Working directly with SQL can be quite cumbersome, so to make your life easier, Django comes with an object-relational mapper, or ORM for short. The ORM maps the relational database to the world of object oriented programming. 

- Instead of defining database tables in SQL, you write Django models in Python. Your models define database fields, which correspond to the columns in their database tables.

- But just defining a model class in a Python file does not make a database table magically appear out of nowhere. Creating the database tables to store your Django models is the job of a database migration. Additionally, whenever you make a change to your models, like adding a field, the database has to be changed too. Migrations handle that as well.

When you start a Django project via startproject, it provides you a admin panel to manage your site, for which there are seperate tables, to see that admin panel and tables run the following command

```
    python3 manage.py migrate
```

You will see output something like this,

![migrate_output]()

If you go into your data base now, you can see the additional tables that have been created and furthermore you can also login into admin site by visiting /admin. But first you shall create a superuser these credentials you will be using to login into admin site.

To create a superuser run the following command and fill options accordingly.
```
    python3 manage.py createsuperuser
```
We created a superuser by the name of ```admin``` with password as ```admin```.

### Yayy if you have reached here you have started to understand basics of Django, keep on going!!.


## 4. Starting with templating in Django

So now lets display some HTML pages in our project.

Create a templates directory and a static in your project root folder, i.e <project_name>/

Now you need to tell your projec that you are using templates in your project directory's ```settings.py``` change the ```TEMPLATE``` variable to look like this.
```python 
import os 
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, "templates")],
        'APP_DIRS': True,
        },
    },
]
```
File structure should look something like this. 

![File_Structure]("something")

A template in Django is basically written in HTML, CSS and Javascript in an .html file. 

Django framework efficiently handles and generates dynamically HTML web pages that are visible to end-user.

Let's say we want to display a simple index.html, when user calls our index views

in List/views.py
```python
    #Change your index method to this
    def index(request):
        return render(request, 'List/index.html')
```

Inside templates directory create a directory List (or your app name) inside it create simple html file named ```index.html``` as follows

```html
    <!--Please include head body etc on your own for the time being-->
    <h1>Hello From the list again</h1>
```

Refresh your page and voila! now you will be seeing these changes.

