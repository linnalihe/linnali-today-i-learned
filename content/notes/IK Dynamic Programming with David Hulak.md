---
date: 2024-02-27 19:47
tags:
  - swe-job-search
internal-links:
---
https://uplevel.interviewkickstart.com/resource/helpful-class-video-465956-953-23998-0
##### Two halves of the problem
Recursion
- foundation for DP
- actual bottleneck, high complexity, and intuition might play a role
- requires more practice and pattern recognition
DP
- primary goal for the class
- medium complexity and more algorithmic rather than intuitive after knowing the recursive relation
- becomes repetitive after solving a few problems

1. Optimizing recursive solutions
	- evaluate each subproblem once
2. Lazy manager approach
3. Top-down vs bottom-up DP
4. Counting problems
	- climbing n-stairs
	- calculating combinations
	- unique paths in the grid
5. DP was designed for optimization problems an not counting problems but turns out they are good for counting. Optimization problems include
	- maximum path sum in a grid
	- minimum cost stair climbing
	- coin change problem
6. Decision problems
7. Subproblems as prefixes/suffixes of a larger problem

DP walkthrough structure
- identify whether it's a optimization, counting, or decision problem with overlapping subproblems
- define the steps to be taken as a lazy manager
- assume you have the optimal answer from other steps
- define the function you're optimizing in plain english
- identify recursion formula

Draw the recursion tree
- overlapping subproblems
- optimal substructure

Memoization vs tabulation
- do we have to solve all subproblems?
- recursive overhead vs table creation?
- code complexity

If memoization, write the recursive code
- cache subproblems as you solve

If tabulation defines the DP table
- dimensions (1D, 2D)
- type (bool, int)
- range(0 to N, N-1 to 0, ...etc)
- base case conditions
- identify cell that contains the result
- identify traversal direction
- populate table

Usually want to go with tabulation when in doubt

Cutting rod to maximize profit
- optimization problem
- defining optimization function in plain english
	- f(i) = max profit we obtain by optimally cutting a rod of size i
	- have to solve all subproblems. If you do have to solve all subproblems, tabulation is a good choice. If not all subproblems, recursion with memo might be better to save space


![[Pasted image 20240228095404.png]]

![[Pasted image 20240228100327.png]]

Up to 2:25:00

Find if array can be partitioned into two subsets such that sums are equal
- decision problems
- recognizing patterns -> similar to the target sum, combinations question is similar with an added constraint of k. We need to find a subset that sums to the target which is sum of array divided by 2. The array sum subtract target will be the other half and check if it is equal to the target

![[Pasted image 20240228103351.png]]

Up to 3:00:00