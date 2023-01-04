---
layout: post
title: django project
subtitle: getting started
tags: [django]
date: 29-12-2022 
---
Lets directly get down to business and get our hands dirty. The intention of this article is to understand the basics not just creating a Django project. There are a lot of tools out there for creating a sample Django project instantly.

Lets directly get down to business and get our hands dirty. The intention of this article is to understand the basics not just creating a Django project. There are a lot of tools out there for creating a sample Django project instantly.

**Step 1: Create a Virtual Environment**

I assume you already have python running in your environment So, the next obvious step is to install Django. Before that, it will better if we create a virtual environment so that we can isolate this project with other projects of us and it also helps us in maintaining different versions of dependencies for different projects

Creating a **virtual environment** is very simple.

```
pip install virtualenvvirtualenv djangosource django/bin/activate
```

The above code will install **virtualenv,** create a virtual environment called **django** and activate it which means we are inside the virtual environment now and our project which we will be creating will be isolated. If you want to get out of this environment just use the command **deactivate**

**Step 2**: **Installing Django**

The next step is to install **Django**

```
pip install django
```

This will install Django. Now you are all set.

img1

Now let's go ahead and create our first Django project

```
django-admin startproject sampledjango
```

The above code will create a **Django Project** with a name as **sampledjango** (I am bad with names 😓) for you with a base structure. Yes, this command will take care of everything and we need not sweat at all. By the way, you will be wondering what **django-admin** is…it is Django’s command-line utility for administrative tasks

```
django-admin help --commands
```

This will list out all the commands possible like below and also you can use the help command to check out what each command does.

img2

If you want to understand how Django internally executes these commands you can look at the respective codes inside the Django folder under the core directory. It is not required, but it will be good if you can explore these folders and files for a better understanding of how this is done.

**Step 3**: **Understanding the structure of the Project**

Now let's look at our project folder and see what files it contains and what they do.

img3

This is how our project structure will look like. Let’s understand the importance of each file

**./manage.py:** It’s an alternative to **django-admin** both of them are the same and is used to run the server, run tests, make migrations, edit migrations, e.t.c

**settings.py:** It is the settings file where we store the configuration related to the project like secret keys, db cluster settings, e.t.c

**urls.py**: This file holds the routes and other mappings. this is the trigger point for your API

**wsgi.py**: This is the file that holds the configuration of the WSGI. Now, you must be wondering what **WSGI** is it stands for **Web Server Gateway Interface** as the name suggest it acts as middleware or interface between the webserver and the Django application.

```
WSGI is the Standard and uWSGI is the implementation
```

I have observed that a lot of people get confused here

So, this is how the flow looks like

```
Web Client < --- > Web Server < --- > WSGI Server < --- > Django App
```

There a couple of popular WSGI servers available like **uWSGI** and **gunicorn**

To give you a little info on why we are using this

There is no way where a web server can directly communicate with python applications. So the community came up with a standard called WSGI which has to be implemented on both web servers and python frameworks. We need not actually worry about it right now.

**Step 4**: **Launching the Application**

Since our setup is done and we understand what our default project looks like, let’s go ahead and run the Django server

```
./manage.py runserver
```

