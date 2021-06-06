---
layout: single
title: Sudoku and the Power of Reduction 
classes: wide 
---

As much as it is mentioned, using mathematical techniques to solve programming-related problems has always remained a distant idea for me. However, by diving into the world of Sudoku, I was able to experience and learn how powerful and important mathematical techniques really are. 

Through the use of reduction, a typical Sudoku puzzle can be solved within milliseconds, a performance that outshines many search algorithms. 

## Sudoku
First, we need to define the problem that we are trying to solve. 

A typical 9x9 sudoku is a logical puzzle that is subdivided into nine 3x3 blocks. It has 81 cells and every cell needs to be filled out based on the following rules: 

- A cell must have one and only one value ranged from [1, 9] 
- All cells in a row must not have the same values
- All cells in a column must not have the same values
- All cells in a block must not have the same values

## Possible Algorithms 
A naive algorithm at solving any sudokus would be doing BFS/DFS on the problem space. Essentially, we would try out every possible combination of number placements and accept the one that satisfies all constraints. The time complexity of this algorithm is clearly not ideal. 

Another possible approach is to combine plain searching algorithm, DFS for example, and filtering algorithms. One benefit of trying to solve a game that can be traced back to the 19th century is how extensively it has been studied. Over the years, many [techniques](https://bestofsudoku.com/sudoku-strategy) that enable players to make non-trivial decisions have been developed. Therefore, at each step of DFS, we can utilize these techniques to prune out value domains. Though doing so would drastically improve our naive algorithm's performance, the actual implementation of these techniques can be rather tedious. Besides this, programmers need to overcome another challenge: how many and what techniques should one choose so that each recursive step could prune out enough values within a reasonable time.

So, is there an algorithm that can solve our problems? The answer is yes. But, before we take a look at our algorithm, we must take a little detour to figure out what is an exact cover problem. 

## Exact Cover Problem
An [exact cover problem](https://en.wikipedia.org/wiki/Exact_cover) is surprisingly easy to describe. It can be formally stated as follows. Given a collection $$C$$ that contains some number of subsets of a set $$S$$, an exact cover of $$S$$, $$C^\prime$$, is a subcollection of $$C$$ such that 

1. The intersection of any two distinct sets from $$C^\prime$$ is empty. 
2. The union of all subsets in $$C^\prime$$ should equal to $$S$$. 

Although the exact cover problem is NP-complete, Donald Knuth's [Algorithm X](https://en.wikipedia.org/wiki/Knuth%27s_Algorithm_X) has proven to be a rather efficient algorithm. Since Wikipedia has already done an amazing job at explaining and demonstrating the algorithm, I decided not to reinvent the wheel. Instead, we shall focus on how we could use reduction to solve a Sudoku. 

## Reduction 
In order to successfully deploy Algorithm X to solve a Sudoku, we must first reduce, or convert, Sudoku into an exact cover problem. Luckily, there is a general recipe that helps us to convert any CSP to an exact cover problem. It states that every column in the incident matrix corresponds to a constraint that must be satisfied and every row in the incident matrix is a value-variable pair. 

For a Sudoku, we know that for each cell, they have to abide by the four rules mentioned above. That covers (no pun intended) all the columns. We also know that for each cell they can have 9 values. And this covers all the rows. 
To ensure that all clues are included in the final solution output by Algorithm X, we can simply pre-select all the respective rows. 

## Performance Analysis 
Now, we have shown how we can convert a Sudoku to an exact cover problem. There is only one question that is yet to be answered: why is this better? 

First, Algorithm X's time performance is strictly bounded. We know that the run time of this algorithm is entirely based on the size of the incident matrix. In this case, the number of columns is $$9 \cdot 9 \cdot 4 = 324$$ and the number of rows is $$9 \cdot 9 \cdot 9 = 729$$. Thus, the matrix's size is fixed. Moreover, Algorithm X always chooses the least satisfiable constraint which allows the algorithm to terminate early when it enters down a branch that will never reach the solution. Most importantly, this algorithm ignores things that conventionally make a Sudoku be "hard". The only thing that matters is the number of clues provided which is also [bounded](https://www.nature.com/articles/nature.2012.9751#:~:text=A%20sudoku%20puzzle%20needs%20at%20least%2017%20clues%20to%20be%20solvable.).

As for implementation difficulty, Donald Knuth's original paper proposed a data structure called dancing links which supports fast search, insertion, and deletion. But, the implementation of dancing links can hardly be argued as easy. Fortunate enough, with the work done by Ali Assaf, [Algorithm X can be implemented with python's native library using only 30 lines](https://www.cs.mcgill.ca/~aassaf9/python/algorithm_x.html)! 

Lastly, I want to include a time performance table to demonstrate how powerful Algorithm X is. Remember, all of this would not have been possible if we don't know how to reduce Sudoku into an exact cover problem. 


|:-----------------------------:|----------------|-------|--------------|
|                               | Avg. Time (ms) | Steps | Success Rate |
| Easy                          | 3.400          | 60    | 100%         |
| Medium                        | 4.400          | 83    | 100%         |
| Hard                          | 3.439          | 72    | 100%         |
| Expert                        | 9.478          | 276   | 100%         |

[This dataset is provided by David Radcliffe on Kaggle](https://www.kaggle.com/radcliffe/3-million-sudoku-puzzles-with-ratings). The puzzles are divided into four difficulty categories based on their difficulty rating, 0-2 is easy, 2-4 is medium, 4-6 is hard, 6 and above is expert. Ratings are based on the average tree depth that an automatic solver going into when it has found the solution. One thing to note is that most sudokus in the dataset have between 23-26 clues
