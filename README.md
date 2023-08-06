
# Removing Background Server Tutorial

This tutorial will guide you through the steps to set up your own background removal server using Django. The server utilizes the `rembg` library to remove backgrounds from images. Follow the instructions below to get started:

## Prerequisites

- Python 3.x
- Django
- `rembg` library (`pip install rembg`)

## Step 1: Create a Django Project

1. Open your terminal and navigate to your desired project directory.
2. Run the following command to create a new Django project:
   ```
   django-admin startproject YourProjectName
   ```
3. Move into the project directory:
   ```
   cd YourProjectName
   ```

## Step 2: Configure URLs and Views

1. Open the `urls.py` file in your project directory (`YourProjectName/urls.py`).
2. Import the views module by adding this line at the top:
   ```python
   from . import views
   ```
3. Add the following line to the `urlpatterns` list to define the API endpoint:
   ```python
   path('api/', views.process_image),
   ```

## Step 3: Create Views

1. Create a file named `views.py` in your project's main directory (`YourProjectName/views.py`).
2. Copy and paste the following code into `views.py`:
   ```python
   from django.http import JsonResponse, HttpResponse
   from django.views.decorators.csrf import csrf_exempt
   from PIL import Image
   import os
   from rembg import remove

   @csrf_exempt
   def process_image(request):
       if request.method == 'POST' and request.FILES.get('image_file'):
           uploaded_image = request.FILES['image_file']
           temp_image_path = 'temp_input.png'
           with open(temp_image_path, 'wb') as temp_image:
               temp_image.write(uploaded_image.read())
           input_image = Image.open(temp_image_path)
           output_image = remove(input_image)
           temp_output_path = 'temp_output.png'
           output_image.save(temp_output_path)
           os.remove(temp_image_path)

           response = HttpResponse(content_type='image/png')
           response['Content-Disposition'] = 'attachment; filename="output.png"'
           with open(temp_output_path, 'rb') as temp_output:
               response.write(temp_output.read())
           os.remove(temp_output_path)
           return response
       else:
           return JsonResponse({'status': '400', 'message': 'Bad Request'})
   ```

## Step 4: Run the Server

1. In your terminal, make sure you're in your project directory (`YourProjectName`).
2. Run the following command to start the development server:
   ```
   python manage.py runserver
   ```
3. Your server will be accessible at `http://127.0.0.1:8000/`.

## Deploying to Hosting

- If deploying to a hosting service, ensure that you install the `rembg` library within the virtual environment of your hosted project.
- Note that the first run might take a bit longer as the library gets cached.
- For privacy, consider running your own server. Example mirror servers: "http://nobg.pythonanywhere.com/api/", "http://wegvei.pythonanywhere.com/api/".

You've successfully set up a background removal server using Django! Feel free to customize and enhance the code to add more features if desired.
