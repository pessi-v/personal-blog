<div class="post">
	<h2>Notes on Django from a Rubyist</h2>
	<h4>December 7, 2024</h4>

	<p>
		In this post I've gathered some notes on setting up a Django project, comparing some of the
		aspects to Ruby on Rails projects.
	</p>

	<h3>Virtual Environments vs Gemfile</h3>

	<p>
		While Rails uses Bundler and a Gemfile to manage dependencies, Django projects instead use
		Python virtual environments and a requirements.txt file. Here's how to set up a virtual
		environment and generate a requirements.txt:
	</p>

	<pre>
		<code class="language-bash">
			# Create a virtual environment
			python3 -m venv venv

			# Activate it (Unix/MacOS)
			source venv/bin/activate
			# or on Windows
			.\venv\Scripts\activate

			# Install Django
			pip3 install django

			# generate a requirements.txt from the current state of the project
			pip3 freeze > requirements.txt

			# use this to install all project dependencies
			pip3 install -r requirements.txt
		</code>
	</pre>

	<h3>Creating a New Project</h3>

	<p>In Django, you first create a project, then create apps within it:</p>

	<pre>
		<code class="language-bash">
			django-admin startproject bookstore
			cd bookstore
			python manage.py startapp books
		</code>
	</pre>

	<p>The resulting structure is different from Rails:</p>

	<pre>
		<code class="language-json">
			bookstore/                 # Project root
			├── bookstore/            # Project configuration
			│   ├── __init__.py
			│   ├── settings.py      # Like config/application.rb
			│   ├── urls.py          # Like config/routes.rb
			│   └── wsgi.py          # Production server config
			├── books/               # Your app
			│   ├── __init__.py
			│   ├── admin.py        # Admin interface config
			│   ├── apps.py         # App configuration
			│   ├── models.py       # Like app/models/
			│   ├── views.py        # Like app/controllers/
			│   └── tests.py        # Like test/
			└── manage.py           # Command-line utility
		</code>
	</pre>

	<p>Remember to add your app to INSTALLED_APPS in settings.py:</p>

	<pre>
		<code class="language-python">
			INSTALLED_APPS = [
					'django.contrib.admin',
					'django.contrib.auth',
					'django.contrib.contenttypes',
					'django.contrib.sessions',
					'django.contrib.messages',
					'django.contrib.staticfiles',
					'books',  # Add this line
			]
		</code>
	</pre>

	<h3>Models and Migrations</h3>

	<p>Django models inherit from models.Model:</p>

	<pre>
		<code class="language-python">
			# books/models.py
			from django.db import models
		
			class Author(models.Model):
					name = models.CharField(max_length=200)
					bio = models.TextField()
		
					def __str__(self):
							return self.name
		
			class Book(models.Model):
					title = models.CharField(max_length=200)
					author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name='books')
					published_date = models.DateField()
					price = models.DecimalField(max_digits=6, decimal_places=2)
		
					def __str__(self):
							return self.title
		</code>
	</pre>

	<p>
		Unlike in Rails, in Django model database structure is defined in a models file, from which
		migrations are generated using the manage.py script. Yes, it's not unusual for Django projects
		to have all the models defined in one file!
	</p>

	<p>To generate and run migrations, you'd run the following commands:</p>

	<pre>
		<code class="language-bash">
			python manage.py makemigrations
			python manage.py migrate
		</code>
	</pre>

	<p>
		Note, that the default database for Django is SQLite, which would be created when you run the
		'migrate' command, if it doesn't exist yet.
	</p>

	<h3>Admin Interface</h3>

	<p>Django includes a complete admin interface out of the box.</p>

	<pre>
		<code class="language-python">
			# books/admin.py

			from django.contrib import admin
			from .models import Author, Book
		
			@admin.register(Author)
			class AuthorAdmin(admin.ModelAdmin):
					list_display = ('name',)
					search_fields = ('name',)
		
			@admin.register(Book)
			class BookAdmin(admin.ModelAdmin):
					list_display = ('title', 'author', 'published_date', 'price')
					list_filter = ('published_date', 'author')
					search_fields = ('title',)
		</code>
	</pre>

	<p>Create an admin user:</p>

	<pre><code class="language-bash">python manage.py createsuperuser</code></pre>

	<h3>Routing in Django</h3>

	<p>
		The project and the apps have their own urls.py files. The project urls.py will contain the
		routes for an admin area and references to the apps' url files.
	</p>

	<pre>
		<code class="language-python">
			# bookstore/urls.py
			from django.contrib import admin
			from django.urls import path, include
		
			urlpatterns = [
					path('admin/', admin.site.urls),
					path('', include('books.urls')),
			]
		
			# books/urls.py
			from django.urls import path
			from . import views
		
			app_name = 'books'
			urlpatterns = [
					path('', views.index, name='index'),
					path('books/', views.book_list, name='book_list'),
					path('books/<int:pk
				>/', views.book_detail, name='book_detail'),
			]
		</int:pk></code
		>
	</pre>

	<h3>Controllers, Views and Templates</h3>

	<p>
		In Django the logic you'd normally have in a Rails Controller, goes into a 'View'. Django views
		can be functions or classes:
	</p>
	<pre>
		<code class="language-python">
			# books/views.py
			from django.shortcuts import render
			from .models import Book
		
			def index(request):
					latest_books = Book.objects.order_by('-published_date')[:5]
					return render(request, 'books/index.html', &lcub;'latest_books': latest_books&rcub;)
		</code>
	</pre>

	<p>
		Templates (no special file type here, just .html files) in Django use a similar syntax to ERB
		but with different tags:
	</p>

	<pre>
		<code class="language-html">
			&lt;!-- books/templates/books/index.html --&rt;
			&lt;!DOCTYPE html&rt;
			&lt;html&rt;
			&lt;head&rt;
					&lt;title&rt;Bookstore&lt;/title&rt;
			&lt;/head&rt;
			&lt;body&rt;
					&lt;h1&rt;Latest Books&lt;/h1&rt;
					&lcub;% for book in latest_books %&rcub;
							&lt;div class="book"&rt;
									&lt;h2&rt;&lcub;&lcub; book.title &rcub;&rcub;&lt;/h2&rt;
									&lt;p&rt;By &lcub;&lcub; book.author.name &rcub;&rcub;&lt;/p&rt;
									&lt;p&rt;Price: $&lcub;&lcub; book.price &rcub;&rcub;&lt;/p&rt;
							&lt;/div&rt;
					&lcub;% endfor %&rcub;
			&lt;/body&rt;
			&lt;/html&rt;
		</code>
	</pre>

	<p>
		Key template differences: &lcub;% %&rcub; for logic (instead of &lt;% %&rt;) &lcub;&lcub;
		&rcub;&rcub; for output (instead of &lt;%= %&rt;) No more = form_for helpers (Django has its own
		form system)
	</p>

	<h3>Running the Server</h3>

	<pre><code class="language-bash">python manage.py runserver</code></pre>

	<p>By default, Django runs on port 8000 (Rails uses 3000).</p>

	<h3>Key Philosophical Differences</h3>

	<p>
		Explicit vs Implicit: Django favors explicit configuration over Rails' convention over
		configuration. Apps vs Engines: Django apps are lighter than Rails engines and are meant to be
		used more frequently. Admin Interface: Django provides a powerful admin interface out of the
		box. Form Handling: Django's form system is more explicit and separate from models. Template
		Language: Django's template language is intentionally limited to enforce separation of concerns.
	</p>

	<p>All right, I hope that was useful!</p>
</div>

<style src="../../../app.css"></style>
