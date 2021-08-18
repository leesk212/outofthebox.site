---
tags : python
---

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
  print(KST.strftime(fmt))
  ```
