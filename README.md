# Django_login
# Django Authentication Setup Guide

This guide walks through setting up Django's authentication system including login, logout, password change, and user registration.

## Full Example

### Step 1: Enable Django Auth App

Check that the `django.contrib.auth` and `django.contrib.contenttypes` apps are in the list of installed apps, if not add them. You can do this by opening `settings.py` and updating `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    ...
    'django.contrib.auth',
    'django.contrib.contenttypes',
    ...
]
```
Next make sure the following middlewares are present:
```python
MIDDLEWARE = [
    ...,
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    ...
]
```
Step 2: Setting Up Urls & Redirects
Open the urls.py file and add the required accounts urls as shown below:

```
from django.contrib import admin
from django.urls import path, include
from myapp.views import SignUpView  # make sure this import is correct
from django.views.generic import TemplateView

urlpatterns = [
    path("admin/", admin.site.urls),
    path("accounts/", include("django.contrib.auth.urls")),
    path("accounts/profile/", TemplateView.as_view(template_name="accounts/profile.html"), name="profile"),
    path("signup/", SignUpView.as_view(), name="signup"),
]
```
Here we setup the accounts path which contains the login, logout, password change etc. routes except the signup and profile routes. These are separately added.

Next, update the redirect constant variables to redirect to the profile page. Open the settings.py file and update it as below:

```
LOGIN_REDIRECT_URL = "/accounts/profile"
LOGOUT_REDIRECT_URL = "/accounts/profile"
```
Step 3: Adding Template Files
First create a templates folder at the root of the project and update the TEMPLATES constant in the settings.py file as follows:

```
TEMPLATES = [
    {
        ...
        'DIRS': [ BASE_DIR / "templates" ],
        ...
    },
]
```
The following will be the expected folder structure. Assuming you have an app named myapp (this can be any app including a dedicated accounts app if you prefer):

```
├── db.sqlite3
├── manage.py
├── myapp
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── urls.py
│   └── views.py
├── mysite
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── templates
    ├── profile.html
    ├── accounts
    │   └── profile.html
    └── registration
        ├── login.html
        └── signup.html
```
profile.html
```
{% if user.is_authenticated %} 
You are logged in as {{ user }}.
<form action="{% url 'logout' %}" method="post">
    {% csrf_token %}
    <button type="submit">Log Out</button>
</form>
{% else %} 
You are not logged in.
<a href="{% url 'login' %}">Click here to log in.</a>
{% endif %}
```
login.html
```
{% if form.errors %}
<p>Your username and password didn't match. Please try again.</p>
{% endif %}

<form method="post" action="{% url 'login' %}">
    {% csrf_token %} {{ form.as_p }}
    <input type="submit" value="login" />
</form>
```
sigup.html
```
{% block title %}Sign Up{% endblock %} 
{% block content %}
<h2>Sign up</h2>
<form method="post">
    {% csrf_token %} {{ form }}
    <button type="submit">Sign Up</button>
</form>
{% endblock %}
```
Step 4: Adding Signup View
The next thing to do is adding a signup view. This view can be added to your existing app or to a dedicated app for handling accounts. In our case we will add this to our myapp app views.py file:

```python
from django.contrib.auth.forms import UserCreationForm
from django.urls import reverse_lazy
from django.views.generic import CreateView

class SignUpView(CreateView):
    form_class = UserCreationForm
    success_url = reverse_lazy("login")
    template_name = "registration/signup.html"
```
Step 5: Migrating Your Changes and Running Your Project
The last thing to do is migrate your changes and run your project:

```python
python manage.py makemigrations
python manage.py migrate
python manage.py runserver
```

This README.md file includes all the steps with proper Markdown formatting, including:
```
- Headers for each section
- Code blocks with language specification
- Proper indentation and formatting
- Clear separation between different steps
- File structure visualization
```
