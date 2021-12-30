# SEI-Project-4-Cicero 


pip3 install django
pip3 install django-cors-headers
pip3 install djangorestframework   
pip3 install braintree 
pip3 install Pillow


REST_FRAMEWORK = {
    # Use Django's standard `django.contrib.auth` permissions,
    # or allow read-only access for unauthenticated users.
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}

install django rest_framework
we are creating another app for the api so if we decide to build a different fronend we can do that without restructrue the back end 

create a model in Category : {
regster admin
serializer
views MODEL VIEWSET --> DOCOMENTATION
setup url
}

create a model in Product: { they are gonna be linked up with Category so we need a foregin key
regster admin
serializer
views MODEL VIEWSET --> DOCOMENTATION
setup url
We need pillow package for images
}

Django do not provide an absolute url to the image stored in the models.imageField
we need to take that into account when we set up the serializer for the product so it will show the entire URL


create a custom User model {

    as we need separete login for the user and admin the serialization is gonna be more difficult because data is not just flowing from the backend -> frontend but also frontend will send data for the backend to register to loggin and to sign out the user
}
by default django will log you in with username and password I need email and password login so I need to override this 

What is the pop method in Python
USER serialization lOOK up
LOOK UP RANGE IN PYTHON
regexr.com
LOOK UP VIDEO 35 USER MODEL VIEWS.PY
once i do customization on model login I need to create an file in the Migartion foldern in the API folder called 0001_intial py to be able to createa super user
get_user_model ?
Setup a payment Braintree {
    1. frontend asking backend are you ready?
    2. backend yes here is your token
    3. frontend will talk to Braintree server and passes back a NONCE
    4. as the frontend receives the NONCE it
    5. it will send the NONCE and the  money(£50) to the backend 
    6. take the NONCE with the payment and send it back to Braintree server and the Braintree replies if this was all good or not
}
do not need to register in the admin page 

MOVING TO FRONT END
instead of proxy I set up a backend .js file to in case we have to migrate our server it is easier just api variable changes 
npm install react-router-dom@5.2.0


# General Assembly Project 4: Cicero 👒

Solo Project

**_Timeframe_**

7 Days

## Goal:

Design a full-stack React app using Python, Django and PostgreSQL.

## Technologies Used

- React Hooks
- Python
- Django
- PostgreSQL
- SASS
- Braintree
- Pillow
- base64
- rest_framework
- Bootstrap
- HTTP-proxy-middleware


# Cicero

An E-Commerce website that sells hipster hats


<img src='https://i.imgur.com/9uTqWSf.png'>



## Code Installation

https://github.com/lukacspapp/SEI-Project-4-Cicero


- Clone or download the repo
- <code>cd lcodev</code> to go to the folder
- <code>pipenv shell</code> to install Python packages
- <code> python3 manage.py startserver</code> to start the backend 
- <code>cd frontend</code> to go to the frontend directory
- <code>npm i</code> to install frontend dependencies
- <code>npm start</code> to start the app

# Process

## Idea

For my final project, I wanted to push myself and build an e-commerce application with integrated payment system from Braintree


## Planning

### Entity-Relationship-Diagram (ERD)

I used Coggle to create the ERD.

