---
tags: python
toc: True
---

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
* timeout -s 9 1s "program"
--> 1초마다 해당 program 죽이기
