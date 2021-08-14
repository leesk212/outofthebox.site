---
tags: python data-preprocessing numpy npz2mat
toc: True
---
```
import numpy as np
from scipy.io import savemat

def npz2mat(name_of_npz):
    with np.load(name_of_npz) as data:
        a = data['x']
        mdic = {"a":a,"label":"experiment"}
        savemat(name_of_npz[:name_of_npz.find(".")+1],mdic)


```
