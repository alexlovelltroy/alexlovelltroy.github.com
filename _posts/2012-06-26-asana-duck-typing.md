---
layout: post
category: opinion
title: Adventures in the Asana API
subtitle: Duck Typing my Asana API
twitter: alexlovelltroy
github: alexlovelltroy
---

{{ page.title }}
================

Having never used markdown before, I guess I should just start with the basics.  

### Duck Typing ###
I've been playing with duck typing for an [Asana](http://asana.com) API I've been writing.  The issue is to make a list of `Task` objects out of a plain old list of dictionaries.  That list comes for free when I query the project that their in, but is missing a ton of detail about the tasks.  To find out that detail, I have to query every single one by hand.  Since I'm playing with duck typing, I thought I'd fake a [container object](http://docs.python.org/reference/datamodel.html#emulating-container-types) to see if that made life easier.  In my case, it didn't and I'll be refactoring that out at some stage.  Here's some example code.

    class AsanaResourceList(AsanaResource):
        def __len__(self):
            return len(self.data)
        def __getitem__(self, key):
            try:
                return [x for x in self.object_cache if x.id == key][0]
            except (KeyError, AttributeError, IndexError):
                return [x for x in self.data if x['id'] == key][0]
                #return self.data.__getitem__(key)
        def __delitem__(self, key):
            return self.data.__delitem__(key)
        def __setitem__(self, key, value):
            return self.data.__setitem__(key, value)
        def __contains__(self, item):
            return self.data.__contains__(item)
        def __iter__(self):
            return self.object_cache.__iter__()
