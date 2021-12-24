## Mastering Django ORM. Use Q objects to filter Django Queryset.

We all know that we can use Django ORM as a high-level interface to interact with databases. And we can use normal methods like `get()`, `filter()` to narrow down our search.
But, there comes a stage in our project that it becomes tedious to handle looong lines and chains of filtering. Such as:

```python
# Get the countries with name that starts with N or ends with L
countries = Country.objects.filter(name__istartswith="N") |
     Country.objects.filter(name__iendswith="l")
# Django querysets support & as and and | as or.
``` 

Here is where *Q* object makes an entry. 
While using `Q` objects, we can use `&`, `|` and `~` as **and**, **or**, and **not**. 

Let's see an example:

```python
from django.db.models import Q

countries = Country.objects.filter(
    Q(name__istartswith='N') | Q(name__iendswith='l')
)
```

This was a rather simple example that shows how we can use `Q` objects to simplify how we write queries.

Now, let's see how we can simplify a somewhat complex query using Q objects.
Here, we will use the same example of countries. But let's make our logic a bit complex. Like: <br>
**Get countries that satisfy both of the given criteria: ** 
* Continent's name: starts from 'A' or has 'rica' BUT exclude 'Africa'. 
* Has a per-capita income of more than 10000 OR is not landlocked.

Let's do that using normal model methods.
```python
countries = ((
        Country.objects.filter(continent__name__istartswith="A") |
        Country.objects.filter(continent__name__icontains="rica")
    ).exclude(continent__name__iexact="Africa")) & (
        Country.objects.filter(pci__gt=10000) |
        Country.objects.filter(is_landlocked=False)
    )
```
We have already been really overwhelmed by the complexity of this query. Let's use `Q` and see its magic.
```python
from django.db.models import Q
countries = Country.objects.filter(
        (
            Q(continent__name__istartswith="A") |
            Q(continent__name__icontains="rica") &
            ~Q(continent__name__iexact="Asia")
        ) & (
            Q(pci__gt=10000) | Q(is_landlocked=False)
        )
    )
```

As we can see, using `Q` objects has the potential of refactoring a lot of complex code and making it more readable.

Thank you for your time. To know more about `Q` object, visit  [Django Docs](https://docs.djangoproject.com/en/4.0/topics/db/queries/#complex-lookups-with-q-objects) 

** Happy Reading, Keep Pythoning ** 
