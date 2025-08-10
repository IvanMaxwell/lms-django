# Jeevi Academy – Learning Management System (LMS)


## **Project Overview**

**Project Title:** *Jeevi Academy – Learning Management System (LMS)*
**Technology Stack:** Django, Django REST Framework, PostgreSQL, HTML/CSS/JS
**Target Audience:** Educational institutions, teachers, and students
**Purpose:** To provide a centralized, secure, and user-friendly platform for delivering, managing, and tracking online learning.

---

### **Introduction**

Education today is no longer confined to classrooms. Students expect flexibility, teachers need streamlined content delivery, and administrators require a clear overview of academic progress. Recognizing this shift, *Jeevi Academy* is designed as a **modern, scalable Learning Management System (LMS)** that caters to the operational and academic needs of an institute.

This project focuses on building a platform where:

* **Students** can enroll in courses, access learning materials, take quizzes, and track their progress.
* **Teachers** can create and manage courses, upload lessons, assign homework, and grade submissions.
* **Administrators** can oversee the entire learning ecosystem, from user management to performance analytics.

---

### **Objective**

The primary objective of *Jeevi Academy* is to provide a **robust, secure, and modular** LMS that:

* Supports multiple roles with distinct permissions (Admin, Teacher, Student).
* Streamlines academic workflows, reducing manual paperwork.
* Offers both synchronous and asynchronous learning opportunities.
* Tracks learner progress and provides insights through reports and analytics.

---

### **Scope**

The LMS is designed to handle the full cycle of online education, from course creation to student certification. The scope includes:

* **Course Management:** Create, organize, and publish courses with structured modules and lessons.
* **Content Delivery:** Support for videos, documents, and interactive materials.
* **Assessment Tools:** Quizzes, assignments, and automated grading.
* **Progress Tracking:** Visual dashboards for students and performance analytics for teachers.
* **Communication:** Notifications, announcements, and optional discussion boards.
* **Extensibility:** Modular architecture allowing easy feature addition in the future.

---

### **Key Features**

1. **Role-Based Access Control** — Secure authentication with separate dashboards for Admins, Teachers, and Students.
2. **Course & Lesson Management** — Structured modules with multimedia support.
3. **Assessment & Grading** — Auto-graded quizzes and manually graded assignments.
4. **Progress Tracking** — Real-time analytics for performance monitoring.
5. **Notifications System** — Email and in-app alerts for important updates.
6. **User-Friendly Interface** — Responsive design for desktops, tablets, and mobiles.

---





# Step-by-step guide:


## let’s break the **Jeevi Academy LMS** into clear, beginner-friendly **phases** so you know the full roadmap before diving into Phase 2.

---

## ** Project Roadmap (Phases)**

### **Phase 1 – Base Setup & Course Listing**  

* Set up Django project & apps.
* Create **Custom User Model** with roles (Admin, Teacher, Student).
* Create **Course model** & simple listing page.
* Register in admin for management.

---

### **Phase 2 – Enrollment & Student Dashboard**

* Allow students to **enroll in courses**.
* Create a **student dashboard** to list enrolled courses.
* Limit course detail access only to enrolled students.

---

### **Phase 3 – Modules & Lessons System**

* Create **Module model** (chapters/sections in a course).
* Create **Lesson model** (videos, PDFs, text).
* Display course → modules → lessons hierarchy.

---

### **Phase 4 – Quizzes & Assignments**

* Add **quiz model** with MCQs and grading.
* Add **assignment model** with file upload and teacher review.
* Show results to students in their dashboard.

---

### **Phase 5 – Notifications & Communication**

* In-app notifications (new lesson, new assignment, grades).
* Optional **discussion forum** or comment section under lessons.

---

### **Phase 6 – Progress Tracking & Reports**

* Track student completion percentage.
* Teacher dashboard to see students’ progress.
* Admin report system for overall analytics.

---

### **Phase 7 – Final Touches & Polish**

* Add **responsive front-end** (Bootstrap/Tailwind).
* Improve navigation & UI.
* Add **security features** (permissions, input validation).

---


# Phases one:
---

## **Step 1 — Create the Project**

```bash
# Create project folder
mkdir jeevi_academy
cd jeevi_academy

# Create virtual environment (keeps project packages separate)
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# Mac/Linux:
source venv/bin/activate

# Install Django (framework)
pip install django

# Start a new project named "config"
django-admin startproject config .
```

---

## **Step 2 — Create Accounts App**

```bash
# Create "accounts" app for user management
python manage.py startapp accounts
```

---

## **Step 3 — config/settings.py** (IMPORTANT LINES MARKED)

```python
# config/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',   # IMPORTANT: Admin panel app
    'django.contrib.auth',    # IMPORTANT: Authentication system
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',

    # Our custom apps
    'accounts',  # IMPORTANT: Our user app must be here
    'courses',   # Will be created later
]

# This tells Django to use our custom user model instead of default
AUTH_USER_MODEL = 'accounts.CustomUser'  # IMPORTANT: Must match appname.ModelName

# Static files setup (CSS, JS, Images)
STATIC_URL = 'static/'  # Can be changed later if needed
```

---

## **Step 4 — accounts/models.py**

```python
# accounts/models.py
from django.contrib.auth.models import AbstractUser  # IMPORTANT: Base class for custom users
from django.db import models

class CustomUser(AbstractUser):  # Inherits all fields from Django's default User
    # "choices" allows only specific role values
    ROLE_CHOICES = [
        ('admin', 'Admin'),     # Role for site-wide admin
        ('teacher', 'Teacher'), # Role for course creators
        ('student', 'Student'), # Role for learners
    ]
    role = models.CharField(
        max_length=10,          # IMPORTANT: Limits role string length
        choices=ROLE_CHOICES,   # Limits possible values
        default='student'       # Default if not specified
    )

    def __str__(self):
        # How the object is shown in admin and shell
        return self.username
```

---

## **Step 5 — Create Courses App**

```bash
python manage.py startapp courses
```

---

## **Step 6 — courses/models.py**

```python
# courses/models.py
from django.db import models
from django.conf import settings  # Gives access to AUTH_USER_MODEL

class Course(models.Model):
    title = models.CharField(max_length=200)  # Course name
    description = models.TextField(blank=True)  # Optional description
    teacher = models.ForeignKey(
        settings.AUTH_USER_MODEL,  # Links to user table
        on_delete=models.CASCADE,  # If teacher is deleted, delete their courses
        limit_choices_to={'role': 'teacher'}  # IMPORTANT: Only teachers can be linked
    )
    created_at = models.DateTimeField(auto_now_add=True)  # Auto sets time

    def __str__(self):
        return self.title
```

---

## **Step 7 — courses/views.py**

```python
# courses/views.py
from django.shortcuts import render
from .models import Course

def course_list(request):
    # Query all courses from database
    courses = Course.objects.all()  # IMPORTANT: Fetch data
    # Render HTML page with courses
    return render(request, 'courses/course_list.html', {'courses': courses})
```

---

## **Step 8 — courses/urls.py**

```python
# courses/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.course_list, name='course_list'),  # URL: /courses/
]
```

