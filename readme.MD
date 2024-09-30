# All about Static and media files in Django.

Handling Static and media files are very crucial for a Django project.
> Static files are files that don't change when your application is running.

> Media files are image, video, audio or any type of graphics file which are dynamically added and updated in runtime.

## Handling static files

There are two types of scenario that we should know about the static files.
1. **Development server**: On the development period of a Django project the staticfiles are automatically served by Django if we have the necessary settings. Static files settings are already configured properly in the `settings.py`. ( `DEBUG=True` )

2. **Production server**: In the production server Django doesn't serve static files automatically. `Whitenoise` is commonly used to serve static files in the production server. ( `DEBUG=False` )

## Setup static
In the `settings.py` file, there is a variable `STATIC_URL` it helps to create the static url of the Django project. By default Django search for a folder named `static` in the apps of the project. 
> Note: If we create the static folder in any app, we have to ensure that no migrations are pending. Otherwise it's possible that there will 404 error while getting the static file.

If we create our static files directory outside apps, we have to configure the directory on `settings.py`.

We have to add a variable in `settings.py`.
```py
# Make sure that the BASE_DIR variable is already declared in the settings.py
STATICFILES_DIRS = [BASE_DIR / 'static'] # Must be an iterable

# In this example the static folder is in project directory.
```
This will serve the staticfiles with the help of,
```py
INSTALLED_APPS = [
    #.....
    'django.contrib.staticfiles'
    #.....
]

```
If we want to serve the staticfiles manually, we have follow the below steps.
1. Add `STATIC_ROOT` in `settings.py`.
```py
STATIC_URL = 'static' # Make sure STATIC_URL is also defined
STATIC_ROOT = 'staticfiles'
```
2. Run command to collect the static to the static root folder.
```bash
py manage.py collectstatic
```

3. In the project `urls.py` we have to add the following codes.
```py
from django.conf import settings # NEW
from django.conf.urls.static import static # NEW
from django.urls import path
urlpatterns = [
    #......
]
urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)

```
> This is not suitable for production use!

## Handling static in production server.

As we all know, Django don't serve static files automatically in the production server. So, we have to use whitenoise to serve our staticfiles in production server. (`DEBUG=False`).

First, we have to ensure that, our staticfiles are collected in the static root. Ensure the `STATIC_ROOT` variable is add in the `settings.py` and run the above command to collect static.

Now we have to install whitenoise.
``` 
pip install whitenoise
```

After that we have to add whitenoise middleware in the middleware list in `settings.py`.

```py
MIDDLEWARE = [
    #......
    'whitenoise.middleware.WhiteNoiseMiddleware',
]
```
In addition, we have to added `STATICFILES_STORAGE` in `settings.py`.
```py
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```
#### We are ready with our static files now.

## Media files in Django
Media files are added dynamically on our server by the user. We should use cloud storages to handle media in our production server. I will show how to handle media file in development server here.

1. Firstly, we have to specify `MEDIA_URL` and `MEDIA_ROOT` in our `settings.py`.

```py
MEDIA_URL = 'media/'
MEDIA_ROOT = BASE_DIR / 'media'
```
2. Now, we have to serve the media files as static manually. In project `urls.py`,
```py
from django.conf import settings # NEW
from django.conf.urls.static import static # NEW
from django.urls import path
urlpatterns = [
    #......
]
urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```
> Note: It will not work if `DEBUG=False` in `settings.py` 
#### Now we can see the media files in our development server properly