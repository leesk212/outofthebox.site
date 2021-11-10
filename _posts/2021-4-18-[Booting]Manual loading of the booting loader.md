---
tags: linux
toc: True
---
# grub > 이 생성되며, Rom에 있는 booting loader를 자동으로 잡지 못할 경우

> grub> ls    
> "list hardisk partition"   
> "Find Linux partion" (tip: hardisk size)  
> grub> set root=(hd0,msdos5)  
> grub> set prefix=(hd0,msdos5)/boot/grub  
> grub> insmod normal  
> grub> normal   