---

## **Step 9 — config/urls.py**

```python
# config/urls.py
from django.contrib import admin
from django.urls import path, include  # include() lets us add app URLs

urlpatterns = [
    path('admin/', admin.site.urls),               # Admin panel
    path('courses/', include('courses.urls')),     # Include course app URLs
]
```

---

## **Step 10 — Templates**

Create folder `courses/templates/courses/course_list.html`:

```html
<!-- courses/templates/courses/course_list.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Courses</title>
</head>
<body>
    <h1>Available Courses</h1>
    <ul>
        {% for course in courses %}
            <li>
                {{ course.title }}  
                (Teacher: {{ course.teacher.username }})
            </li>
        {% empty %}
            <li>No courses available.</li>
        {% endfor %}
    </ul>
</body>
</html>
```

---

## **Step 11 — Register Models in Admin**

```python
# accounts/admin.py
from django.contrib import admin
from .models import CustomUser

admin.site.register(CustomUser)  # IMPORTANT: Allows user management in admin

# courses/admin.py
from django.contrib import admin
from .models import Course

admin.site.register(Course)  # Shows courses in admin panel
```

---

## **Step 12 — Migrate & Test**

```bash
python manage.py makemigrations
python manage.py migrate

# Create admin user
python manage.py createsuperuser

# Run server
python manage.py runserver
```

Go to:

* **Admin:** `http://127.0.0.1:8000/admin` → login → add courses.
* **Courses Page:** `http://127.0.0.1:8000/courses/`

---

 Now you have:

* **Custom user system with roles**
* **Course creation & listing**
* **Admin panel setup**

---



# Phase 2 — Enrollment & Student Dashboard

---

## Step 1 — Update Courses App for Enrollment

```python
# courses/models.py
from django.db import models
from django.conf import settings

class Course(models.Model):
    title = models.CharField(max_length=200)
    description = models.TextField(blank=True)
    teacher = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
        limit_choices_to={'role': 'teacher'}
    )
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title

class Enrollment(models.Model):
    student = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
        limit_choices_to={'role': 'student'}  # Only students can enroll
    )
    course = models.ForeignKey(Course, on_delete=models.CASCADE)
    enrolled_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        unique_together = ('student', 'course')  # Prevent duplicate enrollments

    def __str__(self):
        return f"{self.student.username} → {self.course.title}"
```

**Key points:**

* `unique_together` is **important** to prevent duplicate enrollment records.
* Keeping enrollment separate from `Course` makes the system **scalable** (many-to-many relationship).

---

## Step 2 — Create Forms for Enrollment

```python
# courses/forms.py
from django import forms
from .models import Enrollment

class EnrollmentForm(forms.ModelForm):
    class Meta:
        model = Enrollment
        fields = []  # No manual input needed, will be set in view
```

**Why empty fields?**

* We don’t want users choosing other students or courses manually — the view will set these from the logged-in user and the course ID.

---

## Step 3 — Create Views for Enrollment

```python
# courses/views.py
from django.contrib.auth.mixins import LoginRequiredMixin
from django.views.generic import ListView, DetailView, View
from django.shortcuts import get_object_or_404, redirect
from .models import Course, Enrollment
from .forms import EnrollmentForm

class CourseListView(ListView):
    model = Course
    template_name = 'courses/course_list.html'
    context_object_name = 'courses'

class CourseDetailView(DetailView):
    model = Course
    template_name = 'courses/course_detail.html'
    context_object_name = 'course'

class EnrollView(LoginRequiredMixin, View):
    def post(self, request, pk):
        course = get_object_or_404(Course, pk=pk)
        Enrollment.objects.get_or_create(student=request.user, course=course)
        return redirect('student_dashboard')

class StudentDashboardView(LoginRequiredMixin, ListView):
    template_name = 'courses/student_dashboard.html'
    context_object_name = 'enrollments'

    def get_queryset(self):
        return Enrollment.objects.filter(student=self.request.user)
```

**Scalable choices:**

* `ListView` and `DetailView` keep code DRY.
* `get_or_create` avoids duplicate entries.
* `LoginRequiredMixin` ensures only logged-in users access these features.

---

## Step 4 — Update URLs

```python
# courses/urls.py
from django.urls import path
from .views import CourseListView, CourseDetailView, EnrollView, StudentDashboardView

urlpatterns = [
    path('', CourseListView.as_view(), name='course_list'),
    path('<int:pk>/', CourseDetailView.as_view(), name='course_detail'),
    path('<int:pk>/enroll/', EnrollView.as_view(), name='enroll_course'),
    path('dashboard/', StudentDashboardView.as_view(), name='student_dashboard'),
]
```

---

## Step 5 — Templates: Course Detail

```html
<!-- courses/templates/courses/course_detail.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{{ course.title }}</title>
</head>
<body>
    <h1>{{ course.title }}</h1>
    <p>{{ course.description }}</p>
    {% if user.is_authenticated and user.role == 'student' %}
        <form method="post" action="{% url 'enroll_course' course.pk %}">
            {% csrf_token %}
            <button type="submit">Enroll</button>
        </form>
    {% endif %}
</body>
</html>
```

**Why check `user.role`?**

* Avoid showing “Enroll” button to teachers/admins.

---

## Step 6 — Templates: Student Dashboard

```html
<!-- courses/templates/courses/student_dashboard.html -->
<!DOCTYPE html>
<html>
<head>
    <title>My Dashboard</title>
</head>
<body>
    <h1>My Courses</h1>
    <ul>
        {% for enrollment in enrollments %}
            <li>{{ enrollment.course.title }} (Teacher: {{ enrollment.course.teacher.username }})</li>
        {% empty %}
            <li>You are not enrolled in any courses yet.</li>
        {% endfor %}
    </ul>
</body>
</html>
```

---

## Step 7 — Admin Registration

```python
# courses/admin.py
from django.contrib import admin
from .models import Course, Enrollment

admin.site.register(Course)
admin.site.register(Enrollment)
```

---

## Step 8 — Apply Migrations

```bash
python manage.py makemigrations
python manage.py migrate
```

---

## Step 9 — Create Test Data

```bash
python manage.py createsuperuser
```

Then:

* Login to admin
* Create a **Teacher** user
* Create a **Course** for that teacher

---

## Step 10 — Test Enrollment

1. Log in as a student.
2. Go to `http://127.0.0.1:8000/courses/`
3. Click a course → enroll.
4. Go to `/courses/dashboard/` to see enrolled courses.

---

## Step 11 — Scalability Notes

* This structure can handle **thousands of courses** and **millions of enrollments** without performance loss (with indexing).
* You can easily attach lessons, quizzes, progress tracking without touching existing logic.


---


# Phase 3 — Modules & Lessons,

---


## Step 1 — Models: Module and Lesson

