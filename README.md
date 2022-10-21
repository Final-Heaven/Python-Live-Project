# Introduction
As part of a student enrolled in the Software Developer Boot Camp at The Tech Academy, I worked on a Python Live Project for two weeks. I worked on creating a basic application, utilizing Python and the Django Framework, that has basic CRUD functionality, as well as an API and Beautiful Soup integration. The application allowed users to write reviews of a video game of their choosing, and then save that review to a database for viewing later. My app was part of a larger project that other students were also working on, although each app was relatively self-contained. As part of this project, we also practiced the Agile/Scrum methodology. This included daily online standup meetings, as well as code retrospectives at the end of each week.

In order to facilitate our progress throughout the project, each student was assigned a set sequence of user stories. These stories contained tasks for us to complete when building our app, and what was expected. However, we were not explicitly guided through the tasks, and needed to rely on outside research and our own testing/debugging in order to make the app functional. This was a tremendous learning experience on my part, as getting this hands-on experience allowed me to become better at researching and writing on my own.

# CRUD Functionality
- ## Create
  One of the first stories I was assigned was to create a model for the item and categories I wanted to track. In this case, I wanted to track user-generated         reviews     of video games, including the game name, reviewer name, review score, review text, and date reviewed. To accomplish this, I created this model,         including a model manager:
  
  ```
  class Review(models.Model):
    game_title = models.CharField(max_length=50)
    game_score = models.PositiveIntegerField(default=1)
    review_text = models.TextField(default='')
    reviewer_name = models.CharField(max_length=50)
    date_reviewed = models.DateField()
    
    Reviews = models.Manager()
  ```
    
  I then created a views function in order to allow the user to save their review to the database, as well as render the actual page for doing so:
    
  ```
  def video_game_create(request):
  form = ReviewForm(data=request.POST or None)
  if request.method == 'POST':
      if form.is_valid():
          form.save()
          # Redirects to home page after form is saved
          return redirect('video_game_display')
  content = {'form': form}
  return render(request, "VideoGameReviews/VideoGameReviews_create.html", content)
  ```
  
  The actual form itself was a Modelform, based on the Review model:
  
  ```
  class ReviewForm(ModelForm):
      class Meta:
          model = Review
          fields = '__all__'
          widgets = {
              # Here I am  assigning widgets and giving each field an attribute that I'm using for basic CSS styling
              'game_score': forms.Select(choices=SCORE_CHOICES, attrs={'class': 'create-form-fields'}),
              'date_reviewed': forms.SelectDateWidget(attrs={'class': 'create-form-fields'}),
              'game_title': forms.TextInput(attrs={'class': 'create-form-fields'}),
              'review_text': forms.Textarea(attrs={'class': 'create-form-fields'}),
              'reviewer_name': forms.TextInput(attrs={'class': 'create-form-fields'}),
          }
  ```
  
  The SCORE_CHOICES were just the numbers 1-10.
  
- ## Read
  The next two stories were concerned with both creating a page that listed every item saved to the database, and allowing a user to click on an item to that list     and be taken to a details page for that review. I needed to create more HTML pages for this functionality, but since we were working with Django, I utilized         template inheritance to speed up the process. First, I started on creating a page to show a list of all database items.
