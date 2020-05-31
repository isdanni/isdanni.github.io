---
layout: single
title: "Hochtec.ch Dev Docs"
author: "Danni"
header: 
  overlay_images: "assets/images/header/slide4.jpg"
toc: true
toc_sticky: true
lang: en
tags:
  - Web
  - System Design
---

> Django2, Python3, PostgreSQL, Gunicorn, Whitenoise, Pillow, Virtualenv

> Making a website from scratch is always fun since there're so many unknown problems to tackle and so many mistakes to learn from. Before this, everytime I fixed a problem during developmnet process, I may store it somewhere in my notebook or thought it's worth memorizing but could not tell a thing after a month. So after I was luckily given the opportunity to develop the website for Hochtec Gmbh, I figured it would be much valuable to jot down my thoughts during design & developing & deploying & debugging process. 

> This post took a few weeks to finish since I was quite busy with school and work but it was such fun to learn from everything. 

### A. Design

To establish the online presence and highlight their product feature is the initial reason why hochtec wanted to make a modern website. As you can see, below is their original website made around 2010s -- just plain HTML and CSS.

| old homepage  | old contact page  | old product page 1 |
| ------------- |:-------------:| -----:|
| ![Old1](/assets/images/post/hochtec/old1.png) | ![Old2](/assets/images/post/hochtec/old2.png) | ![Old3](/assets/images/post/hochtec/old3.png) |

So the first step of website design is to know the need. As a decoration company, Hochtec Gmbh wants to showcase their different types of product onine with description(price, material...), display contact information(Google Maps, phone, email...) with enquiry form, do product showcase under different categories, slider bar at the homepage, etc.

##### Hochtec Requirements

Following are some demo pictures sent by HochtecGmbh to specify their design needs. Basically apart from functions I lsited above, this website should also provide all E-Commerce features like product browsing under different categories, checking detailed product, also updating the product whenever they want(OK hello Django Admin Panel).


|  Product Description Page | Product List Page | Design Demo|
| ------------- |:-------------:| :-----:|
| ![Old1](/assets/images/post/hochtec/request1.jpg) | ![Old2](/assets/images/post/hochtec/request2.jpg) | ![Old3](/assets/images/post/hochtec/request3.jpg) |


So based on both sides of requirements, I devided websites into following parts:

1. Homepage: slider bar(3), 3 card blocks(floor, showertile, handrail), 6 project showcases, one visual element block, footer. 
2. Category page: 3 card blocks.
3. Product page: a. floor. b. showertile. c. handrail.
4. Product detail page: picture, title, description, price...
5. Contact page: contact information, google map, user form.
6. Admin center: determined by models.py, provide dashboard for staff to manage dashboard data.


##### Database Design

> A well-design database means half success of making a website.

For this website, the database generally have two main functions: managing product information(only text info, image will be directly stored in `/media/`) and user data(name, email, enquiry, address). I will not describe more about details. But the whole design process strictly follows the general db design process:

1. requirement gathering.
2. conceptual design.
3. logical design.
4. physical design.

During the first step of deployment, we cannot use SQLite on Heroku because it if file-based and it would be deleted from the ehyemeral file system every time the application restarts. 

--------------------------------------------------------------

### B. Develop

After I set up the virtual environment, installed all the packages and built Django project skeleton, I simply started by adding all the HTML templates as well as all the static files like JavaScript/CSS/bootstrap. I wanted to render all the web pages while running project locally. Because it's quite satisfying that I could view the website in local browser in such short time so I built front end first before moving on to all the `models.py`. 

Interesting enough, when I started my database design, I came across this [post](https://andreicioara.com/always-start-with-the-front-end-768dff8e961e) from Medium where a developer states how web developers should always write the front end before the back end when creating the website. I couldn't agree more. It's true that whenever I updated developing process, I just ran project locally and shot a demo video in Chrome since it's much easier to show the progress, receive feedbacks, brings everyone on the same page and **making development more enjoyable**. I, myself, is a visual learner so it's a treat to look at beautiful templates instead of strangling with half-ok database while implementing a bit of **UI** in a rush.

After the front end tested ok, I started adding data definition and embed such attributes in HTML templates. Here's where I really started to learn the beauty of Django. Its workflow is just simple, clean and effect.  

|  New homepage | New contact page | 
| :-------------: |:-------------:| 
| ![new1](/assets/images/post/hochtec/new1.png) | ![new2](/assets/images/post/hochtec/new2.png) | 
| **New product page** | **New product detail**|
| ![new3](/assets/images/post/hochtec/new3.png)|  ![new4](/assets/images/post/hochtec/new4.png) | 


##### Database Configuration

First install all db dependencies, this is crucial for deployment and needs to be defined in `requirements.txt`.

Then change the database from SQLite to PostgreSQL. In `settings.py` modify following: 

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

to 

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'NAME_OF_DB',
        'USER': 'DB_USER_NAME',
        'PASSWORD': 'DB_PASSWORD',
        'HOST': 'localhost',
        'PORT': 'PORT_NUMBER',
    }
}
```

Then create the database and user:
```shell
(venv) danni@danni-pc:~/Dropbox/CS/web/hochtec_pro$ sudo -u postgres psql
could not change directory to "/home/danni/Dropbox/CS/web/hochtec_pro": Permission denied
psql (10.6 (Ubuntu 10.6-0ubuntu0.18.04.1))
Type "help" for help.

