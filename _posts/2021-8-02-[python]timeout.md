# window
```python
from datetime import timedelta, datetime
from time import sleep

endtime = datetime.utcnow() + timedelta(seconds = 2)

while True:
    sleep(1) # just an example
    if datetime.utcnow() > endtime: # if more than two seconds has elapsed
        break
```
# Linux
