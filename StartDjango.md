###Guide to Creating a Django Project (OS X)

Key: (*Questions*)

--------------------------------------------------------------------------------

####Initial Setup:

#####Start Postgres from Terminal:
(Only required after reboot since I closed the terminal window I used to install it initially.)

From a terminal window, enter:

  `pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start`

Postgres is a database which will exist in any regular development environment, but has to be initialized in Mac OS.


#####Create Virtual Environment:
From a terminal window in the directory where you want the Virtual Environment to exist, type:

  `python3 -m venv [your virtual environment name]`

Virtual environments are used to isolate the set of tools used by your app and the set of risks that opens up due to running your app, so that it is compartmentalized in your computer.


#####Run Virtual Environment:
From the same terminal window where your venv folder is, type:

  `source [your virtual environment name]/bin/activate`

You will know its working when your terminal path starts with `([your virtual environment name])`


#####Stop Virtual Environment:
From a terminal window where your venv folder is, type:

  `deactivate`


#####Check Virtual Environment Installations:
From a terminal window with your virtual environment activated, type:

  `pip freeze`

Or:

  `pip3 freeze`

pip3 always uses python3, whereas pip will use the default version of python on the OS.


#####Install Django:
From a terminal window with your virtual environment activated, type:

  `pip3 install django`

This is usually not necessary, as django-toolbelt will also install django:

  `pip3 install django-toolbelt`

Other tools, if needed, can also be installed this way.


#####Saving a virtual environment's installed programs:
From a terminal window with your virtual environment activated, type:

  `pip3 freeze > [name of requirements doc].txt`

This can then be used to install programs with:

  `pip3 install -r [name of requirements doc].txt`

'-r' is the flag for a set of requirements in pip3 install


#####Starting a Django Project:
From a terminal window with your virtual environment activated, type:

  `django-admin startproject [project name]`

Project name can be any combination of numbers, letters, and underscores


#####Starting an Application:
Navigate into the project folder with:

  `cd [project name]`

This directory contains the 'manage.py' file that you need to execute the command to start an app.

From this terminal window, type:

  `python3 manage.py startapp [app name]`

App name can be any combination of numbers, letters, and underscores, and cannot be the same as the project name or another app.


#####Updating Settings:

  `INSTALLED_APPS:`

  Open the settings.py folder.

  Add '[app name]' to the INSTALLED_APPS list and include a trailing comma.

  ```python
  DATABASES:
  Edit DATABASES to your local Postgres server:
    DATABASES = {
  	'default': {
  		'ENGINE': 'django.db.backends.postgresql_psycopg2',
  		'NAME': '[name of database]',
  		'USER': 'altheasmith',
  		'PASSWORD': 'babybills',
  		'HOST': '127.0.0.1',
  		'PORT': '5432',
  		}
  	}
  ```


#####Create the database in Postgres:
From a terminal window with your virtual environment activated, type:

  `createdb [name of database]`

This initializes the database within Postgres that will be used for your project.


#####Delete the database in Postgres:
From a terminal window with your virtual environment activated, type:

  `dropdb [name of database]`

--------------------------------------------------------------------------------

####Create necessary files and directories:

#####Create urls.py in the new app folder:
From the new app folder with virtual environment activated, type:

  `touch urls.py`

Alternatively, this file can be copied over from the main project folder:

  `cp [project name]/urls.py [app name]`


#####Create directory for html templates in new app folder:
From the new app folder with virtual environment activated, type:
  ```bash
  mkdir templates
  cd templates
  mkdir [app name]
  ```
Move into this directory and create the necessary html files:
  ```bash
  cd [app name]
  touch index.html
  ```


#####Create forms.py in the new app folder:
From the new app folder with virtual environment activated, type:

  `touch forms.py`

--------------------------------------------------------------------------------

####Models & Forms:

#####Models:
The models.py file should always be headed by:
  ```python
  from django.db import models
  ```
Models are built as classes inheriting from models.Model:
  ```python
  class Class(models.Model):
  ```