postgres=# CREATE DATABASE hochtec;
CREATE DATABASE
postgres=# \l
postgres=# CREATE USER hochtec WITH PASSWORD '***';
CREATE ROLE
postgres=# ALTER ROLE hochtec SET client_encoding TO 'utf8';
ALTER ROLE
postgres=# ALTER ROLE hochtec SET default_transaction_isolation TO 'read committed';
ALTER ROLE
postgres=# GRANT ALL PRIVILEGES ON DATABASE hochtec TO hochtec;
GRANT
postgres=# GRANT CONNECT ON DATABASE hochtec TO hochtec;
GRANT
postgres=# \du+
                                          List of roles
 Role name |                         Attributes                         | Member of | Description
-----------+------------------------------------------------------------+-----------+-------------
 admin     |                                                            | {}        |
 hochtec   |                                                            | {}        |
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}        |
postgres=# \l
postgres=# \q
```

Then test db and migrate:
```shell
python manage.py makemigrations
python manage.py migrate
```

And finally create admin roles for admin panel `python manage.py createsuperuser`. 

Following shows the UI for Admin Panel(e.g. floor): 

|  Admin Panel | Product Description | Product Managemeny | 
| :-------------: |:-------------:| :-------------:| 
| ![admin1](/assets/images/post/hochtec/admin1.png) | ![admin2](/assets/images/post/hochtec/admin2.png)| ![admin3](/assets/images/post/hochtec/admin2.png)| 

--------------------------------------------------------------

### C. Deploy

First I tried direct deployment in their remote server space, however, the [vendor](https://cyon.ch) does offer root access so I could not install any packages && building from source seems way too much trouble. A workaround for this is to use Heroku app temporarily and add custom domain later. Then the following process is plain simpleness:

> Also, I checked this [article](https://devcenter.heroku.com/articles/getting-started-with-python) for reference.

1. First push the project code to a Github repo. Can change the `.gitignore` to Python while initializing.
2. Assume having set up the Heroku account(including verification, SSH keys...) and installed heroku locally(To check this, try `heroku help` in your local terminal), create the Heroku app by:

```shell
heroku create

git remote -v   # check remote status, if ok, there should be both Github && Heroku's remote.

git push heroku master

heroku run python manage.py migrate  # perform migrate operation for database, if no db then do not use

heroku ps:scale web=1  # check at least one dyno is running

# my output: Scaling dynos... done, now running web at 1:Free

