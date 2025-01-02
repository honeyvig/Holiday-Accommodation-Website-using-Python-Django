# Holiday-Accommodation-Website-using-Python-Django
Sample https://portdouglasaccom.com.au
To create a Holiday Accommodation Website using Python Django, we will need to set up a Django project with features like displaying available accommodations, booking forms, user management, and backend functionality for managing listings. Below, I'll provide a step-by-step guide and a simplified version of the code to help you get started.
1. Setup Django Project

    Install Django: First, you need to install Django. You can do this via pip:

pip install django

Create a New Django Project: Create a new Django project for your holiday accommodation website:

django-admin startproject holiday_accommodation
cd holiday_accommodation

Create an App: Create a new app to handle the accommodation listings and user bookings:

python manage.py startapp accommodation

Add the App to Settings: Open the settings.py file in the holiday_accommodation folder and add the app to INSTALLED_APPS:

    INSTALLED_APPS = [
        # Default Django apps...
        'accommodation',  # Add this line
    ]

2. Models for Accommodations and Bookings

In the accommodation app, you will need to define models for accommodations and bookings. Open accommodation/models.py and add the following:

from django.db import models

class Accommodation(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField()
    location = models.CharField(max_length=100)
    price_per_night = models.DecimalField(max_digits=8, decimal_places=2)
    available_from = models.DateField()
    available_to = models.DateField()
    image = models.ImageField(upload_to='accommodations/', blank=True, null=True)

    def __str__(self):
        return self.title


class Booking(models.Model):
    accommodation = models.ForeignKey(Accommodation, on_delete=models.CASCADE)
    customer_name = models.CharField(max_length=100)
    customer_email = models.EmailField()
    check_in_date = models.DateField()
    check_out_date = models.DateField()

    def __str__(self):
        return f"Booking for {self.customer_name} - {self.accommodation.title}"

    Accommodation: Contains details about the available accommodations.
    Booking: Stores customer booking details for accommodations.

3. Admin Interface

You’ll need to add the models to the Django admin interface for easy management. Open accommodation/admin.py and add the following:

from django.contrib import admin
from .models import Accommodation, Booking

admin.site.register(Accommodation)
admin.site.register(Booking)

4. Views and Templates

Now, let's define some views and templates to display accommodations and allow users to make bookings.

In accommodation/views.py, create views for displaying accommodation listings and handling bookings:

from django.shortcuts import render, get_object_or_404
from .models import Accommodation, Booking
from .forms import BookingForm

def accommodation_list(request):
    accommodations = Accommodation.objects.all()
    return render(request, 'accommodation/accommodation_list.html', {'accommodations': accommodations})

def accommodation_detail(request, pk):
    accommodation = get_object_or_404(Accommodation, pk=pk)
    if request.method == 'POST':
        form = BookingForm(request.POST)
        if form.is_valid():
            booking = form.save(commit=False)
            booking.accommodation = accommodation
            booking.save()
            return render(request, 'accommodation/booking_confirmation.html', {'booking': booking})
    else:
        form = BookingForm()
    return render(request, 'accommodation/accommodation_detail.html', {'accommodation': accommodation, 'form': form})

    accommodation_list: Displays a list of available accommodations.
    accommodation_detail: Displays a detailed page for each accommodation and allows users to book.

5. Forms for Booking

In accommodation/forms.py, create a form for handling booking requests:

from django import forms
from .models import Booking

class BookingForm(forms.ModelForm):
    class Meta:
        model = Booking
        fields = ['customer_name', 'customer_email', 'check_in_date', 'check_out_date']

6. URL Configuration

Define URLs to handle the views. In accommodation/urls.py, add the following:

from django.urls import path
from . import views

urlpatterns = [
    path('', views.accommodation_list, name='accommodation_list'),
    path('accommodation/<int:pk>/', views.accommodation_detail, name='accommodation_detail'),
]

Then, link these URLs to the project-level URL configuration in holiday_accommodation/urls.py:

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('accommodation.urls')),
]

7. Templates

Create the templates for displaying the accommodation list and booking details. First, create a templates directory inside the accommodation app:

accommodation/
├── migrations/
├── templates/
│   ├── accommodation/
│   │   ├── accommodation_list.html
│   │   ├── accommodation_detail.html
│   │   └── booking_confirmation.html

Accommodation List Template (accommodation_list.html)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Accommodation Listings</title>
</head>
<body>
    <h1>Available Accommodations</h1>
    <ul>
        {% for accommodation in accommodations %}
            <li>
                <h2>{{ accommodation.title }}</h2>
                <p>{{ accommodation.description }}</p>
                <p>Price per night: ${{ accommodation.price_per_night }}</p>
                <a href="{% url 'accommodation_detail' pk=accommodation.pk %}">View Details</a>
            </li>
        {% endfor %}
    </ul>
</body>
</html>

Accommodation Detail Template (accommodation_detail.html)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>{{ accommodation.title }}</title>
</head>
<body>
    <h1>{{ accommodation.title }}</h1>
    <p>{{ accommodation.description }}</p>
    <p>Price per night: ${{ accommodation.price_per_night }}</p>

    <h2>Book This Accommodation</h2>
    <form method="POST">
        {% csrf_token %}
        {{ form.as_p }}
        <button type="submit">Book Now</button>
    </form>
</body>
</html>

Booking Confirmation Template (booking_confirmation.html)

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Booking Confirmation</title>
</head>
<body>
    <h1>Thank you for your booking!</h1>
    <p>Your booking details are as follows:</p>
    <p>Accommodation: {{ booking.accommodation.title }}</p>
    <p>Customer Name: {{ booking.customer_name }}</p>
    <p>Check-in Date: {{ booking.check_in_date }}</p>
    <p>Check-out Date: {{ booking.check_out_date }}</p>
</body>
</html>

8. Static Files for Styling (Optional)

You can also add static CSS files to improve the appearance of your website. Create a static directory within your app to store your CSS files.
9. Run the Development Server

Once everything is set up, run the Django development server to view your website:

python manage.py runserver

Visit http://127.0.0.1:8000/ to see your holiday accommodation website in action.
10. Conclusion

This code sets up a basic holiday accommodation website with functionalities to display listings, allow users to view details, and book accommodations. You can extend this functionality with features like user authentication, payment processing, search filters, and more, depending on your requirements.
