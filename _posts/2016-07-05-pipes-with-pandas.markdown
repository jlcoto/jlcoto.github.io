---
layout: post
title:  "Data pipes with pandas"
date:   2016-07-04 00:24:55 +0100
category: Data Analysis
tags: [pandas]
---

Lately, I have kept myself busy reading the `pandas` [documentation](http://pandas.pydata.org/pandas-docs/stable/). I am always happy when I find something very useful that I didn't know before. One of the things that I've lately discovered is piping. 

The idea is simple. Suppose you want to apply a function to a data frame or series, to then apply other, other, ... One way would be to perform this operations in a "sandwich" like fashion:

{% highlight python %}
df = foo3(foo2(foo1(df, arg1= 1), arg2= 2), arg3=3)
{% endhighlight %}

In the long run, this notation becomes fairly messy and error prone. What you want to do here is use `pipe()`. Pipe can be thought of as a function chaining. This is how you'd perform the same task as before with `pipe()`:

{% highlight python %}
df.pipe(foo1, arg1=1).
	pipe(foo2, arg2=2).
	pipe(foo3, arg3=3)
{% endhighlight %}

This way is a cleaner way that helps keep track the order in which the functions and its corresponding arguments are applied.

## Example 1

Suppose, for a moment, as strange as it may sound now, that you want to apply  the following three functions to a data set or series: The first function subtracts a number from the data. The second function divides the data by a given parameter. The third function multiplies the data by a given parameter and then adds another given number. 

Here is the data set.

{::options parse_block_html="true" /}

<div class="tables-back">

<div class="single-table">


|    |   col_1 |   col_2 |   col_3 |
|----|:-------:|:-------:|---------|
| **A**  |       1 |       1 |       1 |
| **B**  |       2 |       2 |       2 |
| **C**  |       3 |       3 |       3 |

</div>

</div>

I have already created the functions. Here is how we apply them using `pipe()`:

{% highlight python %}
data_set.pipe(adder, add=2)
	.pipe(div, div=2)
	.pipe(sub_mult, sub=2, mult=2)
{% endhighlight %}


First I am adding two to every single entry of the data set with the `adder` function. Then, I use the `div` function to divide by two. Finally, I reverse the whole process with the `sub_mult` function, multiplying by two and subtracting two. Unsurprisingly, the application of the functions in that order give us our original data set. Here is how the data set is transformed with every stage of our pipe:

{::options parse_block_html="true" /}

<div class="tables-back">

<div class="single-table">

<span class="table-pipe"> original </span> 

|    |   col_1 |   col_2 |   col_3 |
|----|:-------:|:-------:|---------|
| **A**  |       1 |       1 |       1 |
| **B**  |       2 |       2 |       2 |
| **C**  |       3 |       3 |       3 |


<span class="table-pipe"> (adder + 2) </span> <img src="/img/arrow.png" alt="arrow pipe" class="arrow-down"> 


|    |   col_1 |   col_2 |   col_3 |
|----|:-------:|:-------:|---------|
| **A**  |       3 |       3 |       3 |
| **B**  |       4 |       4 |       4 |
| **C**  |       5 |       5 |       5 |


<span class="table-pipe"> (div  / 2) </span> <img src="/img/arrow.png" alt="arrow pipe" class="arrow-down"> 


|    |   col_1 |   col_2 |   col_3 |
|----|:-------:|:-------:|---------|
| **A**  |     1.5 |     1.5 |     1.5 |
| **B**  |     2   |     2   |     2   |
| **C**  |     2.5 |     2.5 |     2.5 |

<span class="table-pipe"> (sub_mult / 2 - 2) </span> <img src="/img/arrow.png" alt="arrow pipe" class="arrow-down"> 


|    |   col_1 |   col_2 |   col_3 |
|----|:-------:|:-------:|---------|
| **A**  |       1 |       1 |       1 |
| **B**  |       2 |       2 |       2 |
| **C**  |       3 |       3 |       3 |


</div>

</div>


**Note**: To apply `pipe()`, the first argument of the function must be the data set. For example, `adder` accepts two arguments `adder(data, add)`. As `data` is the first parameter that takes in the data set, we can directly use `pipe()`. When this is not the case, no sweat. There's a way around this. We only need to specify to `pipe` what's the name of the argument in the function that refers to the data set. 

## Example 2

Suppose, now, that the function adder is specified as `adder(add, data)`. As the data is not the first argument, we need to pass it to pipe as follows:

{% highlight python %}
data_set.pipe((adder, "data"), 2)
{% endhighlight %}

In the end, we get the same result as before. All the entries are added 2.

<div class="tables-back">

<div class="single-table">

<span class="table-pipe"> original </span> 


|    |   col_1 |   col_2 |   col_3 |
|----|:-------:|:-------:|---------|
| **A**  |       1 |       1 |       1 |
| **B**  |       2 |       2 |       2 |
| **C**  |       3 |       3 |       3 |

<span class="table-pipe"> (adder + 2) </span> <img src="/img/arrow.png" alt="arrow pipe" class="arrow-down"> 

|    |   col_1 |   col_2 |   col_3 |
|----|:-------:|:-------:|---------|
| **A**  |       3 |       3 |       3 |
| **B**  |       4 |       4 |       4 |
| **C**  |       5 |       5 |       5 |

</div>

</div>

<br>

<div align="right">  
**Voilà.** 
</div>

