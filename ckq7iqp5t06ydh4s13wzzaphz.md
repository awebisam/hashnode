## Encrypting objects in Django with secret key.

Hi everyone.
Let's talk about encrypting data in Django. 

Django has a built-in module called signing that allows you to sign data and then also unsign it.
It uses SECRET_KEY of your project to hash so only the backend that encrypted can decrypt the data.
There might be many use cases of this. 
I encountered the given situation recently.


```py
"""An utility module to sign and unsign email."""

from django.core import signing
from typing import Union


def sign(email: str) -> str:
    """Returns signed email using Django Secret Key."""
    
    signer = signing.Signer(salt="Salty Thingy")
    enc = signer.sign_object(email) #also supports other types
    return enc


def unsign(email: str) -> Union[str, None]:
    """ Returns decrypted email and error if any. """
    
    signer = signing.Signer(salt="Salty Thingy") #salt needs to be same
    try:
        enc = signer.unsign_object(email)
        return enc, None
    except signing.BadSignature as e:
    	print("enc has been tinkered.")
        return None, e
``` 
