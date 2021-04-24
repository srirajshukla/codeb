---
layout: post
author: gyan
heading: The Problem with auto_now and auto_now_add in Django.
summary: Django's auto_now changes the field to timezone.now whenever the field is saved, auto_now_add on the other hand only updates the field once when it is created. 
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

This is because you can't make the `DateTimeField` editable in the model, so when you try to `makemigrate` your model, python throws tantrums. 

In my opinion, this has been done to prevent accident changes to data that are supposed to store the DateTime of object creation. However, you can change this time - after migrating your models, you can just change the field to be editable, 

```python
# Uncomment for testing purposes only
# Causes key error while migration because auto_now_add is uneditable by default
created.editable = True
```

Then you can change the fields now. When you're done you can change the model back to its original state. 



## A Better Solution?

When you encounter this problem, you may want to rethink whether you truly want the field to have `auto_now_add` as well as `editable=True`. If the answer is no, then great, fix your model. 

If yes, then you need some workaround. One of the most common ways is to entirely avoid auto_now_add and instead pass the time of object creation explicitly. This way you can control the time easily. Another solution is to create a new data type that inherits from Django `DataTimeField` and modify as per your needs as discussed here [https://stackoverflow.com/a/1737078/4748060](https://stackoverflow.com/a/1737078/4748060). 

