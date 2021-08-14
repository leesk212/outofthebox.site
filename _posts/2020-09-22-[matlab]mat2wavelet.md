---
tags: matlab wavelet data-preprocessing
toc: True
---
```
f = dir('1D_N1');
name = f(3).name;
load(name)
[cA,cD] = dwt(a,'sym4')
[c,l] = wavedec(a,5,'sym4')
approx = appcoef(c,l,'sym4')
[cd1,cd2,cd3,cd4,cd5] = detcoef(c,l,[1 2 3 4 5])
subplot(6,1,1)
plot(cd1)
subplot(6,1,2)
plot(cd2)
subplot(6,1,3)
plot(cd3)
subplot(6,1,4)
plot(cd4)
subplot(6,1,5)
plot(cd5)
```
