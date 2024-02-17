---
date: 2024-02-13 09:17
tags:
  - lesson-note
  - swe-job-search
internal-links: []
---
https://uplevel.interviewkickstart.com/resource/rc-video-573732-921951-862-4935-4924595

`problem-solving` and `coding fluency` are two halves of the technical interview

`problem-solving` is a question you have no idea initially what methods will work. starting state is that you don't know anything. If you already know how to approach it, it's an exercise, not a problem
Need to deploy problem-solving strategies.
For computational algorithm problems, need to use
- brute force
- decrease and conquer
- divide and conquer
- transform and conquer
Can you design a correct and efficient algorithm?
Problem-solving and coding skills and your knowledge of computer science and engineering fundamentals

How to approach the tension where you have test anxiety during interviews?
Become a problem solver by developing inner confidence while getting exposed to popular interview questions grouped by coding patterns

Increase the timescale of concentration from half an hour to an hour. Failure is always worthwhile even if it leads to dead-ends. Exploring, getting lost, and using what you learn on new problems. 

Sorting => selection, bubble, insertion, merge, quick, heap
Be familiar with arithmetic series and geometric series

up to @1:00:00

Good to always start with talking about the brute force
Will rely on decrease and conquer, and transform and conquer the most
merge sort and trees will use divide and conquer
dynamic programming is special type of decrease and conquer

Two sum as an example
When you're looking for a true or false, it's a decision problem
Brute force
- whatever comes to your mind first (subjective)
- exhaustively look at all options. all pairs of numbers for a nested for loop

divide and conquer is good for merge sort and trees
- work is done upfront, quicksort
- work is done at the end, mergesort
- naturally recursive bc will from the perspective of an internal node manager

for two sum problem, if we go down the divide and conquer route, time it takes will not be better than 2 nest for loops because `T(n) = T(n/2) + T(n/2) + T(n^2)` where left and right will have to do `T(n/2)` work and the "manager" will have to still compare the left and right to check scenerio where `value on right + value one left` is the target which is `T(n^2)`

Usually want to start by decrease and conquer
- can be recursive but can be written iteratively
- do work upfront
	- take a bite and give the rest to another worker
	- tail recursion
- do work at end
	- give the rest to another worker and add what the internal node manager is doing at the end
	- head recursion
- time complexity is still O(n^2) because it's an arithmetic series if the current node does a linear scan and then assign `n-2` values to a subordinate

Next we look at transform and conquer to ask what data structure we can use to search efficiently
- two different ways to transform
	- input rearrangement which pre-sorting falls under
	- representation change where we dump it into another data structure
- data structures to use
	- balanced BST
	- Hash Table

up to @2:00:00

presort and then perform n-binary searches with time complexity O(n log n)
But we noticed that as we move to the left, the value that sums to target with the current value shifts to the right. So we can use a variable to keep track of this history
Space-Time Tradeoff
- use extra space to reduce the time with Two Pointer Pass

presorting + binary search
presorting + one pas
presorting + two pointer

solve these problems

![[Pasted image 20240214192257.png]]

![[Pasted image 20240214192321.png]]

Problem reduction
Transform the problem into a different problem

Find the intersection where we want the docs where both words appear
![[Pasted image 20240214193033.png]]


Lower bounds where you can't do better. 
O(N) is usually a good lower bound for array problems like sliding window, two-pointer, pre-fix sums

up to @4:35:00

so far problems are straightforward, now we dive into more complex problems

find kth largest value
start with pre-sort and lookup with is O(n log n) + O(1)
we can do better with using a minheap at O(n log k)
we can do better with quick select at O(n)

Quick select is better than using a heap

With decrease and conquer strategies, it's an arithmetic series
If the work at each manager is constant than it's O(n)
If the work at each manager is O(n) then total time is O(n^2)
Something like binary search where it's cut in half each time, it's O(log n)

Divide and conquer
Decrease by half each time where each level is `O(c*n)` where c is a constant
So it becomes `O(cn*log n)` where cn is multiplied by height of tree, which is then `O(n log n)`
![[Pasted image 20240216124454.png]]

Space repetition on the problem in each category
