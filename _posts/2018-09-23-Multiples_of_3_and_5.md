---

layout: post
title: [Euler Project] Problem 1. Find the sum of all the multiples of 3 or 5 below 1000.
description: >
  [참조사이트](https://projecteuler.net) 

tags: [neweins]

---

# [Euler Project] Problem 1. Find the sum of all the multiples of 3 or 5 below 1000.

~~~c
// (1+2+3 ..... + p) = 1/2 * p * (p+1)
#define MAX_NUM 1000


long long sumMultipleOf(int number){
    long long maxNumOfDivisible = (MAX_NUM-1) / number;
    
    return number * maxNumOfDivisible * (maxNumOfDivisible + 1) /2 ;
   
}

int main()
{
    printf("%lld\n", sumMultipleOf(3) + sumMultipleOf(5) - sumMultipleOf(15));
}


~~~