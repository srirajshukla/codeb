---
layout: post
author: gyan
heading: The Problem with auto_now and auto_now_add in Django.
---
`auto_now` and `auto_add_now` are attributes to Django `DateTimeField` methods. 

`auto_now` changes the field to `timezone.now` whenever the field is saved, `auto_now_add` on the other hand only updates the field once when it is created. 



Let's say you have created a model like this:

```python
class TimeTest(models.Model):
    created = models.DateTimeField(auto_now_add=True)
    updated = models.DateTimeField(auto_now=True)
```

Now, for some reasons, you want to update the `created` field in your model - You Can't. 

So you think, what can I do, you do some googling, and find that there's a property called editable.

So you set `created.editable = True`, and voila it works. In your Django Admin panel the created field is now editable. 

What's the problem then?



## Addressing the Issue

When you try to update the model, and rerun the `makemigration` or `migrate` commands, you get an error saying 

```python
    del kwargs['editable']
KeyError: 'editable'
```

