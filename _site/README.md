###### Jekyll youtube Thomas Bradley
### install
* xcode - command line tools
* homebrew
* brew install ruby - jekyll is built on this
* which gem - where we installed ruby
* gem install jekyll

### setting up on github
* create new repository on github i.e. myProject
* init with README.md
* create branch: gh-pages
* set this to default, delete master.
* git clone repository to directory.
* create index.html with basic html
* push to git with:
> * git add .
> * git commit -m "comment"
> * git push

### setting up a Jekyll project
* create \_config.yml
* in config set:
> * baseurl: /myWebSiteName
> * exclude: ['README.md','otherFolder']

### starting and stopping Jekyll
* turn jekyll on:
> * jekyll serve --watch --baseurl ""
> * open browser at localhost:4000

* to stop:
> control c

### the **\_site** folder
the \_site (its processed by jekyll) folder is the final version of the website, we want github to ignore it completely. need to create .gitignore
* create myProject/.gitignore
* add \_site to to this, then:
> * git add .gitignore

### layouts
common for multiple pages to have repeated html content, layouts simplifies this.
* create new folder/file: \_layouts/default.html

jekyll will look here for any layouts we create. for example:
```
<!DOCTYPE html>
<html>
    <head>
        <title>jekyll test</title>
    </head>
    <body>
        <h1>Messing around with Jekyll</h1>
        {{content}}
    </body>
</html>
```
to use this layout need to add some yml to the head of html page. ie for index.html
```
---
layout: default
---
<h1>Jekyll Yeah</h1>
<p>ipsum dipsum lorem corim</p>
```
now jekyll will use default.html as the template and insert index.html where it states content. This can be viewed on the web at localhost:4000/index.html

### creating navigation
create a second page and some basic navigation.
* create a second page next to index.html

i.e. news.html

```
---
layout: default
---
<h1>News</h1>
<p>interesting stuff</p>
```
now add basic navigation to default template
```
<!DOCTYPE html>
<html>
  <head>
    <title>jekyll test</title>
      <nav>
        <ul>
          <li><a href="index.html">home</a>
          <li><a href="news.html">home</a>
        </ul>
      </nav>
    </head>
    <body>
        <h1>Messing around with Jekyll</h1>
        {{content}}
    </body>
</html>
```
### site baseurl
makes url's work consistently in multiple locations. use baseurl so jekyll puts correct url into our links.

in the previous example change the links to:
```
<li><a href="{{site.baseurl}}/index.html">home</a>
<li><a href="{{site.baseurl}}/news.html">home</a>
```
commit to github.
to this for all links.
### includes
Similar idea to layouts, have a snippet in one location and share it in multiple locations. For this example we will separate out the navigation.
* create \_includes
* create nav.html within \_includes
* cut and paste the nav from the default.html to nav.html
* add a tag default.html
```
<!DOCTYPE html>
<html>
  <head>
    <title>jekyll test</title>
      {% include nav.html %}
    </head>
    <body>
        <h1>Messing around with Jekyll</h1>
        {{content}}
    </body>
</html>
```

nav.html is now inserted at the tag.

### adding CSS
* Create myProject/css/common.css
* Add styling to common.css
* within the head add
```
<  
link href="{{site.baseurl}}css/common.css rel="stylesheet">
```

### highlighting navigation, jekyll if statement.
this code will toggle the class of current. between li's using jekyll's if statement.
* class ="{% if %} return {%endif%}"
```
<li class="{% if page.url == '/' %} current {% endif %}"><a href=#>Home</a></li>
<li class="{% if page.url == '/page2.html' %} current {% endif %}"><a href=#>Page2</a></li>
```

### include parameters
A way to pass information to our include to customize the output.

* in default.html within the include supply a key-value
```
{% include nav.html navclass='nav-top'}
{% include nav.html navclass='nav-bottom'}
```
* within nav.html with a tag you want to change
```
class="{{include.navclass}}"
```
voila, custom classes for includes.