Each named attribute of the class is categorized as a django models field from this list:
  [Django Models Fields](https://docs.djangoproject.com/en/1.8/ref/models/fields/)
E.g.:
  ```python
  text = models.TextField()
  ```
Foreign Keys are saved as field_id in the database.

E.g.:
  ```python
  user = models.ForeignKey('users.User')
  ```
The field in the database is 'user_id'.


#####Forms:
The forms.py file should always be headed by:
  ```python
  from django.forms import ModelForm
  ```
All models to be used in forms should also be imported from [app name].models.

Forms are built as classes inheriting from ModelForm:
  ```python
  class ComplimentForm(ModelForm):
  ```
Their structure is stored in a secondary class: class Meta *(WHAT is Meta and why do we use it?)*
  ```python
  class ComplimentForm(ModelForm):
    class Meta:
  ```
The fields included in the form are:
  1. The model being used to build the form;
  2. The fields or attributes of the model class;
  3. Any widgets to be used in the form, e.g., HiddenInput()

For example:
  ```python
  class ComplimentForm(ModelForm):
    class Meta:
        model = Compliment
        fields = ['text','user']
        widgets = {
            'user': HiddenInput(),
        }
  ```
  
--------------------------------------------------------------------------------

####Migrate & Run:

#####Once the model is created, Migrate:
From a terminal window in the root folder with the virtual environment activated, type:
  ```bash
  python3 manage.py makemigrations [app name]
  python3 manage.py migrate [app name]
  ```
For both of these, the [app name] is optional - if it is provided it will makemigrations and migrate only that app, and if not, it will do the same for all apps int he project.


#####Run the server:
From a terminal window in the root folder with the virtual environment activated, type:

  `python3 manage.py runserver`

`Ctrl + click` on URL indicated in the set up will navigate to the page.

`Ctrl + C` will stop the server.

To make edits with the server running, open another terminal window to the project directory.

--------------------------------------------------------------------------------

####Server Flow:

#####Create URL in urls.py:
The urls.py should include:
  ```python
  from django.conf.urls import include, url
  from django.contrib import admin
  ```
A url() function can either be set up to include another set of URLs, e.g. an app's urls:
  ```python
  url(r'^url/', include('url.urls')),
  ```
Or to call a view:
  ```python
  url(r'^add/(?P<user_id>[\d]+)/post$', 'compliments.views.post', name='post'),
  ```
It requires an initial regex which matches it with URLs for which to run views.
The regex can also identify variables with (?P<[variable name]>[[variable regex]]).
For example, the following identifies a variable called 'user_id' which consists of one or more digits:
  ```html
  (?P<user_id>[\d]+)
  ```
The second argument is the name of the view, and the third is the name of the the url.
The name is often the same as the name of the page.
Always use a comma after every entry in urlpatterns = []


#####Create View in views.py:
The views.py file should always be headed by:
  ```python
  from django.shortcuts import render, redirect
  ```
Views are referenced as the second argument in a url, as '[app name].views.[view name]'
Views are defined in views.py as functions:
  ```python
  def post(request, user_id):
    Compliment.objects.create(
        text = request.POST['text'],
        user_id = user_id,
    )
    return redirect('/compliments/{}'.format(user_id))
  ```
The first argument is "request". *(WHY? and what does this do?)*

The rest are parameters/variables passed to the rendered html template (e.g. user_id).

Views can run logic, and then end with either a render or a redirect.

Render is used for running view functions straight to html templates.

Redirect is used when an action has been taken on the page.

This requires the user to be redirected to another page after the action is complete.

######Render:
At the end of the view function, the render syntax is:
  ```python
  return render(request, '[app name]/[template name].html', {context dictionary: of passed parameters})
  ```

######Redirect:
At the end of the view function, the redirect syntax is:
  ```python
  return redirect('redirectto/thisurl')
  ```
Instead of rendering a template directly, redirect sends the process back through the urls -> views process to end with a rendered template.


#####HTML Templates:
Templates are written in HTML, but can include Python variables for added functionality.

These variables are passed in the view functions as the third argument: the dictionary of parameters.

Each template should begin as:
  ```html
  {% load staticfiles %}
  <html>
    <head>
      <!--CSS: <link rel="stylesheet" type="text/css" href="{% static '.css' %}"> -->
    </head>
    <body>
      <!--Content-->
    </body>
  </html>
  ```


######Using Variables in HTML Templates:


######Using Forms in HTML Templates:


--------------------------------------------------------------------------------

####Other Specifics:

######HiddenInput:
- widget in Form
- Initialize in context dictionary in render()