```python
# courses/models.py
from django.db import models
from django.conf import settings

class Module(models.Model):
    # The course this module belongs to
    course = models.ForeignKey(
        'Course',                  # IMPORTANT: references Course model in same app
        on_delete=models.CASCADE,  # If a course is deleted, delete its modules
        related_name='modules'     # Allows course.modules.all() for reverse lookup
    )
    title = models.CharField(max_length=200)  # Human-friendly title
    description = models.TextField(blank=True)  # Optional short summary
    order = models.PositiveIntegerField(default=0)  # Order of modules within a course

    class Meta:
        ordering = ['order']  # IMPORTANT: ensures modules are returned in order

    def __str__(self):
        return f"{self.course.title} — {self.title}"


class Lesson(models.Model):
    CONTENT_TYPE_CHOICES = (
        ('video', 'Video'),
        ('text', 'Text'),
        ('file', 'File'),
        ('url', 'External URL'),
    )

    module = models.ForeignKey(
        Module,
        on_delete=models.CASCADE,     # If module deleted, delete its lessons
        related_name='lessons'        # Allows module.lessons.all()
    )
    title = models.CharField(max_length=200)     # Lesson title
    content_type = models.CharField(
        max_length=10,
        choices=CONTENT_TYPE_CHOICES,
        default='text'
    )
    content_text = models.TextField(blank=True)  # For text lessons
    content_file = models.FileField(
        upload_to='lessons/files/', blank=True, null=True
    )                                            # For uploads (PDFs, zips)
    external_url = models.URLField(blank=True)   # For embedding external video links
    order = models.PositiveIntegerField(default=0)  # Order of lessons inside a module
    duration_seconds = models.PositiveIntegerField(null=True, blank=True)  # Optional metadata

    class Meta:
        ordering = ['order']  # IMPORTANT: ensures lessons are in the right order

    def __str__(self):
        return f"{self.module.title} — {self.title}"
```

**Notes**

* `related_name` helps query related objects conveniently.
* `ordering` in `Meta` guarantees modules/lessons are always returned in the correct sequence.
* We use simple content fields to support multiple lesson types. For production video, use a media service (S3 + CDN) and store URLs.

---

## Step 2 — Admin Registration (for easy content management)

```python
# courses/admin.py
from django.contrib import admin
from .models import Module, Lesson

@admin.register(Module)
class ModuleAdmin(admin.ModelAdmin):
    list_display = ('title', 'course', 'order')   # Shown columns in admin list
    list_filter = ('course',)                     # Filter by course
    ordering = ('course', 'order')                # Order modules in admin

@admin.register(Lesson)
class LessonAdmin(admin.ModelAdmin):
    list_display = ('title', 'module', 'content_type', 'order')
    list_filter = ('content_type', 'module__course')
    ordering = ('module', 'order')
```

**Notes**

* Admin classes give editors a clear UI to manage modules and lessons.
* `list_filter` and `ordering` help manage large content sets.

---

## Step 3 — Services / Helpers: Access check (reusable)

```python
# courses/services.py
from .models import Enrollment

def user_is_enrolled(user, course):
    """
    Return True if the given user is enrolled in the course.
    Use this function anywhere you need to guard content.
    """
    if not user.is_authenticated:
        return False
    # IMPORTANT: using exists() for efficient DB hit (returns boolean)
    return Enrollment.objects.filter(student=user, course=course).exists()
```

**Notes**

* Centralizing checks avoids duplicate queries and keeps logic reusable.

---

## Step 4 — Views: Module list and Lesson detail (class-based)

```python
# courses/views.py
from django.views.generic import ListView, DetailView
from django.shortcuts import get_object_or_404
from django.http import Http404
from django.contrib.auth.mixins import LoginRequiredMixin
from .models import Module, Lesson, Course
from .services import user_is_enrolled

class ModuleListView(LoginRequiredMixin, ListView):
    """
    List modules for a given course.
    Only accessible to users enrolled in that course.
    """
    model = Module
    template_name = 'courses/module_list.html'
    context_object_name = 'modules'

    def get_queryset(self):
        # Get the course from URL kwargs; raises 404 if not found
        course_pk = self.kwargs.get('course_pk')
        course = get_object_or_404(Course, pk=course_pk)
        # Guard: only enrolled students (or teachers/admins) can view modules
        if not (self.request.user.role == 'teacher' and course.teacher == self.request.user) and not user_is_enrolled(self.request.user, course):
            raise Http404("You do not have access to this course.")  # IMPORTANT: denies access safely
        # Return modules for this course
        return Module.objects.filter(course=course).order_by('order')

    def get_context_data(self, **kwargs):
        ctx = super().get_context_data(**kwargs)
        ctx['course'] = get_object_or_404(Course, pk=self.kwargs.get('course_pk'))
        return ctx


class LessonDetailView(LoginRequiredMixin, DetailView):
    """
    Show a single lesson. Checks for enrollment before returning content.
    """
    model = Lesson
    template_name = 'courses/lesson_detail.html'
    context_object_name = 'lesson'

    def get_object(self, queryset=None):
        lesson = super().get_object(queryset)
        course = lesson.module.course
        # Only allow teachers of the course, admins, or enrolled students
        if not (self.request.user.role == 'teacher' and course.teacher == self.request.user) and not user_is_enrolled(self.request.user, course):
            raise Http404("You do not have access to this lesson.")  # IMPORTANT: secure access
        return lesson
```

**Notes**

* Use `LoginRequiredMixin` to force login.
* `get_queryset` and `get_object` guard against unauthorized access.
* This pattern is scalable and secure.

---

## Step 5 — URLs: Module and Lesson routes

```python
# courses/urls.py
from django.urls import path
from .views import ModuleListView, LessonDetailView

urlpatterns = [
    # Modules list for a course: /courses/<course_pk>/modules/
    path('<int:course_pk>/modules/', ModuleListView.as_view(), name='module_list'),

    # Lesson detail: /courses/<course_pk>/modules/<module_pk>/lessons/<pk>/
    # Note: LessonDetailView uses lesson pk; course_pk is used for URL clarity and checks
    path('<int:course_pk>/modules/<int:module_pk>/lessons/<int:pk>/', LessonDetailView.as_view(), name='lesson_detail'),
]
```

**Notes**

* URLs are nested for clarity and future RESTful API mapping.
* This layout is friendly to breadcrumb UI and analytics.

---

## Step 6 — Templates: Module list

```html
<!-- courses/templates/courses/module_list.html -->
<!DOCTYPE html>
<html>
<head><title>{{ course.title }} — Modules</title></head>
<body>
  <h1>{{ course.title }}</h1>
  <h2>Modules</h2>
  <ul>
    {% for module in modules %}
      <li>
        <a href="{% url 'lesson_list_for_module' course.pk module.pk %}">
          {{ module.order }}. {{ module.title }}
        </a>
        <p>{{ module.description }}</p>
      </li>
    {% empty %}
      <li>No modules yet.</li>
    {% endfor %}
  </ul>
  <a href="{% url 'student_dashboard' %}">Back to Dashboard</a>
</body>
</html>
```

**Note:** we will create `lesson_list_for_module` route in Step 7 (or you can link directly to the first lesson).

---

## Step 7 — (Optional) Lesson list per module view & template

If you prefer listing lessons instead of linking module → lesson directly, add:

