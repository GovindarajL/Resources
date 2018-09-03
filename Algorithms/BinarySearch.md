- Most famous and fundamental Algorithm
problem: Given a sorted array of integer we want to find out whether x is exist or not
Interesting problem to try out:-
https://www.geeksforgeeks.org/search-an-element-in-a-sorted-and-pivoted-array/

Three cases: 1. x==A[mid] 2. x<A[mid] 3. x>A[mid] 

Using two indices we use our search space start and end initially start will be zero and end will be length-1. 
mid = (start+end)/2
if x<A[mid] then start=0 end = mid-1
if x>A[mid] then start=mid+1 end= length-1

we can reduce the search by half using binary search. Only constraint is array should be sorted. 

**Also explore that whether binary search algorithm will work to find mulitple values in an array**

Prerequisite for Binary Search Algorithm:-
1. Input Array to this algorithm should be sorted.

Refer this:-
http://ibt.edu.pk/qec/jict/2.1/7%20Multiple%20Values%20Search%20Algorithm.pdf
