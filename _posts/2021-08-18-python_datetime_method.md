---
tags : python
title: '[Python]datetime_method'
date:   2021-08-18 00:19:00 +0900
lastmod : 2017-10-20 12:00:00
sitemap :
changefreq : daily
priority : 1.0
description: >
  python datetime api 
toc: True
---
# Print UTC & KST
```python
from pytz import timezone
from datetime import datetime 
if __name__ == "__main__": 
  fmt = "%Y%m%d%H%M%S %Z%z"
  UTC = datetime.now(timezone('UTC'))
  KST = datetime.now(timezone('Asia/Seoul'))
  print(UTC) 
  print(KST) 
  print(UTC.strftime(fmt))
  KST = KST.strftime(fmt)
  KST = str(KST).split()[0]
  print(KST)
  ```
# KST2UTC
```python
from datetime import datetime
from datetime import timedelta
when = ';; WHEN: 수  8월 04 17:00:30 KST 2021'
when = when.split()
KST_timestring = '2021-08-04 '+when[-3]
print(KST_timestring)
logdate = datetime.strptime(KST_timestring, '%Y-%m-%d %H:%M:%S') - timedelta(hours=9)
print(logdate)
```

# Comparation times
```python
import time
KST_timestring = '2021-08-04 '+when[-3]
print(KST_timestring)
logdate = datetime.strptime(KST_timestring, '%Y-%m-%d %H:%M:%S') - timedelta(hours=9)
print(str(logdate))
formatted_1_time = time.strptime(str(logdate), "%Y-%m-%d %H:%M:%S")
print(formatted_1_time)
formatted_2_time = time.strptime(KST, "%Y%m%d%H%M%S" )
print(formatted_2_time)
print(formatted_1_time<formatted_2_time)
```
