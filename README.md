# Django project steps

1) 
Open terminal in VS Code. Create virtual environment using following command.

```shell
$ python -m venv venv
```

2) 
Activate virtual environment.

```shell
$ . venv\Scripts\Activate
```

3)
Install Django in the virtual environment.

```shell
(venv) $ pip install Django
```

4)
Create a django project.

```shell
(venv) $ django-admin startproject shop .
```

5)
Check the directory to see what files & folders have been created:

```shell
(venv) $ ls
shop
venv
manage.py
```

6)
Create a django app to manage products. We will call it products.

```shell
(venv) $ python manage.py startapp products
```

Check the directory again to see the files & folders:

```shell
(venv) $ ls
products
shop
venv
manage.py
```

7)
Add the `products` app to the `INSTALLED_APPS` configuration setting found in `shop\settings.py`

```python
...
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'products',
]
...
```

8)
We want to store products that have a name and a price. Go to `products\models.py` and add the following content.

```python
from django.db import models

class Product(models.Model):
    name = models.CharField(max_length=100, null=False)
    price = models.PositiveIntegerField(null=False)

    def __str__(self):
        return self.name
```

9)
Models are Django's way of representing database tables. In order for the database to be aware of this model and add the corresponding table we must add migrations. For migrations there are two steps. First we must create a migration:

```shell
(venv) $ python manage.py makemigrations
```

10)
This will generate a migration script in `products\migrations\0001_initial.py`. After the migration script has been created we have to apply the migration:

```shell
(venv) $ python manage.py migrate
```

11)
We want to display a list of all products. To do that we must first add a **view function**. A view function, is a normal python function that always takes a HTTP Request as its first parameter. Go to `products\views.py` and add the following code.

```python
# products\views.py
from django.shortcuts import render

from products.models import Product


def get_product_list(request):
    products = Product.objects.all()
    return render(request, "product_list.html", context={
        "products": products
    })
```

12)
The view function generally uses a template to generate html. Create a folder named `templates` in `products`. Inside the created folder templates create a file named `product_list.html`. The structure should look like this:

```shell
(venv) $ ls
shop
venv
products
    migrations
    templates
        product_list.html
    __init__.py
    admin.py
    apps.py
    models.py
    tests.py
    views.py
manage.py
```

13)
Add the following content to `products\templates\product_list.html`

```html
<h1>Products</h1>
<ul>
{% for product in products %}
  <li>{{ product.name }} - ${{ product.price }}</li>
{% endfor %}
</ul>
```

14)
In order for django to execute our view function when we go to the home page we must map the URL path to the view function we wrote in `products\views.py`. To do that we add a `urls.py` to the `products` folder. Inside `urls.py` we should add the following content:

```python
from django.urls import path

from products.views import get_product_list

urlpatterns = [
    path("", get_product_list, name="product_list"),
]

```

15)
In order for django to pick up this url mapping we should add the following content to `shop\urls.py`:

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path("", include("products.urls")),
]
```

16)
To view the site during development Django provides a web server to do just that. You can run the development web server by using the following command:

```shell
(venv) $ python manage.py runserver
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
July 04, 2021 - 10:45:41
Django version 3.2.5, using settings 'shop.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.
```

If you open the address `http://127.0.0.1:8000/` in your browser you should be able to see the content django rendered. Since you don't have any products in the database you will only see the `h1` tag displayed.

17)
Django provides an app out of the box that creates an administration dashboard that allows you to manage your models. To be able to manage our `Product` model using the django admin we must first register our model with it. To do that we should open the `products\admin.py` file and add the following content:

```python
# products\admin.py
from django.contrib import admin

from products.models import Product

class ProductAdmin(admin.ModelAdmin):
    pass

admin.site.register(Product, ProductAdmin)
```

18)
To be able to use the admin you must have a "superuser" account. We must first create a superuser account using the following command:

```shell

(venv) $ python manage.py createsuperuser
Username (leave blank to use 'gledi'): admin
Email address: admin@somewhere.com
Password:
Password (again):
This password is too short. It must contain at least 8 characters.
This password is too common.
Bypass password validation and create user anyway? [y/N]: y
Superuser created successfully.
```

Now you can go to `http://127.0.0.1:8000/admin/` and login.

#   M a k i n a  
 