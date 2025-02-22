# Accuknox_-assignment


Question 1: By default are django signals executed synchronously or asynchronously? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

Ans : By default Django signals are executed synchronously. This means that when a signal is triggered, it runs immediately within the same thread before the execution of the next statement in the calling function.

To prove that Django signals are executed synchronously, we can create a simple example where:

A post save signal is connected to a model.
The signal logs a message.
A model instance is saved, and we check whether the next statement executes only after the signal handler completes.
```ruby
from django.db import models
from django.db.models.signals import post_save
from django.dispatch import receiver
import time

class TestModel(models.Model):
    name = models.CharField(max_length=100)

# Signal handler
@receiver(post_save, sender=TestModel)
def test_signal(sender, instance, **kwargs):
    print("Signal started...")
    time.sleep(3)  # Simulating a long-running operation
    print("Signal finished!")
```
run the django shell :
```
python manage.py shell
```
Then execute :
``` ruby
from myapp.models import TestModel
import time

print("Before saving instance")
obj = TestModel(name="Test")
obj.save()
print("After saving instance")
```
output :
```
Before saving instance
Signal started...
(Signal processing for 3 seconds...)
Signal finished!
After saving instance
```
<hr>

Question 2: Do django signals run in the same thread as the caller? Please support your answer with a code snippet that conclusively proves your stance. The code does not need to be elegant and production ready, we just need to understand your logic.

Ans : By default, Django signals run synchronously in the same thread as the caller. This means that when a signal is sent (for example, after saving a model instance), the connected signal handlers execute immediately in the same thread, blocking further execution until they complete.

``` ruby
# models.py
import time
from django.db import models
from django.db.models.signals import post_save
from django.dispatch import receiver

class TestModel(models.Model):
    name = models.CharField(max_length=100)

# Signal handler that simulates a time-consuming task
@receiver(post_save, sender=TestModel)
def test_signal_handler(sender, instance, created, **kwargs):
    print("Signal handler started")
    time.sleep(3)  # Simulate a delay of 3 seconds
    print("Signal handler finished")
```
