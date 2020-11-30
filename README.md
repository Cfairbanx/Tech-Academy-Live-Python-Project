# Tech-Academy-Live-Python-Project
Simulated Team Work Environment

## Introduction
For the last nine days I participated in a simulated team work environment on a Django Python project. We were to each create our own web application all within the same master. Working within the same master was an excellent opportunity to learn version control systems, merging conflicts, migration conflicts and other team issues that you would come across in a working environment. We were alotted 2 weeks, 9 days as it spanned over the holiday Thanksgiving, to see how far we could make it through application construction stories. Each story progressively became more difficult in order to continue pushing our skills and problem solving mindset. I saw how a developer communicates and collaborates with their team in order to improve a product. I worked on the [back end stories](#back-end-stories) and [front end stories](#front-end-stories) of an application about plants, having a "green thumb" I felt right at home in creating something visually calming and appealing. I gained valuable real world experience and [skills](#other-skills-learned) that I intend to implement within my development career. 

Below are descriptions and examples of the work that I completed, this is an app about plants that stores added plant information, allows you to sort through stored information and displays this information. 

## Back End Stories
* [Models and Forms](#models-and-forms)
* [Views Functions](#views-functions)


### Models and Forms
Here is where I created a model for what information to store in the database. This is the needed information from the user, given the constraints.

    from django.db import models
    
    # Limit the choices for growth type
    GROWTH_CHOICES = (
        ('Trailing/Vining', 'Trailing/Vining'),
        ('Tree', 'Tree'),
        ('Shrub', 'Shrub'),
        ('Succulent', 'Succulent'),
        ('Fern', 'Fern'),
    )
    # limit the choices for light needs
    LIGHT_CHOICES = (
        ('Direct Light', 'Direct Light'),
        ('Bright Indirect Light', 'Bright Indirect Light'),
        ('Indirect Light', 'Indirect Light'),
    )
    # define model
    class Indoor_Plant(models.Model):
        plant_name = models.CharField(max_length=50)
        plant_growth = models.CharField(max_length=25, choices=GROWTH_CHOICES)
        plant_light = models.CharField(max_length=30, choices=LIGHT_CHOICES)
        plant_description = models.TextField(max_length=300)
        objects = models.Manager()
    def __str__(self):
        return self.plant_name
        
After creating the model I created a form so that the user could have fields to enter the information in.

    from django.forms import ModelForm
    from .models import Indoor_Plant
    class Indoor_PlantForm(ModelForm):
        class Meta:
            model = Indoor_Plant
            fields = '__all__'
            
### Views Functions
The views page held the functions necessary to run my application. There are functions for both [back end stories](#back-end-stories) and [front end stories](#front-end-stories).

Function needed to render my home page.

    from django.shortcuts import render

    def home(request):
        return render(request, "PlantApp/PlantApp_home.html")

Function needed to gather information from the user in the designated form. 

    from django.shortcuts import render, redirect
    from .forms import Indoor_PlantForm
    
    def PlantApp_addPlant(request):
        form = Indoor_PlantForm(data=request.POST or None)
        if request.method == 'POST':
            if form.is_valid():
                form.save()
                return redirect('PlantApp_home')
        formset = {'form': form}
        return render(request, 'PlantApp/PlantApp_AddPlants.html', formset)
 
 Function needed to render the index page with pagination, a page that allows a user to sort through the database of plants and select a plant to read further about.
 
      from django.shortcuts import render, redirect
      from .forms import Indoor_PlantForm
      from .models import Indoor_Plant
      from django.core.paginator import Paginator
      
    # display added plants from database, pagination for user-friendliness
    
    def index(request):
        form = Indoor_PlantForm()
        plants = Indoor_Plant.objects.all()
        formset = {'form': form, 'plants': plants}
        page = Paginator(plants, 6)
        page_number = request.GET.get('page')
        page_obj = page.get_page(page_number)
        print(plants)
        return render(request, "PlantApp/PlantApp_index.html", {'page_obj': page_obj}, formset)
        
Function needed to render the details page.

    from django.shortcuts import render, get_object_or_404
    from .models import Indoor_Plant
    
    def details(request, pk):
    plant = get_object_or_404(Indoor_Plant, pk=pk)
    get_details = {'plant': plant}
    print(get_details)
    return render(request, "PlantApp/PlantApp_details.html", get_details)
    
*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#Tech-Academy-Live-Python-Project)*

## Front End Stories
* [Base HTML Template](#base-html-template)
* [Template Inheritance](#template-inheritance)

### Base HTML Template
Here is the base HTML template that I created, all other html templates inherited this form and styling. I utilized bootstrap for my navigation bar, bootstrap is awesome!

    {% load static %}

    <html lang="en">

    <head>
        <!-- Needed html info-->
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>The Indoor Jungle</title>
        <!-- Links -->
        <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/css/bootstrap.min.css" integrity="sha384-TX8t27EcRE3e/ihU7zmQxVncDAy5uIKz4rEkgIXeMed4M0jlfIDPvg6uqKI2xXr2" crossorigin="anonymous">
        <link rel="stylesheet" href="{% static 'CSS/PlantApp_css.css' %}">
    </head>
    <body class="base_background">
        <!-- Nav bar -->
            <nav class="navbar navbar-expand-lg navbar-light bg-light">
              <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav">
                  <li class="nav-item active">
                    <a class="nav-link" href="{% url 'home' %}">Back to Apps <span class="sr-only">(current)</span></a>
                  </li>
                  <li class="nav-item">
                    <a class="nav-link" href="{% url 'PlantApp_home' %}">Home</a>
                  </li>
                  <li class="nav-item">
                    <a class="nav-link" href="{% url 'PlantApp_AddPlants' %}">New Plants</a>
                  </li>
                  <li class="nav-item">
                    <a class="nav-link" href="{% url 'PlantApp_index' %}">Plant Index</a>
                  </li>
                </ul>
              </div>
            </nav>
            <div>
                <!-- needed for to allow headers on child html -->
                {% block header %}
                {% endblock %}
            </div>
            <!-- rest of body -->
            <div>
                {% block content %}
                {% endblock %}
            </div>

            <!-- Footer -->
            <footer>
                <p>&copy; The Tech Academy <a>|</a> Author: Casey Fairbanks</p>
            </footer>

        <script src="https://code.jquery.com/jquery-3.5.1.slim.min.js" integrity="sha384-DfXdz2htPH0lsSSs5nCTpuj/zy4C+OGpamoFVy38MVBnE+IbbVYUew+OrCXaRkfj" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/bootstrap@4.5.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-ho+j7jyWK8fNQe+A12Hb8AhRq26LrZ/JpcUGGOn+Y7RsweNrtN/tE3MoK7ZeZDyx" crossorigin="anonymous"></script>
    </body>
    </html>

### Template Inheritance
Here is an example of inheritance in Django, the details page.

    {% extends 'PlantApp/PlantApp_base.html' %}
    {% load static %}
    {% block title %}Details{% endblock %}
    {% block header %}<h1>{{ plant.plant_name }}</h1>{% endblock %}


    {% block content %}
    <br>
        <!-- Display as list info for selected plant -->
        <ul class="plant_info">
            <li><h4>Type of Growth: </h4>{{ plant.plant_growth }}{{ plant.pk }}</li>
            <br>
            <li><h4>Light Needed: </h4>{{ plant.plant_light }}</li>
            <br>
            <li><h4>Brief Description: </h4>{{ plant.plant_description }}</li>
        </ul>
    <br>
        <div class="Edit-btn">
            <a href="{% url 'PlantApp_Edit' plant.pk %}" class="btn btn-success" role="button">Edit Plant</a>
        </div>
    {% endblock %}

## Other Skills Learned
* Working with a team of developers in order to achieve a common goal. 
* Communicating with a team lead for accuracy, direction and support.
* Azure Devops as a project management system.
* Using my creativity in a programming/project environment to help others.
* Timing of when to reach out for help:
  * In my developing studies I have been learning when the appropriate time to ask for help. This project really showed me that it is conducive to a better workflow and team structure if you reach out for help sooner, although after exhuasting other resources (online searches, previous project examples, etc.). I powered through the first couple of stories, faster than others which gave me a hightened level of confidence. The other team member used my code as an example for their own. About halfway through the project I began to struggle in places where my team members were not and felt comfortable in asking for help sooner. One should seek to problem solve on their own, however, there is a certain level of "spinning your wheels" that one should recognize and reach out for help before burning out. Burn out harms your mindset and production. 
  
*Jump to: [Front End Stories](#front-end-stories), [Back End Stories](#back-end-stories), [Other Skills](#other-skills-learned), [Page Top](#Tech-Academy-Live-Python-Project)*
