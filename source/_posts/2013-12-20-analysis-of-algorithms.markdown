---
layout: post
title: "Algorithm4th: analyzing of algorithms"
date: 2013-12-20 11:08
comments: true
categories: [Algorithm]
---

#### Mathematical Models
D.E.Knuth's basic insight is simple:the total running time of a program is determined by two primary factors:

* The cost of executing each statement 
* The frequency of execution of each statement  

The former is a property of the computer, the Java compiler and the operating system; the latter is a property of the program and the input.
#### Tilde approximations 

	Definition. We write ~f(N) to represent any-function that, when divided by f(N), approaches 1 as N grows, and we write g(N)~f(N) to indicate that g(N)/f(N) approaches 1 as N grows.
	
For example, we use the approximation ~N^3/6 to describe the number of times the if statement in ThreeSum is executed, since N^3/6 - N^2/2 + N/3 divided by N^3/6 approaches 1 as N grows.

{% codeblock ThreeSum.java lang:java %}
package fundamentals.analysisOfAlgorithms;

/**
 * Count the number of triples in a file of N integers that sum to 0.
 *
 * @author ThomsonTang
 * @version ${VERSION}
 * @date 12/20/13
 */
public class ThreeSum {
    public static int count(int[] a) {
        int N = a.length;
        int cnt = 0;
        for (int i = 0; i < N; i++) {
            for (int j = i + 1; j < N; j++) {
                for (int k = j + 1; k < N; k++) {
                    if (a[i] + a[j] + a[k] == 0) {
                        cnt++;
                    }
                }
            }
        }
        return cnt;
    }
} 
{% endcodeblock %}

#### Mathematical models for running time
In principle, accurate mathematical models are available. We use approximate models.

![running-time-formular]

Ex. for ThreeSum.java above, we get **T(N) ~ cN^3**.

#### Common order-of-growth classifications
There are small set of functions:
*** 1, logN, N, NlogN, N^2, N^3, and 2^N ***
suffices to describe order-of-growth of typical algorithms.

![typical-orders-of-growth]

![typical-orders-of-growth-2]


[running-time-formular]: /images/blog/algorithm/running-time-formular.png
[typical-orders-of-growth]: /images/blog/algorithm/typical-orders-of-growth.png
[typical-orders-of-growth-2]: /images/blog/algorithm/typical-orders-of-growth-2.png
