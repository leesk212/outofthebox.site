---
tags: python npz2mat data-preprocessing
toc: True
---
```

import os

import numpy as np
from scipy.io import savemat

global current_dir
current_dir = str()

global temp_dir
temp_dir = str()

def npz2mat(name_of_npz,dir):
    global current_dir
    if current_dir != dir:
        os.chdir(dir)
    current_dir = dir
    with np.load(name_of_npz) as data:
        a = data['x']
        mdic = {"a":a,"label":"experiment"}
        savemat(name_of_npz[:name_of_npz.find(".")+1]+"mat",mdic)

root_dir = "./data/"
for (root,dirs,files) in os.walk(root_dir):
    print("# root: " + root)
    if len(dirs) > 0:
        for dir_name in dirs:
            print("dir: " + dir_name)

    if len(files) > 0:
        print(os.getcwd())
        for file_name in files:
            (name,_format) = file_name.split(".")
            if _format == 'npz':
                dir = root
                #print("file: " + file_name)
                npz2mat(file_name,dir)
    os.chdir("/home/leekatme/code/python3/npz2mat")

```
