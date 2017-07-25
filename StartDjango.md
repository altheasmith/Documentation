### Guide to Creating a Django Project (OS X)

Key:
*(?Questions?)*
####### Section In Progress

--------------------------------------------------------------------------------

#### Initial Setup:

##### Start Postgres from Terminal:
(Only required after reboot since I closed the terminal window I used to install it initially.)

From a terminal window, enter:

  `pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start`

Postgres is a database which will exist in any regular development environment, but has to be initialized in Mac OS.

###### Configure Postgres to start Automagically on boot:
From [this site](http://exponential.io/blog/2015/02/21/install-postgresql-on-mac-os-x-via-brew/):
  ```bash
  mkdir -p ~/Library/LaunchAgents
  ln -sfv /usr/local/opt/postgresql/*.plist ~/Library/LaunchAgents
  launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
  ```

##### Create Virtual Environment:
From a terminal window in the directory where you want the Virtual Environment to exist, type:

  `python3 -m venv [your virtual environment name]`

Virtual environments are used to isolate the set of tools used by your app and the set of risks that opens up due to running your app, so that it is compartmentalized in your computer.


##### Run Virtual Environment:
From the same terminal window where your venv folder is, type:

  `source [your virtual environment name]/bin/activate`

You will know its working when your terminal path starts with `([your virtual environment name])`


##### Stop Virtual Environment:
From a terminal window where your venv folder is, type:

  `deactivate`


##### Check Virtual Environment Installations:
From a terminal window with your virtual environment activated, type:

  `pip freeze`

Or:

  `pip3 freeze`

pip3 always uses python3, whereas pip will use the default version of python on the OS.


##### Install Django:
From a terminal window with your virtual environment activated, type:

  `pip3 install django`

This is not necessary, as django-toolbelt will also install django:

  `pip3 install django-toolbelt`

Other tools, if needed, can also be installed this way.


##### Saving a virtual environment's installed programs:
From a terminal window with your virtual environment activated, type:

  `pip3 freeze > [name of requirements doc].txt`

This can then be used to install programs with:

  `pip3 install -r [name of requirements doc].txt`

'-r' is the flag for a set of requirements in pip3 install


##### Starting a Django Project:
From a terminal window with your virtual environment activated, type:

  `django-admin startproject [project name]`

Project name can be any combination of numbers, letters, and underscores


##### Starting an Application:
Navigate into the project folder with:

  `cd [project name]`

This directory contains the 'manage.py' file that you need to execute the command to start an app.

From this terminal window, type:

  `python3 manage.py startapp [app name]`

App name can be any combination of numbers, letters, and underscores, and cannot be the same as the project name or another app.


##### Updating Settings:
There are many more settings applied to your project than what is in the settings.py folder of your project. This file is used to overwrite the settings you are changing from their default states/values.

  `INSTALLED_APPS:`

  Open the settings.py folder.

  Add '[app name]' to the INSTALLED_APPS list and include a trailing comma.

  `DATABASES:`

  Edit DATABASES to your local Postgres server:

  ```python

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


##### Create the database in Postgres:
From a terminal window with your virtual environment activated, type:

  `createdb [name of database]`

This initializes the database within Postgres that will be used for your project.


##### Delete the database in Postgres:
From a terminal window with your virtual environment activated, type:

  `dropdb [name of database]`

--------------------------------------------------------------------------------

#### Create necessary files and directories:

##### Create urls.py in the new app folder:
From the new app folder with virtual environment activated, type:

  `touch urls.py`

Alternatively, this file can be copied over from the main project folder:

  `cp [project name]/urls.py [app name]`


##### Create directory for html templates in new app folder:
From the new app folder with virtual environment activated, type:
  ```bash
  mkdir -p templates/[app name]
  ```
Move into this directory and create the necessary html files:
  ```bash
  cd templates/[app name]
  touch index.html
  ```


##### Create forms.py in the new app folder:
From the new app folder with virtual environment activated, type:

  `touch forms.py`

--------------------------------------------------------------------------------

#### Models & Forms:

##### Models:
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


##### Forms:
The forms.py file should always be headed by:
  ```python
  from django.forms import ModelForm
  ```

All models to be used in forms should also be imported from [app name].models.

Forms are built as classes inheriting from ModelForm:
  ```python
  class ComplimentForm(ModelForm):
  ```
Their structure is stored in a secondary class: class Meta *(?WHAT is Meta and why do we use it?)*
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

##### HiddenInput:
The HiddenInput() function is imported from the same library as ModelForm. It can be accessed by changing the top line of the forms.py file to read:
  ```python
  from django.forms import ModelForm, HiddenInput
  ```

To use HiddenInput(), the fields to be hidden would have to be noted in the widget, like above. and initialized in the form when it is called in the context dictionary for a template being rendered.

For example, the following view renders the url_shortener template with a context dictionary that calls the form, and initializes a value that is using HiddenInput(), with `[Name ofForm](initial={'[name of HiddenInput() field']:[field value]})`. In this case, the field value is created using the `get_url_id()`` function.

