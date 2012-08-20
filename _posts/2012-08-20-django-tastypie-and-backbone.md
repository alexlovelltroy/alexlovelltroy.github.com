---
layout: post
category: opinion
title: Using django's tastypie with backbone.js
subtitle: Building webapps with json APIs
twitter: alexlovelltroy
github: alexlovelltroy
---

{{ page.title }}
================

### tastypie resources expose django models to backbone ###
Backbone.js is a javascript framework that maps models, views, and routers much the way django maps models and views with an urls.py.  It was a natural fit for me venturing in to the world of javascript-based webapps.  To keep from repeating myself, I pulled in django-tastypie to handle the API that the javscript talks to.  Through a resources.py file sitting next to my models.py file, I can easily add filtered access to my models.  Then, defining the URL for the resource in my backbone code brings the model across without further describing it.

in python:

    from twitterclone.models import UserProfile
    from tastypie.resources import ModelResource

    class UserResource(ModelResource):
        class Meta:
            queryset = UserProfile.objects.all()

in js:

    window.Shaker = Backbone.Model.extend({
        urlRoot: '/api/v1/profile/',
    });



And, backbone already knows how to Create, Retrieve, Update,and Delete any django resource.  We just have to set permissions and access control. What's more, backbone maintains an internal state representation so it can tell when to update the resource from the server.


Ok. Now I've got some RESTful resources that expose my models directly in javascript without any server-side templating.  And, I can still handle users and permissions with django.  We're cooking.

### Bonus caching! ###
Looking a little further at the tastypie resources is where my first bonus came in. I had been thinking about putting a caching layer between the database and the view code anyway.  Most of the things being pulled from the database don't change from one request to another. To do this in django means adding some kind of memoization through memcache to each model.  Pulling the same thing through tastypie means adding a single caching description to the resource definition.  This caches the objects themselves which can then be queried/filtered with your normal resource code.  On top of that, an nginx/varnish cache can sit on top of the REST urls for caching serialized responses as well.  That's two layers of cache that are now available to me and all for free!

We're still working in the abstract, but there are some clear gains already.
* Our javascript can work with our models directly
* Our models get two free layers of caching
* We can use django's permissions and authentication systems unchanged

Up Next, on-demand rendering of models into HTML
