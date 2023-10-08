# I can't believe it can sort - visualized
A few months ago I came across a paper titled [Is this the simplest (and most surprising) sorting algorithm ever?](https://arxiv.org/abs/2110.01111) by Stanley P. Y. Fung in which he presents an incredibly simple sorting algorithm, the entire code is just 4 lines of code:

```
for i = 1 to n do
  for j = 1 to n do
    if A[i] < A[j] then
      swap A[i] and A[j]
```

That's it, that's the entire algorithm. As Fung indicates on the paper, at first glance it looks like this algorithm cannot possible work (e.g. the comparison looks wrong) but if you try it, you'll notice that it is indeed correct. Fung also indicates that the algorithm is not efficient by any stretch of imagination and shouldn't be used as a first example to introduce sorting to students. 

And yet, as Fung points out, the appealing thing about it "may be its simplicity, in terms of lines of code and the 'symmetry' of the two loops".

When I first saw this algorithm I had to try it myself to make sure it does indeed work since, as Fung indicates, "there seems to be no intuition behind it, and its correctness is not entirely obvious". Below is an implementation in JavaScript (notice that JavaScript arrays are zero-based):

```
for(i=0; i<n; i++) {
    for(j=0; j<n; j++) {
        if (a[i] < a[j]) {
            tmp = a[j];
            a[j] = a[i];
            a[i] = tmp;
        }
    }
}
```

And of course it works.

But despite the clear explanation on Fung's paper I couldn't quite understand the mechanics of it so I decided to create a sample program to visualize its execution. Below is an example of how each of the elements in the array are shifted as the algorithm executes with an input array `a = [10, 5, 99, 7]`:

```
initial data: 10, 5, 99, 7

i     j     a[i] < [j]  swap?     a
--    --    ----------  ------    -------------
0     0     10 < 10     false     10, 5, 99, 7
0     1     10 < 5      false     10, 5, 99, 7
0     2     10 < 99     true      **99**, 5, **10**, 7
0     3     99 < 7      false     99, 5, 10, 7
1     0     5 < 99      true      **5**, **99**, 10, 7
1     1     99 < 99     false     5, 99, 10, 7
1     2     99 < 10     false     5, 99, 10, 7
1     3     99 < 7      false     5, 99, 10, 7
2     0     10 < 5      false     5, 99, 10, 7
2     1     10 < 99     true      5, **10**, **99**, 7
2     2     99 < 99     false     5, 10, 99, 7
2     3     99 < 7      false     5, 10, 99, 7
3     0     7 < 5       false     5, 10, 99, 7
3     1     7 < 10      true      5, **7**, 99, **10**
3     2     10 < 99     true      5, 7, **10**, **99**
3     3     99 < 99     false     5, 7, 10, 99

final data: 5, 7, 10, 99
```

As Fung indicates, one of the surprising things of this algorithm is that, without any obvious logic, the algorithm performs two different phases: a select maximum phase and an insertion sort phase. 

The **select maximum phase** is executed when `i = 0` (notice that I am using zero-based indexes). In this phase the algorithm selects the *maximum number* and moves it to the front of the array, you can see this in the table above how when `i = 0 and j = 3` the resulting array is `a = [99, 5, 10, 7]` with `99` being the maximum value in our initial array. The fact that it moves the maximum value (not the lowest!) to the front is counter intuitive, but the next phase takes care of it.

The **insertion sort phase** is the rest of the execution, that is when `i = 1, 2, or 3`. In this phase the algorithm works similar to an [insertion sort algorithm](https://en.wikipedia.org/wiki/Insertion_sort). As you can see in the table above the insertions happen when `swap = true` and little by little it sorts the array by shifting the elements. For example, when `i = 1 and j = 0` it moves `5` to the front, when `i = 2 and j = 1` it moves `10` closer to the front, and so on, until `i = 3 and j = 2` when it gets to the final form `a = [5, 7, 10, 99]`.

If you want to visualize the algorithm yourself you can try this [HTML + JavaScript example](https://hectorcorrea.com/demos/i-cannot-believe-it-can-sort.html) and use different data to see how it behaves. The source code is available [here](https://gist.github.com/hectorcorrea/7332762794a3370a4722fe5c3dceaf0a).

**Update September 2022:** Fung has a [page with comments and criticism](https://www.cs.le.ac.uk/people/pyfung/sorting.html) that he has gotten about the algorithm that are interesting and worth reading if you have made it this far.