### looping over posts
blog posts in an order determined by date. naming convention as follows:
> yyyy-mm-dd-t-i-t-l-e.md

sample content:
```
---
layout: news
title: Curiosity rover makes big water discovery on mars
meta: Future Mars explorers may be able to get all the...
source: www.google.com
category: news
---
Blog entry asdfasdfasdf
asdfasdfasdfasdfasdfasdf
asdfasdfasdf
```
now create a loop on news that loops over each post.
```
<ul>
  {% for post in site.posts %}
  <li>
    <a href="">{{post.title}}</a>
    <p>{{post.meta}}</p>
  </li>
  {% endfor %}
</ul>
```
note how can access the yml front-matter

### post categories
can sort blog posts into categories. start by ordering into specific directories. So you can clearly see which article belongs to which cat.
```
_posts
  |
  newsDir
  otherDir
```
news.html will now loop though all categories. to only select the news category, loop over the news category as follows:
```
{% for post in site.categories.news %}
```
### limiting the post loop
display last to blog posts:
```
{% for post in site.categories.news limit:2 %}
```
### nested layouts
Have a sub-navigation under news pages nav. We achieve this by creating a layout that extends another layout
* in \_layouts create news.html

```
---
layout: default
---
<strong>News</strong>
<nav>
  <ul>
    <li><a href="{{site.baseurl}}/recently-discovered.html">Other</a></li>
    <li><a href="{{site.baseurl}}/news.html">News</a></li>
  </ul>
</nav>

{{content}}
```
This extends the default layout and inserts its content into default layout. Now when news.html and recently-discovered.html extend the news.html layout
```
---
layout: news
---
```
 their content is inserted in its content box.

### highlighting navigation on multiple pages
or statement in nav.html
> * "{% if page.url == '/news.html' or page.url contains 'recently-discovered' %} current {% endif %}"

can use __==__ or __contains__ keyword.

### nested layouts for a specific content type
when we click on a blog post we want to see the blogs content and title, create \_layouts/news-article.html:
```
---
layout: news
---

<h1>{{page.title}}</h1>

{{content}}

```
all posts should adopt this layout in their yml data. can access other yml data also ie. {{page.source}}

### data files
can access yml csv and json. create \_data/planet-types.yml file with the following format for each entry.
```
- title: Dwarf
  folder: dwarf
  content: A **tiny** planet
```
to access data in html:
```
<ol>
{% for planet in site.data.planet-types %}
      <li>
        <a href="#">{{planet.title}}</a>
        {{planet.content | markdownify}}
      </li>
{% endfor %}
</ol>
```
note the markdownify filter to view as markdown.

### putting pages inside folders
nice urls: localhost:4000/planets/mars/
* create planets/index.html
```
---
layout: default
---

<p>test</p>
```
ammend nav.html class; class="{% if page.url contains 'planets' %} current {% endif %}">

### looping over pages
site.pages selects creates a list of all pages. therefore we use yml data to select specific pages
* in planets/index.html
```
<ul>
    {% for planet in site.pages %}
        {% if planet.type == 'dwarf'%}
        <li>
            {{planet.title}} 
        </li>
        {% endif %}
    {% endfor %}
</ul>
```
### includes with page loops
want to extend previous loop to easilly customise for each planet type.
* create _includes/planet-list.html
```
<ul>
    {% for planet in site.pages %}
        {% if planet.type == include.type %}
        <li>
            {{planet.title}} 
        </li>
        {% endif %}
    {% endfor %}
</ul>
```
now in planets/index.html where ever we want to loop over planets copy:
```
{% include planet-list.html type='dwarf' %}
```
where type is used to select which pages to display.

### page loop and images
to connect images within the above loop. need to specify file path. access yml data.
```
<img {site.baseurl}}/img/planets/{{planet.image}}" alt="Photo of {{planet.title}}">
```

### advanced nested layouts for specific content types
md to html 
* create a link in planet-list.html within the loop to link to planet data
```
<a href="{{site.baseurl}}{{planet.url}}">
```
* create planet.html layout that extends default