```python
def url_shortener(request):
    return render(request, 'url/url_shortener.html', {'form':URLForm(initial={'url_identifier':get_url_id()})})
```

--------------------------------------------------------------------------------

#### Migrate & Run:

##### Once the model is created, Migrate:
From a terminal window in the root folder with the virtual environment activated, type:
  ```bash
  python3 manage.py makemigrations [app name]
  python3 manage.py migrate [app name]
  ```
For both of these, the [app name] is optional - if it is provided it will makemigrations and migrate only that app, and if not, it will do the same for all apps int he project.


##### Run the server:
From a terminal window in the root folder with the virtual environment activated, type:

  `python3 manage.py runserver`

`Ctrl + click` on URL indicated in the set up will navigate to the page.

`Ctrl + C` will stop the server.

To make edits with the server running, open another terminal window to the project directory.

--------------------------------------------------------------------------------

#### Data Flow:

##### Create URL in urls.py:
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


##### Create View in views.py:
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
The first argument is "request". *(?WHY? and what does this do?)*

The rest are parameters/variables passed to the rendered html template (e.g. user_id).

Views can run logic, and then end with either a render or a redirect.

Render is used for running view functions straight to html templates.

Redirect is used when an action has been taken on the page.

This requires the user to be redirected to another page after the action is complete.

- Render:
At the end of the view function, the render syntax is:
  ```python
  return render(request, '[app name]/[template name].html', {context dictionary: of passed parameters})
  ```

- Redirect:
At the end of the view function, the redirect syntax is:
  ```python
  return redirect('redirectto/thisurl')
  ```
Instead of rendering a template directly, redirect sends the process back through the urls -> views process to end with a rendered template.


##### HTML Templates:
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


####### Using Variables in HTML Templates:
- Variables are called with {{[variable name]}}
- Variables are pulled from HTML with request.POST['[variable name]']

####### Using Forms in HTML Templates:
- Forms are called like variables, with {{[name of form].as_[name of display]}}
- display options include form.as_table, form.as_p, etc.

--------------------------------------------------------------------------------

#### Other Specifics:

##### Hash Password with BCrypt:
This information is from [Django Docs on Password Management](https://docs.djangoproject.com/en/1.8/topics/auth/passwords/#module-django.contrib.auth.hashers).

From the terminal with your Virtual Environment running, install the bcrypt library with:

`pip3 install django[bcrypt]`

Add the following to the settings.py file:
```
PASSWORD_HASHERS = (
    'django.contrib.auth.hashers.BCryptSHA256PasswordHasher',
    'django.contrib.auth.hashers.BCryptPasswordHasher',
    'django.contrib.auth.hashers.PBKDF2PasswordHasher',
    'django.contrib.auth.hashers.PBKDF2SHA1PasswordHasher',
    'django.contrib.auth.hashers.SHA1PasswordHasher',
    'django.contrib.auth.hashers.MD5PasswordHasher',
    'django.contrib.auth.hashers.CryptPasswordHasher',
)
```
This modifies the existing PASSWORD_HASHERS to put the BCrypt entries first, thus making them the default. The others are still necessary so the database can check/upgrade existing passwords with them.

Once complete, this should upgrade the existing passwords in your database. *(?Should it? It doesn't?)*

`django.contrib.auth.hashers` is the module that includes the functions necessary to create a hash as a password, and check the password once it's created in order to verify the hashed password with the plaintext version.

To use the module, add the following to the top of the file you're using to create your User instances with their passwords as an attribute:

`from django.contrib.auth.hashers import make_password, check_password`

Use the make_password() function in your model or elsewhere to save passwords as a hash instead of plaintext.

  `make_password([plaintext password])`

Use the check_password() function to validate an entered password against its hashed equivalent.

  `check_password([entered password],[stored hashed password for user])`


##### Sessions:
This information is from the [Django Docs on Sessions](https://docs.djangoproject.com/en/1.8/topics/http/sessions/)

Adding the following to the settings.py file will enable cookie-based sessions:

`SESSION_ENGINE = "django.contrib.sessions.backends.signed_cookies"`

When SessionMiddleware is activated (the default is activated), each HTTPRequest object (e.g. the `request` variable passed in the `render()` functions in your views) has a `session` attribute which can be accessed with `request.session.` The `session` attribute is a dictionary-like object.
