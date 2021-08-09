---
tags: data-preprocessing matlab mat2png stft surf 
toc: True
---
# 1. mat to stft to fig to png
```
f = dir('mata_data')  
name = f(3).name  
load(name)  
[S,F,T] = stft(a)  
h = pcolor(T,F,abs(S))  
set(h,'EdgeColor','none);  
axis tight off  
savefig('00001.fig')  
figs = openfig('00001.fig');  
style = hgexport( 'factorystyle' );  
style.Bounds = 'Tight';  
hgexport( figs, '-Clipboard', style, 'ApplyStyle', true );  
F = getframe(figs);  
imwirte(F.cdata,'00001.png')  
```




# 2. mat to stft to surf to fig to png
```
f = dir('mata_data')  
name = f(3).name  
load(name)  
[S,F,T] = stft(a);  
**surf(T,F,abs(S),'EdgeColor','none');**  
**axis tight off**  
**view(0,90)**  
savefig('00001.fig')  
figs = openfig('00001.fig');  
style = hgexport( 'factorystyle' );  
style.Bounds = 'Tight';  
hgexport( figs, '-Clipboard', style, 'ApplyStyle', true );  
F = getframe(figs)  
```
