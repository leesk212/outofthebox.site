---
tags: python
toc: True
---
```
import os

if __name__ == '__main__':
    print(os.getcwd())
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
                if _format != 'png':
                    erase_name = root +'/'+ file_name
                    os.remove(erase_name)
                    
```
png이외의 파일로 바꾸고 싶으면 17번째 줄의 png를 다른 형식으로 바꾸면 된다.
