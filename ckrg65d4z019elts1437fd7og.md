## Django Tip | Run a block of code when the server starts.

You have an awesome django application with a lot of features and now you need a piece of code to execute once when the server starts. Hmm.. Let's see how Django let's you handle that use case. 

One common use case might be doing some kind of db sync operation.

Note: *Although any code you put in the files(urls, views, etc) that Django loads at first also runs whenever the server starts but you might wanna avoid that because: *
1. It might have side effects on `import` statements.
2. It might not guarantee that your server has been initialized yet.

**So, what is the proper way? **
Glad that you asked. You can put the code in .ready() hook in AppConfig class. 
Let's see an example here:


```py
'''name/apps.py'''
from django.apps import AppConfig

# user imports
from common.utils import some_function_to_run_once

class NameConfig(AppConfig):
    name = 'name'
    
    def ready(self) -> None:
     	'''Hook to run code when the server starts'''
        some_function_to_run_once()
``` 

So, whatever code you add into .ready() hook, it runs when your server starts. 

<hr />

** *Comment below for blog requests, or any queries about django, python and tech in general.* **