---
layout: post
title: "Solving Lotka Volterra using Fourth order runge-kutta"
date: 2024-07-30 05:15:25 -0500
categories: jekyll update
---

### Intro
Last article, we used the forward euler method to solve an initial value problem from Lotka Volterra system, today we will be using a different method, ***4th order runge-kutta***, abbreviated ***RK4***, to do the same thing.

### RK4
Founded by Carl Runge and Martin Kutta, the RK4 has the following formula:  
\$$ y_{n+1} = y_n + 1/6(K1 + 2K_2 + 2K_3 + K_4) \$$  
\$ K_1 = hf(x_n, y_n) \$
\$ K_2 = hf(x_n + 1/2h, y_n + 1/2K_1) \$