```python
# courses/views.py (append)
class LessonListView(LoginRequiredMixin, ListView):
    model = Lesson
    template_name = 'courses/lesson_list.html'
    context_object_name = 'lessons'

    def get_queryset(self):
        module = get_object_or_404(Module, pk=self.kwargs.get('module_pk'))
        course = module.course
        if not user_is_enrolled(self.request.user, course) and not (self.request.user.role == 'teacher' and course.teacher == self.request.user):
            raise Http404
        return Lesson.objects.filter(module=module).order_by('order')
```

```python
# courses/urls.py (append)
path('<int:course_pk>/modules/<int:module_pk>/lessons/', LessonListView.as_view(), name='lesson_list_for_module'),
```

Template: `courses/templates/courses/lesson_list.html` (similar to module template, lists lessons with links to `lesson_detail`).

---

## Step 8 — Templates: Lesson detail (secure display)

```html
<!-- courses/templates/courses/lesson_detail.html -->
<!DOCTYPE html>
<html>
<head><title>{{ lesson.title }}</title></head>
<body>
  <h1>{{ lesson.title }}</h1>
  <p>Module: {{ lesson.module.title }}</p>

  {% if lesson.content_type == 'text' %}
    <div>{{ lesson.content_text|linebreaks }}</div>
  {% elif lesson.content_type == 'file' and lesson.content_file %}
    <a href="{{ lesson.content_file.url }}">Download attached file</a>
  {% elif lesson.content_type == 'video' and lesson.external_url %}
    <!-- For embedded video, use iframe carefully (trusted providers) -->
    <iframe src="{{ lesson.external_url }}" width="800" height="450" allowfullscreen></iframe>
  {% elif lesson.content_type == 'url' and lesson.external_url %}
    <p><a href="{{ lesson.external_url }}" target="_blank">Open resource</a></p>
  {% endif %}

  <p><a href="{% url 'module_list' lesson.module.course.pk %}">Back to Modules</a></p>
</body>
</html>
```

**Security note**

* Always sanitize/validate URLs and uploaded files in production.
* Use storage backends (S3) and signed URLs for private files.

---

## Step 9 — Migrations & Media settings

1. Create migrations and migrate:

```bash
python manage.py makemigrations
python manage.py migrate
```

2. In `settings.py`, configure media (development):

```python
MEDIA_URL = '/media/'          # IMPORTANT: base URL for media files
MEDIA_ROOT = BASE_DIR / 'media'  # IMPORTANT: local folder (use S3 in prod)
```

3. In `config/urls.py` (development only), serve media:

```python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... existing urls ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

**Note:** Serving media in production should be handled by object storage + CDN.

---

## Step 10 — Unit Tests (basic)

```python
# courses/tests/test_models.py
from django.test import TestCase
from django.contrib.auth import get_user_model
from ..models import Course, Module, Lesson, Enrollment

User = get_user_model()

class ContentModelTests(TestCase):
    def setUp(self):
        # Create users and course
        self.teacher = User.objects.create_user(username='t1', password='pw', role='teacher')
        self.student = User.objects.create_user(username='s1', password='pw', role='student')
        self.course = Course.objects.create(title='C1', description='d', teacher=self.teacher)
        self.module = Module.objects.create(course=self.course, title='M1', order=1)

    def test_lesson_creation_and_order(self):
        a = Lesson.objects.create(module=self.module, title='L1', order=2)
        b = Lesson.objects.create(module=self.module, title='L2', order=1)
        lessons = list(self.module.lessons.all())
        self.assertEqual(lessons[0].title, 'L2')  # ordering by order field

    def test_enrollment_and_access(self):
        Enrollment.objects.create(student=self.student, course=self.course)
        self.assertTrue(Enrollment.objects.filter(student=self.student, course=self.course).exists())
```

**Notes**

* Tests ensure ordering and basic relationships work. Expand tests for view permissions and templates.

---

## Step 11 — Scalability & Maintainability Notes

* Use `ordering` fields for deterministic sequence (use drag-and-drop admin for UX later).
* For large content libraries, add DB indexes on frequently filtered fields (e.g., `module`, `course`).
* Store large media in S3/MinIO + configure signed URLs for private files.
* Consider background tasks (Celery) for heavy processing (video transcoding, generating thumbnails).
* Add caching on module/lesson lists for high-read use-cases (e.g., `cache_page` or low-level cache with Redis).

---

# Phase 4 — Quizzes & Assignments

---

## Step 1 — Models: Quiz, Question, Choice, Attempt

```python
# courses/models.py (append or new file)
from django.db import models
from django.conf import settings
from django.utils import timezone

class Quiz(models.Model):
    course = models.ForeignKey('Course', on_delete=models.CASCADE, related_name='quizzes')
    title = models.CharField(max_length=255)
    description = models.TextField(blank=True)
    time_limit_minutes = models.PositiveIntegerField(null=True, blank=True)  # Optional time limit
    published = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.course.title} - {self.title}"


class Question(models.Model):
    QUESTION_TYPE_CHOICES = (
        ('mcq', 'Multiple Choice'),
        ('tf', 'True/False'),
        # future: ('short', 'Short Answer')
    )
    quiz = models.ForeignKey(Quiz, on_delete=models.CASCADE, related_name='questions')
    text = models.CharField(max_length=1000)
    question_type = models.CharField(max_length=10, choices=QUESTION_TYPE_CHOICES, default='mcq')
    points = models.PositiveIntegerField(default=1)

    def __str__(self):
        return f"Q: {self.text[:60]}"


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE, related_name='choices')
    text = models.CharField(max_length=500)
    is_correct = models.BooleanField(default=False)  # IMPORTANT: used for auto-grading

    def __str__(self):
        return f"Choice: {self.text[:50]} (Correct={self.is_correct})"


class Attempt(models.Model):
    student = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name='quiz_attempts')
    quiz = models.ForeignKey(Quiz, on_delete=models.CASCADE, related_name='attempts')
    started_at = models.DateTimeField(auto_now_add=True)
    submitted_at = models.DateTimeField(null=True, blank=True)
    score = models.DecimalField(max_digits=6, decimal_places=2, null=True, blank=True)  # Percentage or points
    completed = models.BooleanField(default=False)

    class Meta:
        unique_together = ('student', 'quiz')  # Simple constraint: one attempt per student per quiz (change if multiple attempts allowed)

    def __str__(self):
        return f"{self.student.username} - {self.quiz.title}"
```

**Notes**

* `is_correct` on `Choice` is critical for auto-grading.
* `unique_together` restricts duplicate attempts; adjust if you want multiple attempts with a separate Attempt record per try.

---

## Step 2 — Models: Answer (student's selection) and Assignment/Submission

```python
# courses/models.py (continuation)
class Answer(models.Model):
    attempt = models.ForeignKey(Attempt, on_delete=models.CASCADE, related_name='answers')
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    selected_choice = models.ForeignKey(Choice, on_delete=models.CASCADE, null=True, blank=True)  # For MCQ/TF
    # For future support: short_text = models.TextField(blank=True)

    def __str__(self):
        return f"{self.attempt} - {self.question.text[:30]}"


