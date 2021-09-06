---
tags: linux
toc: True
---
# 1. xrandr
> $ xrandr
#### find display name

# 2. newmode && add  
> $ xrandr --newmode "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync  
> $ xrandr --addmode DP-1 1920x1080_60.00

# 3. output
> $ xrandr --output DP-1 --mode 1920x1080_60.00

# concatenate
> $ xrandr --newmode "1920x1080_60.00"  173.00  1920 2048 2248 2576  1080 1083 1088 1120 -hsync +vsync ; xrandr --addmode DP-1 1920x1080_60.00 ; xrandr --output DP-1 --mode 1920x1080_60.00


# raw
```
xrandr --newmode 1920x1080_60.00 173.00 1920 2048 2248 2576 1080 1083 1088 1120 -hsync +vsync ; xrandr --addmode DP-1 1920x1080_60.00 ; xrandr --output DP-1 --mode 1920x1080_60.00
```
