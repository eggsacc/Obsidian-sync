
Created <font style="color:tomato; font-family:Consolas;">14-10-2024</font>

Tags: 

Related: NIL

****

## Taylor's series 

The Taylor series of a function is the is an infinite sum of terms that are expressed in terms of the function's derivatives at a single point $a$.

The general equation is:

$$
f(x)=f(a)+\frac{f'(a)}{1!}(x-a)+\frac{f''(a)}{2!}(x-a)^2+\cdots=\sum_{n=0}^\infty\frac{f^{(n)}(a)}{n!}(x-a)^n
$$


## Taylor series of sine

Let $f(x)=\sin(x)$

Let $a=0$ since the graph of $y=\sin x$ is centered around (0,0).

Using Taylor's expansion, we get:

$$
\sin(x)=\sin(0)+\frac{\cos(0)}{1!}(x)+\frac{-\sin(0)}{2!}(x)^{2}+\frac{-\cos(0)}{3!}(x)^{3}+\frac{\sin(0)}{4!}(x)^4+\cdot \cdot \cdot
$$

Observe that all the terms with even powers of $x$ are 0 since $\sin{0}=0$, while all the $\cos{0}$
in the odd terms are just 1.

Hence, the Taylor series of $\sin x$ could be simplified to:

$$
\sin(x)=\frac{x}{1!}-\frac{x^{3}}{3!}+\frac{x^5}{5!}-\frac{x^7}{7!}+\cdot \cdot \cdot
$$

The further we expand the Taylor series, the larger the range the expansion is accurate to.


```functionplot
---
title: Expansion up to x power 3
xLabel: 
yLabel: 
bounds: [-5,5,-2,2]
disableZoom: 1
grid: true
---
f(x)=sin(x)
h(x)=x-x^3/6x
```


```functionplot
---
title: Expansion up to x power 5
xLabel: 
yLabel: 
bounds: [-5,5,-2,2]
disableZoom: 1
grid: true
---
f(x)=sin(x)
g(x)=x-x^3/6+x^5/120
```


```functionplot
---
title: Expansion up to x power 7
xLabel: 
yLabel: 
bounds: [-5,5,-2,2]
disableZoom: 1
grid: true
---
f(x)=sin(x)
k(x)=x-x^3/6+x^5/120-x^7/5040
```

Usually, only powers up to $x^7$ are used to approximate sine values due to the sine function being highly symmetrical. We only need accurate values between 0-90 degrees to calculate any $\sin x$.