class Assignment(models.Model):
    course = models.ForeignKey('Course', on_delete=models.CASCADE, related_name='assignments')
    title = models.CharField(max_length=255)
    description = models.TextField(blank=True)
    due_date = models.DateTimeField(null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"{self.course.title} - {self.title}"


class Submission(models.Model):
    assignment = models.ForeignKey(Assignment, on_delete=models.CASCADE, related_name='submissions')
    student = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE, related_name='submissions')
    file = models.FileField(upload_to='assignments/')  # IMPORTANT: restrict types/size in production
    submitted_at = models.DateTimeField(auto_now_add=True)
    grade = models.DecimalField(max_digits=6, decimal_places=2, null=True, blank=True)
    feedback = models.TextField(blank=True)

    class Meta:
        unique_together = ('assignment', 'student')  # One submission per student (change if multiple allowed)

    def __str__(self):
        return f"{self.student.username} - {self.assignment.title}"
```

**Notes**

* File uploads must be validated (type/size) and should use S3 in production.

---

## Step 3 — Admin: Register Quiz & Assignment Models

```python
# courses/admin.py (append)
from .models import Quiz, Question, Choice, Attempt, Answer, Assignment, Submission

class ChoiceInline(admin.TabularInline):
    model = Choice
    extra = 2  # show two empty choice rows by default

class QuestionInline(admin.StackedInline):
    model = Question
    extra = 1

@admin.register(Quiz)
class QuizAdmin(admin.ModelAdmin):
    list_display = ('title', 'course', 'published', 'time_limit_minutes')
    inlines = [QuestionInline]
    search_fields = ('title',)

@admin.register(Question)
class QuestionAdmin(admin.ModelAdmin):
    list_display = ('text', 'quiz', 'question_type', 'points')
    inlines = [ChoiceInline]

@admin.register(Assignment)
class AssignmentAdmin(admin.ModelAdmin):
    list_display = ('title', 'course', 'due_date')
```

**Notes**

* Inline editing makes creating questions and choices faster in admin.

---

## Step 4 — Forms: Quiz Attempt & Assignment Submission

```python
# courses/forms.py (append)
from django import forms
from .models import Submission

class SubmitAnswerForm(forms.Form):
    # For MCQ we'll accept a choice id
    choice_id = forms.IntegerField(required=False)  # set in view from POST

class SubmissionForm(forms.ModelForm):
    class Meta:
        model = Submission
        fields = ['file']  # IMPORTANT: only allow file input from student
```

**Notes**

* For richer UIs, use JavaScript to submit answers via AJAX and avoid full page reloads.

---

## Step 5 — Services: Auto-grading logic

```python
# courses/services.py (append)
from decimal import Decimal

def grade_attempt(attempt):
    """
    Auto-grade MCQ/TF answers for an attempt.
    Sets attempt.score and marks completed.
    """
    total_points = 0
    earned = 0

    questions = attempt.quiz.questions.all().prefetch_related('choices')
    for q in questions:
        total_points += q.points
        ans = attempt.answers.filter(question=q).first()
        if not ans:
            continue
        # For MCQ/TF, check selected_choice.is_correct
        if ans.selected_choice and ans.selected_choice.is_correct:
            earned += q.points

    attempt.score = (Decimal(earned) / Decimal(total_points) * 100) if total_points > 0 else Decimal(0)
    attempt.submitted_at = timezone.now()
    attempt.completed = True
    attempt.save()
    return attempt.score
```

**Notes**

* Returning percentage (0–100) is convenient for UI; store raw points if needed.

---

## Step 6 — Views: Start Quiz, Submit Answer, View Result

```python
# courses/views.py (append)
from django.views import View
from django.shortcuts import render, redirect
from .models import Quiz, Attempt, Question, Choice, Answer
from .forms import SubmitAnswerForm
from .services import grade_attempt

class StartQuizView(LoginRequiredMixin, View):
    """
    Start or get existing attempt for a quiz.
    """
    def get(self, request, pk):
        quiz = get_object_or_404(Quiz, pk=pk, published=True)
        # Ensure student is enrolled
        from .services import user_is_enrolled
        if not user_is_enrolled(request.user, quiz.course):
            raise Http404("You are not enrolled in this course.")
        attempt, created = Attempt.objects.get_or_create(student=request.user, quiz=quiz)
        # Redirect to first question (simple approach)
        first_q = quiz.questions.order_by('id').first()
        if first_q:
            return redirect('quiz_question', attempt_pk=attempt.pk, question_pk=first_q.pk)
        return redirect('quiz_result', pk=attempt.pk)


class QuizQuestionView(LoginRequiredMixin, View):
    """
    Display a single question and accept an answer (POST).
    """
    def get(self, request, attempt_pk, question_pk):
        attempt = get_object_or_404(Attempt, pk=attempt_pk, student=request.user)
        question = get_object_or_404(Question, pk=question_pk, quiz=attempt.quiz)
        choices = question.choices.all()
        return render(request, 'courses/quiz_question.html', {'question': question, 'choices': choices, 'attempt': attempt})

    def post(self, request, attempt_pk, question_pk):
        attempt = get_object_or_404(Attempt, pk=attempt_pk, student=request.user)
        question = get_object_or_404(Question, pk=question_pk, quiz=attempt.quiz)
        choice_id = request.POST.get('choice')
        if choice_id:
            choice = get_object_or_404(Choice, pk=int(choice_id), question=question)
            # Save or update answer
            answer, _ = Answer.objects.update_or_create(
                attempt=attempt,
                question=question,
                defaults={'selected_choice': choice}
            )
        # Find next question
        next_q = attempt.quiz.questions.filter(pk__gt=question.pk).order_by('pk').first()
        if next_q:
            return redirect('quiz_question', attempt_pk=attempt.pk, question_pk=next_q.pk)
        # No more questions → grade
        grade_attempt(attempt)
        return redirect('quiz_result', pk=attempt.pk)
```

**Notes**

* Views use `get_or_create` and `update_or_create` for idempotency.
* Template names referenced below.

---

## Step 7 — Views: Display Quiz Result

```python
# courses/views.py (append)
class QuizResultView(LoginRequiredMixin, DetailView):
    model = Attempt
    template_name = 'courses/quiz_result.html'
    context_object_name = 'attempt'

    def get_queryset(self):
        # Student can only view own attempts (and teachers/admins could be allowed)
        return Attempt.objects.filter(student=self.request.user)
```

**Notes**

* Restricting queryset is important for security.

---

## Step 8 — Views: Assignment List, Submit & Grade

```python
# courses/views.py (append)
from .forms import SubmissionForm
from .models import Assignment, Submission

class AssignmentListView(LoginRequiredMixin, ListView):
    template_name = 'courses/assignment_list.html'
    context_object_name = 'assignments'

    def get_queryset(self):
        # Show assignments for the course(s) the user is enrolled in
        # For simplicity: show assignments of a single course if course_pk provided
        course_pk = self.kwargs.get('course_pk')
        if course_pk:
            return Assignment.objects.filter(course__pk=course_pk)
        return Assignment.objects.none()

