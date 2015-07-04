---
layout: post
title: "Recursion vs. Iteration"
date: 2013-01-30 16:36
comments: true
categories: [Algorithm]
---

**Recursion** is the process of repeating items in a self-similiar way. A recursive function is one that calls itself.  
**Iteration** means the act of repeating a process with the aim of approaching a desired goal, target or result. Each repetition of the process is also called "iteration", and the results of one iteration are used as the starting point for the next iteration.
<!-- more -->

Divide and Conquer
-------------------------
* Roughly speaking, recursion and iteration perform the same kinds of task:  
    --> solve the complicated task one piece at a time, and combine the result.
* Emphasis of iteration:  
    --> keep repeating until a task is "done".  
    e.g., loop counter reaches limit.  
    linked list reaches null pointer.  
    boolean value becomes true.
* Emphasis of recursion:  
    --> Solve a large problem by breaking it up into smaller and smaller pieces until you can solve it; combine the results.  
    In mathematics and computer science, a class of objects or methods exhibit recursive behavior when they can be defined by two properties:  
    1. A simple basic case(or cases), and  
    2. A set of rules which reduce all other cases toward the basic case.

Which is Better?
------------------------
* No clear answer, but there are know trade-offs.   
* "Mathematicians" often prefer recursive approach.  
    --> solution often shorter, closer in spirits to abstract mathematical entry.  
    --> good recursive solutions may be more difficult to design and test.  
* "Programmers" often prefer iterative solutions.  
    --> somehow, it seems more appealing to many.  
    --> control stays local to loop, less "magical".  

Example
-----------------------
The **Fibonacci sequence** is a classic example of recursion and iteration.  
*Recursion Version:*
{% codeblock Factorial.java lang:java %}
public static long factRecusive(int n) {
    if (n == 0) {
        return 1;
    } else {
        return n * factRecusive(n - 1);
    }
}
{% endcodeblock %}
*Loop Version:*
{% codeblock Factorial.java lang:java %}
public static long factLoop(int n) {
    if (n == 0) {
        return 1;
    }

    long sum = 1;
    for (int i = 1; i <= n; i++) {
        sum *= i;
    }
    return sum;
}
{% endcodeblock %}
