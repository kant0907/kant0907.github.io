---
layout: post
title: "Different modulo operation in cpp and python"
date: 2026-01-11 15:00:00
categories: [blog]
---

## modulo operation in cpp

    -7 % 3   // -1
    7 % -3   // 1
    -7 % -3  // -1

a / b for integers is truncation toward 0
% is consistent with truncation:
a == (a / b) * b + (a % b)

## modulo operation in python

    -7 % 3   # 2
    7 % -3   # -2
    -7 % -3  # -1

a // b is floor division (rounds down toward −∞)
% is consistent with floor division:
a == (a // b) * b + (a % b)

## Recap

In C++ (since C++11), the % operator follows the rule that the result takes the sign of the dividend (left operand).

In Python, the % operator follows the mathematical definition of modulus. The result always has the same sign as the divisor (right operand) and is always non-negative when the divisor is positive.