class SubmitAssignmentView(LoginRequiredMixin, View):
    def get(self, request, pk):
        form = SubmissionForm()
        return render(request, 'courses/submit_assignment.html', {'form': form})

    def post(self, request, pk):
        assignment = get_object_or_404(Assignment, pk=pk)
        form = SubmissionForm(request.POST, request.FILES)
        if form.is_valid():
            # Save submission; prevent duplicates if necessary
            submission, created = Submission.objects.update_or_create(
                assignment=assignment,
                student=request.user,
                defaults={'file': form.cleaned_data['file']}
            )
            return redirect('assignment_list', course_pk=assignment.course.pk)
        return render(request, 'courses/submit_assignment.html', {'form': form})
```

**Notes**

* `update_or_create` ensures resubmissions replace previous files; change behavior if multiple submissions are required.

---

## Step 9 — URLs: Quiz & Assignment Routes

```python
# courses/urls.py (append)
from .views import (
    StartQuizView, QuizQuestionView, QuizResultView,
    AssignmentListView, SubmitAssignmentView
)

urlpatterns += [
    # Quiz flow
    path('quiz/<int:pk>/start/', StartQuizView.as_view(), name='start_quiz'),
    path('quiz/attempt/<int:attempt_pk>/question/<int:question_pk>/', QuizQuestionView.as_view(), name='quiz_question'),
    path('quiz/result/<int:pk>/', QuizResultView.as_view(), name='quiz_result'),

    # Assignments
    path('course/<int:course_pk>/assignments/', AssignmentListView.as_view(), name='assignment_list'),
    path('assignment/<int:pk>/submit/', SubmitAssignmentView.as_view(), name='submit_assignment'),
]
```

**Notes**

* Keep route names consistent and predictable for templates and JS.

---

## Step 10 — Templates: Quiz Question & Result (simplified)

`courses/templates/courses/quiz_question.html` (simplified)

```html
<h1>Question</h1>
<form method="post">
  {% csrf_token %}
  <p>{{ question.text }}</p>
  {% for choice in choices %}
    <label>
      <input type="radio" name="choice" value="{{ choice.pk }}">
      {{ choice.text }}
    </label><br>
  {% endfor %}
  <button type="submit">Next</button>
</form>
```

`courses/templates/courses/quiz_result.html` (simplified)

```html
<h1>Quiz Result</h1>
<p>Score: {{ attempt.score }}</p>
<p>Completed at: {{ attempt.submitted_at }}</p>
```

**Notes**

* Improve UX later with AJAX, progress bars, and timed countdowns.

---

## Step 11 — Tests: Basic grading & submission tests

```python
# courses/tests/test_quiz_assignment.py
from django.test import TestCase
from django.contrib.auth import get_user_model
from ..models import Course, Quiz, Question, Choice, Attempt, Assignment
from django.urls import reverse

User = get_user_model()

class QuizAssignmentTests(TestCase):
    def setUp(self):
        self.teacher = User.objects.create_user(username='t', password='pw', role='teacher')
        self.student = User.objects.create_user(username='s', password='pw', role='student')
        self.course = Course.objects.create(title='C', description='d', teacher=self.teacher)
        self.quiz = Quiz.objects.create(course=self.course, title='Q1', published=True)
        q = Question.objects.create(quiz=self.quiz, text='Is sky blue?', question_type='tf', points=1)
        Choice.objects.create(question=q, text='True', is_correct=True)
        Choice.objects.create(question=q, text='False', is_correct=False)

    def test_auto_grading(self):
        self.client.login(username='s', password='pw')
        # Enroll student
        from ..models import Enrollment
        Enrollment.objects.create(student=self.student, course=self.course)
        # Start attempt
        attempt = Attempt.objects.create(student=self.student, quiz=self.quiz)
        # Answer question
        question = self.quiz.questions.first()
        correct_choice = question.choices.filter(is_correct=True).first()
        from ..models import Answer
        Answer.objects.create(attempt=attempt, question=question, selected_choice=correct_choice)
        # Grade
        from ..services import grade_attempt
        score = grade_attempt(attempt)
        self.assertGreater(score, 0)
```

**Notes**

* Expand tests to cover time limits, permissions, and submission edge cases.

---

## Step 12 — Scalability & Production Notes

* For **timed quizzes**, control time server-side (store `started_at` and compare to `time_limit_minutes`) and reject late submissions.
* For high-concurrency grading: run `grade_attempt` in a background task (Celery) if grading becomes expensive.
* Use DB indexes on `Attempt.student`, `Question.quiz` and `Submission.assignment` for performance.
* Store assignment files and lesson files on S3 / object storage with signed URLs to keep the app stateless.
* For academic integrity: consider IP logging, randomized question ordering, question pools, and proctoring integrations if needed.
* For many quiz attempts/large question banks, consider separating read-heavy quiz APIs with caching and paginated question loads.

---

# Phase 5 — Notifications & Communication


## Step 1 — Models: Notification & Thread/Comment (basic)

```python
# notifications/models.py
from django.db import models
from django.conf import settings

class Notification(models.Model):
    """
    In-app notification for a single user.
    """
    user = models.ForeignKey(
        settings.AUTH_USER_MODEL,      # IMPORTANT: links to CustomUser
        on_delete=models.CASCADE,
        related_name='notifications'
    )
    title = models.CharField(max_length=255)     # Short headline
    message = models.TextField()                 # Full message/body
    read = models.BooleanField(default=False)    # Mark as read/unread
    created_at = models.DateTimeField(auto_now_add=True)  # Timestamp
    # Optional generic relation to link to other objects (course, assignment, etc.)
    content_type = models.ForeignKey('contenttypes.ContentType', null=True, blank=True, on_delete=models.SET_NULL)
    object_id = models.PositiveIntegerField(null=True, blank=True)
    # IMPORTANT: Do not rely on generic links for critical security checks; use IDs only for reference.

    class Meta:
        ordering = ['-created_at']  # Newest first

    def __str__(self):
        return f"[{'R' if self.read else 'U'}] {self.user.username}: {self.title}"


class Thread(models.Model):
    """
    Optional discussion thread attached to a lesson or course.
    """
    title = models.CharField(max_length=255)
    created_by = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    # Topics can be attached to content similarly (omitted for brevity)

    def __str__(self):
        return self.title


class Comment(models.Model):
    """
    Comments within a thread (simple forum).
    """
    thread = models.ForeignKey(Thread, on_delete=models.CASCADE, related_name='comments')
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    body = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    edited = models.BooleanField(default=False)

    def __str__(self):
        return f"{self.author.username}: {self.body[:50]}"
```

**Notes**

* Use `contenttypes` only for optional linking — it’s flexible but adds complexity.
* Keep notifications lightweight (avoid storing large payloads).

---

## Step 2 — Admin Registration

```python
# notifications/admin.py
from django.contrib import admin
from .models import Notification, Thread, Comment

@admin.register(Notification)
class NotificationAdmin(admin.ModelAdmin):
    list_display = ('user', 'title', 'read', 'created_at')
    list_filter = ('read', 'created_at')
    search_fields = ('title', 'message', 'user__username')

@admin.register(Thread)
class ThreadAdmin(admin.ModelAdmin):
    list_display = ('title', 'created_by', 'created_at')

