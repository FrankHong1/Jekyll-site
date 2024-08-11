---
layout: post
title: "Solving Lotka Volterra using Euler Forward method"
date: 2024-07-30 05:15:25 -0500
categories: jekyll update
---

### Intro
Lotka Volterra, aka Predator Prey, is a ***system of first order ODEs*** used to predict the populations, of the prey and the predator over time, given some initial conditions.  
In this model, the change in the population of one of the two species, instead of a function of itself, is a function taking the arguments of both populations.  

There are some ***assumptions*** to be made in this model:
1. Only the predator and the prey exists in this system
2. Prey born naturally and die at a rate positively affected by both populations
3. Predators born at a rate positively affected by both populations and die naturally  

In this article, we used the simple ***euler forward method*** to solve this first order IVP, in python. We will start by explaining what is euler forward method.  

### Euler forward
Created by Leonhard Euler, the method itself is defined as:
\$$ y_{n+1} = y_n + hF(x_n, y_n) \$$

Where: 
* \$ F(x_n, y_n) = \frac{dy}{dx}_{(x, y)} \$ is the derivative of y about x at the current point
* h is the size of the step each iteration 'jumps' to reach the next point
* \$ y_{n+1} \$ is the approximated value at the next point
* \$ y_n \$ is the value at the current point  

There are several ways to prove/derive the euler forward method, you can check them out at these links:
1. [Proving through tangent lines](https://www.math.purdue.edu/files/academic/courses/2010spring/MA26200/1_10.pdf)
2. [Proving algebraically](https://math.libretexts.org/Bookshelves/Differential_Equations/Numerically_Solving_Ordinary_Differential_Equations_(Brorson)/01%3A_Chapters/1.02%3A_Forward_Euler_method#:~:text=The%20forward%20Euler%20method%20is,%E2%88%92yn)  

But we are not going to derive the method here.  
The goal of the euler forward method, is to continuously approximating the value at the next point by iterating through the forward loop.  

Basically what we want to do is we setup two functions respectively for the ***derivative of the population of the prey over time***, and the ***derivative of the population of predator over time***, each of the two function will take ***two arguments***, the population of the ***prey***, and the population of the ***predator***, and the two variables will be weighted by some constant, eventually returning the derivative of the indicated specie.  

### Setting up the derivative
We will use:
* ***x*** to represent the population of the prey
* ***y*** to represent the population of the predator  

Now we will to set up the derivatives of x and y in the following way:  
\$$ \dfrac{dx}{dt} = ax - bxy \$$
\$$ \dfrac{dy}{dt} = cxy - dy \$$  
Where:
* a: the birth rate of the prey
* b: the eating rate of the predator
* c: the birth rate of the predator
* d: the death rate of the predator  

We can set up the functions in python through the following commands.
```
def f_x(x, y, params):
  a = params[0]
  b = params[1]
  dx_dt = a * x - ( b * x * y )
  return dx_dt

def f_y(x, y, params):
  c = params[2]
  d = params[3]
  dy_dt = c * x * y - ( d * y )
  return dy_dt
```
The only change is, instead of writing every value for the four parameters `a, b, c, d`, we pass a list `params` consisting all the parameters into the function, and call the items in the list when necessary, the `params` should be a list looking like the following:
```
params = [a, b, c, d]
```
With the functions set up, we still need one more thing before iterating through the euler forward loop, ***initial conditions***, because we need a start point where the iteration is based on.  

### Getting the initial conditions  
Therefore, we will start by defining our body function in the following way:  
```
def lotka_volterra(init_cond, params, h, t_fin):
  t = init_cond[0]
  x = init_cond[1]
  y = init_cond[2]
  T = []
  T.append(t)
  X = []
  X.append(x)
  Y = []
  Y.append(y)
```
The body function takes four arguments:
* `init_cond`: a list containing the initial conditions
* `params`: a list containing the parameters
* `h`: the size of the step between each iteration
* `t_fin`: the time when the approximation ends  

The `init_cond` should be a list set up like the following:
```
init_cond[t_0, x_0, y_0]
```  
This block of the code:
1. Read the initial conditions from the passed list `init_cond`
2. Initializing three empty lists `T, X, Y`, appending the initial conditions to the 3 initialized lists respectively.  

### Euler forward loop
After obtaining the initial conditions, we can start our iteration through the following commands:
```
while True:
    
  if t >= t_fin or x <= 0 and y <= 0:
    break
    
  t = t + h
  x = x + h * f_x(x, y, params)
  y = y + h * f_y(x, y, params)
  T.append(t)
  X.append(x)
  Y.append(y)
```
In this block of the code:
1. Defined an `while True: ` loop:
  * Starting from `t_0`, given the initial `x_0, y_0`, keep iterating one step at a time, with the step size of `h`.    
2. At each iteration the function will calculate the ***approximated values*** of `x` and `y` at the next step according to the euler forward method.  
3. Appending the new values of `x, y, z` to their corresponding lists after each iteeration.  
4. Stop the loop when:
  * The value of t reaches or passes `t_fin`.
  * Or when both population extinct.  

### Play around with the code  
Now that the whole code is done, we can try different values and see what will happen.  
To help us getting a better understanding about what is actually going on, we will use `matplotlib.pyplot`.  
```
import matplotlib.pyplot as plt

plt.plot(T, X, 'r')
plt.plot(T, Y, 'b')
plt.xlabel('Time')
plt.ylabel('Amount of Population')
plt.title('Predator Prey Model')
plt.show()
```
Note that, the ***red curve indicates the prey, the blue one indicates the predator***  

We start by the following configurations:
```
h = 0.001
t_fin = 200
init_cond = [0, 200, 10]
params = [3.5, 0.1, 0.1, 0.3]
```

And the following is what we get:  
![Figure 1]({{ site.baseurl }}/assets/images/Lotka-Volterra/Figure_1.png)  

We can see that, under this configuration, the population of the prey and the population of the predator kinda oscillates back and forth.  


Now let's try to give more advantage to the predator  
```
h = 0.001
t_fin = 200
init_cond = [0, 1, 100]
params = [0.1, 0.5, 0.5, 0.01]
```
![Figure 2]({{ site.baseurl }}/assets/images/Lotka-Volterra/Figure_2.png)  

If we did that, the predator would be too over-powered, and the prey would die off immediately, due to that, the predator won't have enough food, and will also die to extinction gradually.  


Now let's try to give more advantage to the prey
```
h = 0.001
t_fin = 200
init_cond = [0, 1000, 1]
params = [1, 0.01, 0.01, 0.5]
```
![Figure 3]({{ site.baseurl }}/assets/images/Lotka-Volterra/Figure_3.png)  

According to the graph, even if we give the prey a tons of advantage, the two population would still oscillate, because the population of the prey has a positive effect on the population of the predator.  

### Future possibilities
This article is only about the easiest solver to Lotka Volterra  
In the future, maybe I will try to use other solvers, for example, the runge-kutta solver
I may also try to make lotka volterra more complicated, for instance, having the parameters `a, b, c, d` be dynamic instead of just constants, which is, to have them be functions of `t, x, y`.  

Have a nice day and stay tuned!












