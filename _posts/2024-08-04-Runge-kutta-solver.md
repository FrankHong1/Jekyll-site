---
layout: post
title: "Solving Lotka Volterra using Fourth order runge-kutta"
date: 2024-08-04 05:15:25 -0500
categories: jekyll update
---

### Intro
Last article, we used the forward euler method to solve an initial value problem from Lotka Volterra system, today we will be using a different method, ***4th order runge-kutta***, abbreviated ***RK4***, to do the same thing.  

If you forgot what lotka volterra was, you can check my previous article in the introduction part, which is the first paragraph.  
[Solving Lotka Volterra using Euler Forward method](https://frankhong1.github.io/Jekyll-site/jekyll/update/2024/07/30/Lotka-Volterra.html)  

### RK4
Founded by Carl Runge and Martin Kutta, RK4 has the following formula:  
\$$ y_{n+1} = y_n + \dfrac{1}{6}(K1 + 2K_2 + 2K_3 + K_4) \$$  
\$ K_1 = hf(x_n, y_n) \$  
\$ K_2 = hf(x_n + \frac{1}{2}h, y_n + \frac{1}{2}K_1) \$  
\$ K_3 = hf(x_n + \frac{1}{2}h, y_n + \frac{1}{2}K_2) \$  
\$ K_4 = hf(x_n + h, y_n + K_3) \$  

Where:  
* \$ h = x_{n+1} - x_n \$
* \$ f(x, y) = \frac{dy}{dx} \$
* \$ x_n, y_n \$ are discrete points  

You can find the derivation of RK4 through the following link, but we will not further elaborate here.
* [ScienceDirect](https://www.sciencedirect.com/topics/mathematics/runge-kutta-method)
* [MIT public notes](https://web.mit.edu/10.001/Web/Course_Notes/Differential_Equations_Notes/node5.html)  

### RK4 in python  
Intuitively thinking, we might just go and define a bunch of functions.  
But considering the relatively complex structures and the fact that the method requires 4 values each iteration, it is much cleaner and efficient to create a class.

```
class RK_4:
  
  def __init__(self, deriv, h):
      self.deriv = deriv
      self.step = h
      
  def K_1(self, x, y, *args):
      K1 = self.deriv(x, y, *args)
      K1 = self.step * K1
      return K1
      
  def K_2(self, x, y, *args):
      K1 = self.K_1(x, y, *args)
      x = x + (0.5 * self.step)
      y = y + (0.5 * K1)
      K2 = self.deriv(x, y, *args)
      K2 = self.step * K2
      return K2
  
  def K_3(self, x, y, *args):
      K2 = self.K_2(x, y, *args)
      x = x + (0.5 * self.step)
      y = y + (0.5 * K2)
      K3 = self.deriv(x, y, *args)
      K3 = self.step * K3
      return K3
  
  def K_4(self, x, y, *args):
      K3 = self.K_3(x, y, *args)
      x = x + self.step
      y = y + K3
      K4 = self.deriv(x, y, *args)
      K4 = self.step * K4
      return K4
    
  def operation(self, x, y, *args):
    val = (1/6) * (self.K_1(x, y, *args) + 2 * self.K_2(x, y, *args) + 2 * self.K_3(x, y, *args) + self.K_4(x, y, *args))
    return val
```  

Initialization method assigns an instance of the class its own properties, and we can call its own properties when needed.  
We put 2 parameters(other than self) in the initialization method, which is deriv, and h:
* deriv - basically just f(x, y)
* h - the size step during each iteration  

As mentioned previously, RK4 takes 4 values during each iteration, therefore, we assign 4 methods respectively for K1, K2, K3 and K4 according to the formula.  

One thing to notice here is, we also put ___*args___ here, which is quite essential:  
The methods K1, K2, K3, K4 were supposed to take only two arguments, ***x and y***, and process them, which intuitively sounds correct.  
However, ***self.deriv*** might take ***more than two arguments***, instead of just x and y, self.deriv might include other arguments such as parameters used inside the derivative.  

Therefore, we used ___*args___ to allow additional positional arguments, which means, the methods will now take ___at least two arguments___.
It is also conventioanl to include ___**kwargs___ to include keyword arguments, in my case, I know I won't be passing any keyword parameters, therefore I did not include it.  

In the last method, the operation, we called the previous 4 methods together and weighted them according to the formula, returning the final value.

### Defining the derivatives

```
def f_x(x, y, params):
  a = params['prey_birthrate']
  b = params['predator_eatrate']
  dx_dt = a * x - (b * x * y)
  return dx_dt


def f_y(x, y, params):
  c = params['predator_birthrate']
  d = params['predator_deathrate']
  dy_dt = c * x * y - (d * y)
  return dy_dt
```  

We define the derivatives of population against time for prey and predator respectively.  
We will assign the variables as: 
* x - the population of the prey
* y - the population of the predator  

Since the derivative is different between the prey and the predator, later we will initialize two instances of the class for the population of the prey and the predator. 

### Creating the forward loop

```
def lotka_volterra(params):
  T = []
  X = []
  Y = []
  t = params['interval'][0]
  t_f = params['interval'][1]
  x = params['initial_prey']
  y = params['initial_predator']
  T.append(t)
  X.append(x)
  Y.append(y)
  
  h = params['step']
  dx = RK_4(f_x, h).operation(x, y, params)
  dy = RK_4(f_y, h).operation(x, y, params)
  
  while True:
    t = t + h
    x = x + dx
    y = y + dy
    
    if t >= t_f:
      break
    elif x <= 0:
      break
    elif y <= 0:
      break
    
    T.append(t)
    X.append(x)
    Y.append(y)
```  

The forward loop function takes one parameter: 'param', it is a dictionary object that includes all the parameters needed, which are:
* Prey birthrate
* Prey deathrate
* Predator birthrate
* Predator deathrate
* Initial prey population
* Initial predator population
* Interval
* Step size  

Notice that the ***order doesn't matter***, since we will be calling the values we need by ***calling the key***, instead of the index.  

In this forward loop:
1. Get the initial values ***\$ x_0, y_0, and t_0 \$***(also get \$ t_f \$ which will be used later to end the loop).
2. Initialized empty lists ***T, X, Y*** to store the respective values of t, x, y during each iteration. 
3. Called the step size h from the params and the pre-defined function f_x, and initialized an instance of the RK4 used for the prey with the prey's derivative.
4. Called did the same thing, and initialized an instance of the RK4 used for the predator.
5. Established an 'infinite' loop.
6. Created conditions for the loop to end:
    * when t reaches \$ t_f \$
    * when either of the population reaches or passes zero  

Now we can play around with it! With the help of matplot:
```
import matplotlib.pyplot as plt
```
We get to have a better visual understanding of whatever is going on.  