@admin.register(Comment)
class CommentAdmin(admin.ModelAdmin):
    list_display = ('thread', 'author', 'created_at')
    search_fields = ('body', 'author__username')
```

**Notes**

* Admin enables manual moderation and inspection.

---

## Step 3 — Services: Notification creator helper

```python
# notifications/services.py
from .models import Notification
from django.contrib.contenttypes.models import ContentType

def create_notification(user, title, message, related_obj=None):
    """
    Create and return a Notification instance.
    - user: User instance (recipient)
    - title: short string
    - message: detailed string (limit length on sender side)
    - related_obj: optional model instance to link (Course, Assignment, etc.)
    """
    payload = {
        'user': user,
        'title': title,
        'message': message,
    }
    if related_obj is not None:
        payload['content_type'] = ContentType.objects.get_for_model(type(related_obj))
        payload['object_id'] = related_obj.pk

    return Notification.objects.create(**payload)
```

**Notes**

* Centralized creator means any app can call `create_notification(...)` without duplicating logic.

---

## Step 4 — Signals: Trigger notification on events (example: new lesson)

```python
# courses/signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import Lesson
from notifications.services import create_notification
from .models import Course
from .models import Enrollment

@receiver(post_save, sender=Lesson)
def notify_new_lesson(sender, instance, created, **kwargs):
    """
    When a new lesson is created, notify all enrolled students.
    WARNING: For very large courses, do this asynchronously (Celery).
    """
    if not created:
        return
    course = instance.module.course
    # Fetch enrolled students
    student_qs = Enrollment.objects.filter(course=course).select_related('student')
    for enrollment in student_qs:
        create_notification(
            user=enrollment.student,
            title=f"New lesson: {instance.title}",
            message=f"A new lesson has been added to {course.title}: {instance.title}",
            related_obj=instance
        )
```

**Important**

* For courses with thousands of students, the loop above must be offloaded to a background worker — do not run in request/transaction.

---

## Step 5 — Email notifications (Celery task stub)

```python
# notifications/tasks.py
from celery import shared_task
from django.core.mail import send_mail
from django.conf import settings

@shared_task
def send_notification_email(user_id, subject, message):
    """
    Background task to send email notifications.
    Call this from create_notification if you want email-as-well.
    """
    # Simplified: look up user email and send (add error handling in prod)
    from django.contrib.auth import get_user_model
    User = get_user_model()
    user = User.objects.get(pk=user_id)
    if not user.email:
        return False
    send_mail(
        subject,
        message,
        settings.DEFAULT_FROM_EMAIL,  # IMPORTANT: configure in settings
        [user.email],
        fail_silently=False,
    )
    return True
```

**Notes**

* `shared_task` requires Celery + broker (e.g., Redis).
* Never send emails synchronously in web requests for bulk actions.

---

## Step 6 — Views: List notifications & mark-as-read (class-based)

```python
# notifications/views.py
from django.views.generic import ListView, View
from django.shortcuts import redirect, get_object_or_404
from django.contrib.auth.mixins import LoginRequiredMixin
from .models import Notification

class NotificationListView(LoginRequiredMixin, ListView):
    model = Notification
    template_name = 'notifications/list.html'
    context_object_name = 'notifications'
    paginate_by = 20  # IMPORTANT: pagination for scalability

    def get_queryset(self):
        # Only show notifications for the current user
        return Notification.objects.filter(user=self.request.user).order_by('-created_at')


class MarkAsReadView(LoginRequiredMixin, View):
    def post(self, request, pk):
        # Mark a particular notification as read
        notif = get_object_or_404(Notification, pk=pk, user=request.user)
        notif.read = True
        notif.save(update_fields=['read'])
        return redirect('notifications:list')  # adjust name per your urlconf
```

**Notes**

* `paginate_by` prevents loading too many records at once.
* Use AJAX endpoints for smoother UX (optional).

---

## Step 7 — Templates: Notification list and single-row partial

`notifications/templates/notifications/list.html`

```html
<!DOCTYPE html>
<html>
<head><title>Notifications</title></head>
<body>
  <h1>Notifications</h1>
  <ul>
    {% for n in notifications %}
      <li style="{% if not n.read %}font-weight: bold;{% endif %}">
        <a href="{% url 'notifications:detail' n.pk %}">{{ n.title }}</a>
        <small>{{ n.created_at }}</small>

        <!-- Mark as read form -->
        <form method="post" action="{% url 'notifications:mark_read' n.pk %}">
          {% csrf_token %}
          <button type="submit">Mark as read</button>
        </form>
      </li>
    {% empty %}
      <li>No notifications.</li>
    {% endfor %}
  </ul>

  {% if is_paginated %}
    <div>
      {% if page_obj.has_previous %}<a href="?page={{ page_obj.previous_page_number }}">Previous</a>{% endif %}
      Page {{ page_obj.number }} of {{ page_obj.paginator.num_pages }}
      {% if page_obj.has_next %}<a href="?page={{ page_obj.next_page_number }}">Next</a>{% endif %}
    </div>
  {% endif %}
</body>
</html>
```

**Notes**

* For large lists, consider client-side lazy loading or infinite scroll.

---

## Step 8 — URLs: Notifications app

```python
# notifications/urls.py
from django.urls import path
from .views import NotificationListView, MarkAsReadView

app_name = 'notifications'

urlpatterns = [
    path('', NotificationListView.as_view(), name='list'),            # /notifications/
    path('mark-read/<int:pk>/', MarkAsReadView.as_view(), name='mark_read'),
    # Optionally add detail view and API endpoints
]
```

Include in project `config/urls.py`:

```python
path('notifications/', include('notifications.urls', namespace='notifications')),
```

---

## Step 9 — Signals (mark unread counters, optional)

If you want a realtime unread count in navbar, maintain a cached counter per user.

```python
# notifications/signals.py
from django.db.models.signals import post_save
from django.dispatch import receiver
from .models import Notification
from django.core.cache import cache

@receiver(post_save, sender=Notification)
def increment_unread_cache(sender, instance, created, **kwargs):
    if not created:
        return
    key = f"unread_count_{instance.user_id}"
    cache.incr(key, 1)  # Requires key to be initialized; handle exceptions in prod
```

**Notes**

* Use Redis cache for counters. Implement robust initialization and decrement on mark-read.

---

## Step 10 — Tests: Basic notification creation and view

```python
# notifications/tests/test_notifications.py
from django.test import TestCase
from django.contrib.auth import get_user_model
from django.urls import reverse
from ..models import Notification

User = get_user_model()

