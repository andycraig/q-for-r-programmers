# Q for R programmers

**tl;dr:** There's a language called q which feels quite similar to R, with a particularly concise syntax and several nice other features.

**Who is this article for?** R programmers with a general interest in programming languages.

**Who am I?** A data scientist who these days mostly works in Python, has worked a lot with R in the past, and has experimented with q a bit in my free time.

# R and q

I think R and q may be a case of convergent evolution. Both take inspiration from the language APL, but as far as I can tell were developed independently after that. Q is used mostly in finance, and there doesn't seem to be much overlap in the q and R user bases.

Let's have a look at some similarities between R and q.

# Data frames

Q is one of the few languages I know of other than R that has built-in data frames. They're called tables in q.

```q
/ create a table of some of the data from the 'Orange' R dataset
Orange: ([] Tree: 1 1 1 2 2 2 3 3 3; age: 118 484 664 118 484 664 118 484 664; circumference: 30 58 87 33 69 111 30 51 75)
/ display it
Orange
```

We get output like this:

```
Tree age circumference
----------------------
1    118 30
1    484 58
1    664 87
2    118 33
2    484 69
2    664 111
3    118 30
3    484 51
3    664 75
```

Note that q's syntax is very concise. We didn't need any commas between the array elements.

Q has a nice SQL-like query language that can be used on tables. In R, we might use {dplyr} to summarise the data like this:

```r
Orange %>%
    filter(age >= 200) %>%
    group_by(Tree) %>%
    summarise(mean_circumference = mean(circumference))
```

Q version:

```q
select mean_circumference: avg circumference by Tree from Orange where age >= 200
```

Here's the output:

```
Tree| mean_circumference
----| ------------------
1   | 72.5
2   | 90
3   | 63
```

`Tree` is now separated from the other column by `|` characters to signify that it's become an index (similar to a pandas index in Python).

# Arrays and lists

In both R and q the basic data type is an array:

R:

```r
a <- c(1, 2, 3)
```

Q:

```q
a: 1 2 3
```

They both have lists:

R:

```r
b <- list(c(1, 2, 3), c(4, 5, 6, 7))
d <- list(c(1, 2, 3), "abc")
```

Q:

```q
b: (1 2 3; 4 5 6 7)
d: (1 2 3; "abc")
```

We can apply a function across to each element of a list with `Map` in R and `each` in q:

```R
Map(length, b)
# returns list(3, 4)
```

```q
count each b
/ returns (3; 4)
```

As in R, we can index into arrays with `[]`:

```q
a[2]
/ returns 3 in q, as q arrays are 0-indexed
```

There's a symmetry in q between function arguments and array indexing:

```q
/ call function count with argument 2:
count[2]
/ get element 2 of a:
a[2]
```

# More about functions

Similar to the implicit `.x` and `.y` parameters in many of the {purrr} library functions, q functions have implicit parameters `x`, `y` and `z`:

```q
/ create a function f with three parameters
f: {x + y * z}
/ call f with arguments 1, 2 and 3
f[1; 2; 3]
/ output is 7
```

Another nice feature of q is that we can make partial functions just by specifying some of the arguments:

```q
/ create a partial function with x set to 1
f1: f[1]
f1[2; 3]
/ output is 7
```

If there's only one argument to a function, we don't actually need the braces:

```q
/ apply function 'til' to argument 5, returning value 0 1 2 3 4
til 5
/ apply two functions in sequence, returning value 5
count til 5
```

Like `purrr::compose`, we can compose functions with `'[;]`:

```q
/ we need parentheses when assigning composed functions to a variable
g: ('[sum; til])
g 5
/ output is 10 (0 + 1 + 2 + 3 + 4)
```

We can declare longer functions like this:

```q
/ a multi-line function with parameters t and u
g: {[t; u];
    v: t + u;
    v + 1}
```

Like R, the last line of a function is the return value.

# In conclusion

Coming from R, q feels quite familar: the basic variables are arrays and data frames are built in. It has some nice features like a query language for data frames and implicit arguments for functions. There's also very little 'punctuation': we don't need commas between array elements or brackets around single function arguments.

After using R for many years I was surprised to find that there was another widely used language out there that had so many similarities but had been completely off my radar.

# More q

There's lots more information about q on the Kx website: https://code.kx.com/q/learn/

A free non-commercial license is available for one year, although you have to give your name and email address.

Get license: https://code.kx.com/q/learn/licensing/

Download and install: https://code.kx.com/q/learn/install/
