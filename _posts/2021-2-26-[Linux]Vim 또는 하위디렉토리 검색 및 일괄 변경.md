---
tags: linux
toc: True
---
# 특정 폴더 내에서 하위폴더를 포함하여 모든 파일의 내용 중 특정 단어를 바꾸고자 할 때
* find ./ -name "찾을 파일" -exec sed -i "s/찾을 내용/바꿀 내용/g" {} \;

* ex)  find ./ -name "*.xml" -exec sed -i "s/TB_/TB_DEV_/g" {} \;

* ex)  sudo find ./ -name "MT_original.c" -exec sed -i "s/addq %1, %2/imulq %1, %2/g" {} \;

## 주석처리를 위해서는 다른 파라미터를 주어야함
```
ex)  sudo find ./ -name "ZS.c" -exec perl -pi -e "s/CACHE/\/\/CACHE/g" {} \;
sudo find ./ -name "ZS.c" -exec perl -pi -e "s/CACHE/\/\/CACHE/g" {} \;
```
# vim 

* %s/rax/r8/g
* %s/addq/imulq/g



#### reference: https://roxxy.tistory.com/entry/Linux-하위-폴더-내의-파일내용-찾아-바꾸기
