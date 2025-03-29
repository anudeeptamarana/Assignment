# Assignment

1: By default are django signals executed synchronously or asynchronously? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic. 

By default, Django signals are executed synchronously. This means that the signal handler runs in the same thread as the triggering event and blocks execution until it completes.

Code

import time
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
@receiver(post_save, sender=User)
def my_signal_handler(sender, instance, **kwargs):
    print("Signal handler started...")
    time.sleep(5)  # Simulating a long-running task
    print("Signal handler finished.")
user = User.objects.create(username="test_user")
print("User creation complete!")
Expected Output:
Signal handler started...
(Script waits for 5 seconds)
Signal handler finished.
User creation complete!

2: Do Django signals run in the same thread as the caller?

Yes, Django signals run in the same thread as the caller by default.

Code

import threading
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def my_signal_handler(sender, instance, **kwargs):
    print(f"Signal running in thread: {threading.current_thread().name}")

print(f"Main script running in thread: {threading.current_thread().name}")
user = User.objects.create(username="test_user")
Expected Output:
Main script running in thread: MainThread
Signal running in thread: MainThread



3: Do Django signals run in the same database transaction as the caller?

By default, Django signals run outside the transaction unless explicitly wrapped inside transaction.on_commit().

Code
from django.db import transaction
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def my_signal_handler(sender, instance, **kwargs):
    print(f"Signal received: {instance.username}")
    raise Exception("Simulated error inside signal handler")
try:
    with transaction.atomic():
        user = User.objects.create(username="test_user")
        print("User saved in DB")
except Exception as e:
    print("Transaction rolled back due to:", e)
    
Expected Output:
User saved in DB
Signal received: test_user
Transaction rolled back due to: Simulated error inside signal handler




Topic: Custom Classes in Python
Rectangle Class with Iterable Support

class Rectangle:
    def __init__(self, length: int, width: int):
        self.length = length
        self.width = width
    def __iter__(self):
        yield {'length': self.length}
        yield {'width': self.width}
rect = Rectangle(10, 5)
for item in rect:
    print(item)
Expected Output:
{'length': 10}
{'width': 5}