The server runs on the default port 8000. So go to the browser and try accessing [**_http://localhost:8000/_**](http://localhost:8003/)**_._** _If you see a success message then we have_ **_successfully run our first Django Project_**_. If you want to run it on a different port use the below format_

```
./manage.py runserver 8005
```

**Step 5: Creating an app**

Since we got our project running now let’s create an app called **User.** So, what is an app? You can say that the combination of apps is a Django Project.

Each app either can be totally isolated with other apps or can have some dependencies or relations with other apps in the project but I would highly suggest you keep it as decoupled as possible.

```
./manage.py startapp User
```

The above command will create an app **User** with some files in it.

img4

**admin.py**: This file is for configuring admin related stuff of the app

**apps.py**: This file is for configuring settings related to this app itself

**migrations**: This folder consists of migration files that are helpful in creating tables in sequential i.e basically it’s kind of log on how the table was modified.

**models.py**: This file contains models which are basically classes which are used to create and communicate with database tables through Django’s inbuilt **ORM**

**tests.py**: This file is to write test cases related to this app itself

**views.py**: This is the main file which we should be focusing much on this file will contain all the functions or classes which will be our API’s

Now since we have created our app let’s go ahead and configure this with the project. To do this we need to add the app in **_INSTALLED\_APPS_**

```
INSTALLED_APPS = [    'django.contrib.admin',    'django.contrib.auth',    'django.contrib.contenttypes',    'django.contrib.sessions',    'django.contrib.messages',    'django.contrib.staticfiles',    'User']
```

As you can there are already few apps already listed these are Django internals.

**Step 6**: Creating a Model

Let’s create a User Model with a few basic attributes

```py
# -*- coding: utf-8 -*-
from __future__ import unicode_literals

from django.db import models


# Create your models here.

class User(models.Model):
    first_name = models.CharField(max_length=256)
    last_name = models.CharField(max_length=256)
    email = models.CharField(max_length=256, default='', db_index=True, unique=True)
    created_on = models.DateTimeField(auto_now_add=True)
    modified_at = models.DateTimeField(auto_now=True)

    def __unicode__(self):
        return u'{}-{}'.format(self.email, self.get_user_name())

    def to_json(self):
        return {
            'id': self.id,
            'name': self.get_user_name(),
            'email': self.email
        }

    def get_user_name(self):
        return self.first_name + ' ' + self.last_name
```

Since we have created a model that represents a table, the next step would be to create tables accordingly in our database.

So how are we going to do it…… Good News! Django takes care of it and all we need to do is to run the below commands to create db tables.

```
./manage.py makemigrations User./manage.py migrate
```

The F**irst** line will create a migration file by Django which are basically commands on how to convert the model into a database table.

The **Second** line will execute the commands and creates a table called User with the given attributes and conditions.

Once these two commands run successfully we are good to go to the next step.

**P.S**: If you want to understand more about different types of Django fields available.

**Step 7**: URL Patterns

So, when a request reaches the application how does it route to its appropriate view or function?

That’s where we will be maintaining a mapping called **urlpatterns** in the urls.py which is the main file for routing URLs. So, what Django does when it receives a request is it runs through each URL pattern in order and stops at the first one that matches the requested URL(can be regex or exact match and if you want it to be exact match use **path**).

It’s highly recommended to write app-specific URL's within the app and include them in the main urls.py file like below

**Step 8**: Designing Views

Once the URL is matched it executes the function mapped to it. There are three types of views possible

1.  Function-Based Views
2.  Class-Based Views

**Function-Based Views**:

Let’s create a function-based view for fetching user’s account balance

This is just a sample view so if you want to create and update or delete using the same URL pattern you will have to youse **request.method** inside the view to route accordingly

**Class-Based Views**:

Django already has an inbuilt View class and this takes care of routing and other stuff. It’s always recommended to use Class-Based views over Function-Based as Class-Based views are more Object-oriented, extensive and can be reused across the project.

All we need to do is to inherit the View Class as below and also the way we call the class-based views are also differs

There are also few in-built Django generic class-based views that we can use or it’s always recommended to create our own **Base View** class which we can be used across different apps.

Since we are done creating models, views and URLs let’s see them in action. Run the server in 8005 port and check the outputs of the below

```
http://localhost:8005/userhttp://localhost:8005/user/balance
```

**Conclusion**:

There you go You have created your first working Django project. Now all you need to do is to try creating some models and views and explore the core code of Django and understand the flow from request to response.

I am also attaching the code for reference

There is a lot more to understanding in Django like ORM, Logging, Tests, Admin-Site and deployment. Like I said below the above setup is for local but creating a live Django project we need to have WSGI and NGINX servers.

If you want articles for all these topics please let me know in the comment section. Thanks for reading.

Edit: Here is an article on how to log in Django