class NotificationTests(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(username='u', password='pw', role='student', email='u@example.com')
        self.client.login(username='u', password='pw')

    def test_create_and_list_notification(self):
        Notification.objects.create(user=self.user, title='T1', message='M1')
        response = self.client.get(reverse('notifications:list'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'T1')

    def test_mark_as_read(self):
        notif = Notification.objects.create(user=self.user, title='T2', message='M2')
        url = reverse('notifications:mark_read', args=[notif.pk])
        response = self.client.post(url, follow=True)
        notif.refresh_from_db()
        self.assertTrue(notif.read)
```

**Notes**

* Expand tests for bulk notifications and email task triggering.

---

## Step 11 — Real-time notifications (optional roadmap)

* For real-time push to connected browsers, adopt **Django Channels**:

  * Create channel groups per user (e.g., `user_{user_id}`).
  * When `create_notification` is called, publish an event to the channel and also save the Notification object.
  * Browser subscribes via WebSocket and updates the UI (unread count, toasts).
* For production, run channel workers (ASGI server + Redis as channel layer).
* Keep fallback to polling or server-rendered pages for environments that don't support WebSockets.

**Important**

* Real-time systems increase operational complexity — only add if required.

---

## Step 12 — Scalability & Production Notes

* **Background work**: Always send emails, bulk notifications and heavy fan-out (notify many students) via Celery tasks.
* **Rate-limiting**: Prevent notification spam by batching (e.g., aggregate "3 new lessons" into one digest).
* **Storage**: Keep notification payloads small. Store large metadata elsewhere and reference via IDs.
* **Privacy**: Respect user email preferences; add opt-in/opt-out flags and privacy controls.
* **Monitoring**: Track task failures (Sentry), queue lengths (Redis), and user experience metrics (toast delivery time).
* **APIs**: Expose notification endpoints as part of a versioned API (`/api/v1/notifications/`) for mobile apps and SPA frontends.

---


# Phase 6 (Progress Tracking & Reports)

---


## Step 1 — Create Progress App

```bash
python manage.py startapp progress
```

---

## Step 2 — Add to Installed Apps (config/settings.py)

```python
INSTALLED_APPS = [
    # Django default apps...
    'progress',  # IMPORTANT: Required so Django loads our progress-tracking app
]
```

---

## Step 3 — progress/models.py

```python
from django.db import models
from django.conf import settings

class StudentProgress(models.Model):
    """
    Tracks how much of a specific course a student has completed.
    """
    student = models.ForeignKey(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
        related_name='course_progress'
    )
    course = models.ForeignKey(
        'courses.Course',
        on_delete=models.CASCADE,
        related_name='student_progress'
    )
    completed_lessons = models.PositiveIntegerField(default=0)  # How many lessons done
    total_lessons = models.PositiveIntegerField(default=0)  # Total lessons in course
    progress_percentage = models.FloatField(default=0.0)  # Completion %

    updated_at = models.DateTimeField(auto_now=True)

    class Meta:
        unique_together = ('student', 'course')  # Avoid duplicate progress rows

    def update_progress(self):
        """
        Automatically calculates completion %.
        """
        if self.total_lessons > 0:
            self.progress_percentage = round((self.completed_lessons / self.total_lessons) * 100, 2)
        else:
            self.progress_percentage = 0.0
        self.save()

    def __str__(self):
        return f"{self.student.username} - {self.course.title} ({self.progress_percentage}%)"
```

---

## Step 4 — progress/admin.py

```python
from django.contrib import admin
from .models import StudentProgress

@admin.register(StudentProgress)
class StudentProgressAdmin(admin.ModelAdmin):
    list_display = ('student', 'course', 'progress_percentage', 'updated_at')
    list_filter = ('course', 'updated_at')
```

---

## Step 5 — Hook Progress Tracking into Lessons

In **lessons/views.py** (from Phase 3), after a student **completes or views** a lesson, update their progress:

```python
from progress.models import StudentProgress
from lessons.models import Lesson
from courses.models import Course

def mark_lesson_completed(student, lesson_id):
    """
    Called when a student finishes a lesson.
    """
    lesson = Lesson.objects.get(id=lesson_id)
    course = lesson.module.course

    # Get or create progress record
    progress, created = StudentProgress.objects.get_or_create(
        student=student,
        course=course,
        defaults={'total_lessons': Lesson.objects.filter(module__course=course).count()}
    )

    # Increment completed lessons only if not already counted
    if progress.completed_lessons < progress.total_lessons:
        progress.completed_lessons += 1
        progress.update_progress()
```

---

## Step 6 — progress/views.py (Student Dashboard View)

```python
from django.shortcuts import render
from django.contrib.auth.decorators import login_required
from .models import StudentProgress

@login_required
def student_progress_view(request):
    """
    Shows the logged-in student's progress in all enrolled courses.
    """
    progress_list = StudentProgress.objects.filter(student=request.user)
    return render(request, 'progress/student_progress.html', {'progress_list': progress_list})
```

---

## Step 7 — progress/views.py (Teacher Dashboard View)

```python
from django.contrib.auth.decorators import login_required, user_passes_test
from .models import StudentProgress

def is_teacher(user):
    return user.role == 'teacher'

@login_required
@user_passes_test(is_teacher)
def teacher_course_progress_view(request):
    """
    Shows teacher a list of all students' progress for their courses.
    """
    # Get all progress entries for courses taught by this teacher
    progress_list = StudentProgress.objects.filter(course__teacher=request.user)
    return render(request, 'progress/teacher_progress.html', {'progress_list': progress_list})
```

---

## Step 8 — progress/views.py (Admin Report View)

```python
from django.contrib.admin.views.decorators import staff_member_required

@staff_member_required
def admin_progress_report_view(request):
    """
    Shows an admin-wide overview of all students' progress.
    """
    progress_list = StudentProgress.objects.all().order_by('-updated_at')
    return render(request, 'progress/admin_report.html', {'progress_list': progress_list})
```

---

## Step 9 — progress/urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path('student/', views.student_progress_view, name='student_progress'),
    path('teacher/', views.teacher_course_progress_view, name='teacher_progress'),
    path('admin-report/', views.admin_progress_report_view, name='admin_progress_report'),
]
```

---

## Step 10 — Add to config/urls.py

```python
path('progress/', include('progress.urls')),
```

---

## Step 11 — Templates

**progress/templates/progress/student\_progress.html**

```html
<h1>My Course Progress</h1>
<ul>
{% for p in progress_list %}
    <li>{{ p.course.title }} - {{ p.progress_percentage }}% completed</li>
{% empty %}
    <li>No progress data yet.</li>
{% endfor %}
</ul>
```

**progress/templates/progress/teacher\_progress.html**

```html
<h1>Students' Progress in My Courses</h1>
<ul>
{% for p in progress_list %}
    <li>{{ p.student.username }} - {{ p.course.title }}: {{ p.progress_percentage }}%</li>
{% empty %}
    <li>No progress records yet.</li>
{% endfor %}
</ul>
```

**progress/templates/progress/admin\_report.html**

```html
<h1>Admin Progress Report</h1>
<ul>
{% for p in progress_list %}
    <li>{{ p.student.username }} - {{ p.course.title }}: {{ p.progress_percentage }}%</li>
{% empty %}
    <li>No data available.</li>
{% endfor %}
</ul>
```

---

## Step 12 — How This Works in the Real World

* Every time a **student completes a lesson**, `mark_lesson_completed()` updates their progress.
* Teachers see **progress for their own courses** only.
* Admins see **all courses, all students** — useful for audits.
* This is **scalable** because each course's total lessons are stored and progress is updated incrementally rather than recalculated from scratch every time.

---



