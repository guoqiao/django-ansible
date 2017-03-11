# django-ansible
A generic ansible ops repo to deploy django projects.

## django project layout
This repo assume your django project is in following layout:

    REPO
    ├── PROJECT
    │   ├── settings
    │   │   ├── __init__.py
    │   │   ├── base.py
    │   │   ├── dev.py
    │   │   └── prod.py
    │   ├── __init__.py
    │   ├── urls.py
    │   └── wsgi.py
    ├── manage.py
    ├── requirements.txt
    ├── static
    ├── fixtures
    └── templates

## multiple projects layout
And it assume you will deploy multiple such monolithic projects on one server while
still keep the ability to extend to multiple server.
The structure for multiple projects:

    HOME
    ├── env
    │   ├── prj1
    │   └── prj2
    ├── git
    │   ├── prj1
    │   └── prj2
    ├── log
    │   ├── prj1
    │   └── prj2
    ├── var
    │   ├── prj1
    │   └── prj2
    ├── media
    │   ├── prj1
    │   └── prj2
    └── static
        ├── prj1
        └── prj2

The above django code will be in git/prj1.

## all in user home
We will use a genral user like `ubuntu` for all project.
So `HOME` will be `/home/ubuntu`, and we put all projects there.
We will also use this user to run all services like nginx, supervisor and uwsgi.
This has a few benifits:

- all files and services belongs to same user, no annoying permission issues.
- you can see what you have on server instantly after you ssh to it, no need to wonder where they are.

## virtualenvs
I put all envs in one place, this make it easy to use virtualenvwrpper to manage
all of them.

## static files
I put all static files in one place, this make it possible to set up a
static nginx server once and be reused by all. This implies:

Each django project just needs to collect static files to its dir(`STATIC_ROOT`),
and don't need to worry about serving them any more.

`STATIC_URL` for prj1 will be something like `http://static.example.com/prj1/`
other than `/static/`. Considering a lot of browsers can only process 6 resource
requests each domain at the same time, this will improve loading speed since static
files are loading from another domain.

media files has similar layout. In theory we can put static and media in the same
static nginx server, so we don't need to set up another static server for media.
But considering some day you need to migrate or back up your site, you only need to
copy files in media and recollect static files.

## log files
A fact normally be ignored is that: there will be a few log files for each
django project. Like this or even more:

    prj1
    ├── django.log
    ├── nginx.log
    ├── supervisor.log
    └── uwsgi.log

It's annoying when you are debuging with logs you need to search a few locations.
Put all logs together will make it super easy to grep a string, and you don't miss any one.
Also, if you have no access to clients' prod server, you can just ask them to send
you the who log dir for project, then you get everthing you need.

## var dir
So far, I will put these files there:

- pid
- sock
- sqlite db file
