## Django Tip | How To Make Default Admin Panel Suitable For Users?

Assumption:
1. Basic knowledge of Django
2. Have worked with the admin panel for CRUD before.

Let's assume you have a blog site and you are using default admin for CRUD. Now, you have asked some of your friends to write blogs in your app and they agreed. Now you want to let them be able to manage their blogs only.
Oh boy! It's so boring to create a custom admin panel and you don't want them to be able to manage anything other than their own blogs. 

** *Note: This is about making the default admin panel more suitable for other users but  I'd recommend you to create a custom panel if possible. * **

<hr />

**App status so far. **
1. Blog Model
```py
class Blog(models.Model):

    title = models.CharField(max_length=100)
    author = models.ForeignKey(User, on_delete=models.DO_NOTHING)
    body = models.TextField()
    is_published = models.BooleanField(default=False)

    class Meta:
        verbose_name = "Blog"
        verbose_name_plural = "Blogs"

    def __str__(self):
        return self.title
``` 
2. Blog ModelAdmin
```py
@admin.register(Blog)
class BlogAdmin(admin.ModelAdmin):
    list_display = 'title', 'author', 'is_published'
    list_editable = 'is_published',
    list_filter = 'is_published', 'author'
    search_fields = 'title', 'author', 'body'
```
<hr />
 

**Now, let's see how we can handle that situation.**

### Step 1: Create a group and add a user.

First, you wanna create a user group and give blog permissions.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627262054869/_R4GBDnqd.png)

Then, you will have to grant staff access to the user and add the user to the group.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627305191213/8geiY7xn-.png)

<hr />

### Step 2: Filter the blog QuerySet.

You remember the `.get_queryset()` method? Yup! That also works with ModelAdmin. Let's see how we can do it:

```py
@admin.register(Blog)
class BlogAdmin(admin.ModelAdmin):

    list_display = 'title', 'author', 'is_published'
    list_editable = 'is_published',
    list_filter = 'is_published', 'author'
    search_fields = 'title', 'author', 'body'

    def get_queryset(self, request):
        # Filtering queryset
        qs = super().get_queryset(request)
        if not request.user.is_superuser:
            return qs.filter(author=request.user)
        return qs

    def get_form(self, request, obj=None, **kwargs):
        # Modifying the form
        form = super().get_form(request, obj=None, **kwargs)
        author_field = form.base_fields.get('author')
        # Setting value and disabling edits but still making it visible
        author_field.initial = request.user
        author_field.disabled = True

        return form
```

Now your staff users will only be able to view/edit their own blogs. And you can freely let them use the default admin panel without any worries.

<hr/ ><hr/ >
### Bonus: Make admin panel visually beautiful.

1. Install  [Jazzmin](https://pypi.org/project/django-jazzmin/) :
```shell
pip install django-jazzmin
```
2. Add Jazzmin to installed apps:
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627311520753/zYWCVALRI.png)

3. Enjoy:
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1627311648752/9BnmgtaIF.png)

<hr/>

Feel free your question and feedback below and stay tuned for more awesome blogs. 
**Stay Happy, Stay Blessed**