[Link ](https://coggle.it/diagram/Yc3s-714-CArCURa/t/api/42b5fac89eb8a53f334c3418cc49c688752ec1b0e362d66e362e225bf71f8fbc) to the diagram 

<img src='https://i.imgur.com/SfyWMkE.png'>


### Wireframes

For the Wireframes I used Figma to design the pages.

[Link ](https://www.figma.com/file/m4Qy4CCQRq71Y7xw1kbif4/ECOMMERCE-Project-4?node-id=0%3A1) to the wireframes

<img src='https://i.imgur.com/cxA5iDw.png'>

I decided to concentrate the most on the backend for this project as I was focusing on frontend and design in previous projects

<img src="frontend/src/assets/miro.png" alt="Miro" width="600px">

Our **MVP** was to ensure that there is a smooth exchange of image data between the frontend and the backend. If we were able to reach that goal quickly, we would add CSS filters.

### Split of work

George and I used a trello board to divide up tasks between us:

<img src="frontend/src/assets/trello.png" alt="Trello">

George took ownership of:

* Proof of Concept for: Tint, Histogram, Artish Brush filters 
* Image model routing
* Ensuring text on memes were centered and sized depending on user's input.
* Thumbnail generation
* CSS + Konva Filters.

I took ownership of:

* Proof of Concept for Meme Filter
* Selecting filter options and quality control for Tint, Histogram & Artist Brush filters
* Ensuring users are able to memify an image with a filter on it
* Front-end styling for all pages
* Resetting filters & hover effect.

We pair-coded on "Save Image" feature and anytime we needed to talk through a challenge or fix bugs.

## Backend

On the backend, I have four models: User, Product, Order and Category.

### User Model

```
    name = models.CharField(max_length = 50, default = 'Customer') 
    email = models.EmailField(max_length = 200, unique = True)
    
    username = None # because I do not want to sign in the user with username but with EMAIL

    USERNAME_FIELD = 'email' # so the username field will be governed by the email
    REQUIRED_FIELDS = []

    phone = models.CharField(max_length = 50, blank = True, null = True)
    
    session_token = models.CharField(max_length = 10, default = 0) 

    created_at = models.DateTimeField(auto_now_add = True)
    updated_at = models.DateTimeField(auto_now = True)
```    
    
    

The Image Model was the main model. It was use to apply filters and return the base64 image back to the frontend. We send the width and height from the frontend as well, so images are returned with the exact dimensions:
    
    class Image(models.Model):
        url = models.TextField()
        filter_type = models.CharField(
            max_length=10, null=True, validators=[validate_filter])
        filter_options = models.CharField(max_length=200, null=True)
        width = models.IntegerField(null =True)
        height = models.IntegerField(null = True)
    
        def __str__(self):
            return f'{self.url}'

    
The Filter model was created to create the database of filter options from the seeds file.

The views.py and urls.py have 3 main requests: 

* path '': GET to see all Images Uploaded, POST to upload a new image and get a URL/base64 in response
* path'<int:pk>/': GET a single Image
* path'thumbnails/': GET a list of thumbnails

Image POST request:

<img src="frontend/src/assets/viewspy.png" alt="Views flowchart">

Thumbnails GET Request:

<img src="frontend/src/assets/thumbnails.png" alt="thumbnails flowchart" width= "800px">

Thumbnails example for the tint filter:

    def get_sketch_thumbs(img, page):

    thumb_dictionaries = []

    page_index_start = (page - 1) * 10

    sketch_options = Filter.objects.filter(related_filter='sketch')[
        page_index_start:page_index_start + 10]

    for color in sketch_options:
        file_name = sketch(img, color.filter_option, thumbnail=True)
        encoded_image = str(base64.b64encode(
            open(f'{file_name}.png', 'rb').read()))[2:-1]
        thumb_dictionaries.append(
            {'option': color.filter_option, 'image': f'data:image/png;base64,{encoded_image}'})
        os.remove(f'{file_name}.png')
    return thumb_dictionaries

## Frontend

The Frontend was built using React Hooks and for this project, we began working on it at the same time as the backend. This way we could make tweaks to the backend if needed. For example, we wouldn't have realised that we needed to send the width and height of the image to the Backend if we hadn't seen the difference on the page.

We wanted the styling of Filtr to be extremely slick and like the last project, we used a lot of condition rendering to update the "Edit page" instantly. Before and after images of styling the Edit page:

<img src="frontend/src/assets/edit-before.png" alt="edit styling early" width="500px">
<img src="frontend/src/assets/edit.png" alt="edit styling final" width="600px">

We also used [Konva](https://konvajs.org/) to apply CSS filters and Emoji drop features. The below code is a typical example of how we used Konva and conditional rendering on this page:

      <Stage width={width} height={height} ref={stageRef} id="stage">
        <Layer>
          <Image
            ref={imageRef}
            width={width}
            height={height}
            onMouseEnter={showOriginal} // Onhover to show image without filters
            onMouseLeave={hideOriginal}
            x={0}
            y={0}
            image={im}
            filters={[
              Konva.Filters.Blur,
              Konva.Filters.Brighten,
              Konva.Filters.Contrast,
              Konva.Filters.Enhance,
              Konva.Filters.HSL,

              // * Have to pass the Konva filters a function even if they are not used to surpress warnings in the console.
              
              liveEffect.sepiaActive && appliedEffect ? Filters.Sepia : function   () { },
              liveEffect.embossActive && appliedEffect ?Filters.Emboss  : function () { },
              liveEffect.grayscaleActive && appliedEffect ? Filters. Grayscale : function () { },
              liveEffect.invertActive && appliedEffect ? Filters.Invert : function () { }

            ]}

            // * Conditional rendering to set default filters when the image is reset

            blurRadius={appliedEffect ? liveEffect.defaultEffect.blur}
            brightness={appliedEffect ? liveEffect. brightdefaultEffect.brightness}
            contrast={appliedEffect ? liveEffect.contdefaultEffect. contrast}
            embossStrength={appliedEffect ? liveembossStrength : defaultEffect.embossStrength}
            enhance={appliedEffect ? liveEffect.enhdefaultEffect.enhance}
            hue={appliedEffect ? liveEffect.hue : defaulthue}
            saturation={appliedEffect ? liveEffect. saturadefaultEffect.saturation}
            luminance={appliedEffect ? liveEffect.lumindefaultEffect.luminance}
          />
          {images.map((image, i) => {
            return <URLImage key={i} image={image} />
          })}
          </Layer>
      </Stage>


## Challenges

**Learning about image-editing**: This is a massive topic on its own and one of the tools we used (skicit-image) is typically used by researchers and microscopists. For this reason, there was a lot of trial and error when creating filter settings, documentation reading and stack overflow help that assisted us in getting the right filters.

**Image Size and quality**: This is something we had not considered during the initial plan -- and a good part of day three and four were spent on ensuring we don't compromise on the quality. The Gaussian filter helped reduce noise on the Tint filter, however, we still haven't found the answer for Histogram filter.


## Wins

**TeamWork**: George and I worked extremely well for this project. His experience in Python and my passion for UX helped us make a well-functioning and beautiful product. We organised ourselves using a Trello board with tasks split between "to do, in progress and done" for Front and Back end.

**Artist Brush, Meme Filter & CSS filters**: I'm particularly happy with how well these filters turned out. Artist brush is extremely creative + Meme and CSS Filters give quick and fun results!

**Styling**: I LOVE the styling for this website. Even though the little touches (like the filter animation on the homepage) are time-consuming -- It's satisfying and worth it to present a well-designed app!



## Key Learnings

* Python Fundamentals: As this was my first project using Python, I had the opportunity to solidify my understanding of it. 
* Canvas: An extremely powerful JS tool that I had never used before. I look forward to playing around with it more in the future.
* React Hooks: After making two React apps, React Hooks introduced a new way of working. I enjoyed using it and will continue to use it in future projects.


## Future Improvements

**Histogram Filter**: Our histogram filter needs work, as it's quality is completely dependent on the image uploaded by the user.

**User Login & Profile**: The backend for this is all ready, so we could easily make an Instagram or Pinterest like profile page.
© 2021 GitHub, Inc.
Terms
Privacy
Security
Status
Docs
Contact GitHub
Pricing
API
Training
Blog
About