heroku open
```

---------------------------------------------------------
### D. Resolve bugs

#### 1. error: RPC failed; curl 18 transfer closed with outstanding read data remaining

At the push process `git push heroku app`, I constantly get this error:

```shell
remote: -----> $ python manage.py collectstatic --noinput
error: RPC failed; curl 18 transfer closed with outstanding read data remaining
fatal: The remote end hung up unexpectedly
fatal: The remote end hung up unexpectedly
Everything up-to-date
```

From stackoverflow, it seems that the push process is closed due to a common curl error -- a slow internet connection which closes too soon though this happens mostly while cloning a huge codebase to local.   

After tangeling with this for one night, I found that this was caused by one simple overlook: I added one extra `/ `after the `static` in the `settings.py`:

```python
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "static/"), # remove the `/` 
]
```

---------------------------------------------------------
#### 2. Application error: An error occurred in the application and your page could not be served.


So I checked logs using `heroku logs --tail` and got following error:

```shell
2019-02-08T03:56:22.121837+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/" host=fast-taiga-16001.herokuapp.com request_id=45e1b4af-baa1-4ae9-b74b-9d1341bcf72b fwd="144.214.90.65" dyno= connect= service= status=503 bytes= protocol=https
2019-02-08T03:56:23.105462+00:00 heroku[router]: at=error code=H10 desc="App crashed" method=GET path="/favicon.ico" host=fast-taiga-16001.herokuapp.com request_id=f4650a57-fb59-4f77-bd60-d82e88477663 fwd="144.214.90.65" dyno= connect= service= status=503 bytes= protocol=https
```

One common reason here is the port setting, which in production is defined by the `Procfile` file in the root folder, a text file that explicitly declare what command should be executed to start the app.(Some reference here: [1](https://stackoverflow.com/questions/14322989/first-heroku-deploy-failed-error-code-h10), [2](https://stackoverflow.com/questions/15693192/heroku-node-js-error-web-process-failed-to-bind-to-port-within-60-seconds-of), [3 Heroku Official](https://devcenter.heroku.com/articles/getting-started-with-python#define-a-procfile))

So first method I tried was setting `Procfile` from `web: gunicorn locallibrary.wsgi --log-file -` to `web: gunicorn gettingstarted.wsgi --log-file -`, declared in the official Django deployment Heroku doc. But this did not work. Then I found some posts stating that this error happened after they ran db migration. For me, I used default SQLite and also installed PostgreSQL, so I wondered if that's the case. But some tests indicated it's not the case.

And...I found it: Use your own app name to define the Procfile! Since I checked some Github projects so I was naive enough to directly copy the content. However, to define a Procfile you should always use your own app name:

 `web: gunicorn hochtecgmbh.wsgi`

--------------------------------------------------------
#### 3. Static file 404 Not Found

This was caused by inappropriate Whitenoise configration. In prodcution environment it's common to serve static files using WhiteNoise, a static file serving helper for Python web apps without relying on nginx or any other external services.

To install it, jun run `pip install whitenoise`.

Then in `settings.py`, added declaration in MIDDLEWARE:

```python
MIDDLEWARE = [
  # 'django.middleware.security.SecurityMiddleware',
  'whitenoise.middleware.WhiteNoiseMiddleware',
  # ...
]
```
-------------------------------------------------------

#### 4. Server Error (500)

Now that all files are serverd ok, I thought it's safe to check whichever [link](https://hochtec.herokuapp.com/) I want, howver, the `https://hochtec.herokuapp.com/catelogue/floor` keeps getting `Server Error (500)`, so do the other two same-level pages `catelogue/floor` and `catelogue/showertile`. The error logs was like following:

```shell
2019-02-08T13:56:10.598144+00:00 app[web.1]: Internal Server Error: /catelogue/floor
2019-02-08T13:56:10.598152+00:00 app[web.1]: Traceback (most recent call last):
2019-02-08T13:56:10.598154+00:00 app[web.1]: File "/app/.heroku/python/lib/python3.6/site-packages/django/db/backends/utils.py", line 85, in _execute
2019-02-08T13:56:10.598156+00:00 app[web.1]: return self.cursor.execute(sql, params)
2019-02-08T13:56:10.598158+00:00 app[web.1]: File "/app/.heroku/python/lib/python3.6/site-packages/django/db/backends/sqlite3/base.py", line 296, in execute
2019-02-08T13:56:10.598160+00:00 app[web.1]: return Database.Cursor.execute(self, query, params)
2019-02-08T13:56:10.598162+00:00 app[web.1]: sqlite3.OperationalError: no such table: product_floor
2019-02-08T13:56:10.598163+00:00 app[web.1]:
2019-02-08T13:56:10.598165+00:00 app[web.1]: The above exception was the direct cause of the following exception:
***
```
The important message here is `sqlite3.OperationalError: no such table: product_floor`, which means the database connection is not configured properly. 

###### django.db.utils.ProgrammingError: permission denied for relation django_migrations

This error normally relates to user previleges, just `GRANT ALL PRIVILEGES ON TABLE *** TO user;`. For me, I got the error because I used a different role to create the database for this project, so fixing method for me would be change db ownership: `ALTER DATABASE target_database OWNER TO new_onwer;`. Log into db as `sudo -u postgres psql hochtec`

1. The user needs access to the database, obviously:
```postgresql
hochtec=# GRANT CONNECT ON DATABASE hochtec TO hochtec;
GRANT
```

2. And (at least) the USAGE privilege on the schema:
```postgresql
hochtec=# GRANT USAGE ON SCHEMA public TO hochtec;
GRANT
```

3. Then, all permissions for all tables (requires Postgres 9.0 or later):
```postgresql
hochtec=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO hochtec;
GRANT
```

4. And don't forget sequences (if any):
```postgresql
GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO hochtec;
```

Then, in case still got `no such table error`, try make migrations as well as migrate(if got error during migrate stage, use `python manage.py migrate --fake`)

