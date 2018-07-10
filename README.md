# Real
Boost numerical data type for real numbers representation using range arithmetic.

## Introduction

### The problem addressed by boost::real
Several times, when dealing with complex mathematical calculus, numerical errors can be carried from one operation to the next and after several steps, the error may significantly increase obtaining a non-trustworthy result. Normally, in these situations, the error is generated by the representation precision limit that truncates those numbers that do not fit in the representation precision generating errors without at least keeping track of the magnitude of the error. When this is the case, the real result is equal to the obtained approximated result plus the error (which is unknown).

Another major problem when dealing with real numbers is the representation of the irrational number as the number π or e<sup>π</sup>, they are not handled by the native number data types causing limitations when calculations are based on those numbers. Normally we define a truncation of those numbers that are good enough for our purposes, but many times, the needed precision depends on the operation to do and to the composition of multiple operations, therefore, we are unable to determine which is the correct precision until we run the programme.

### The boost::real solution
Boost::real is a real number representation data type that address the mentioned issues using range arithmetic [1] and defining the precision as dynamical to be determined in run-time. The main goal of this data type is to represent a real number "a" as a programme that returns a finite or infinite set of intervals a(k) = [m<sub>k</sub> - e<sub>k</sub>, m<sub>k</sub> + e<sub>k</sub>], K ∈ N ≥ 0, e<sub>k</sub> ≥ 0. Where K1 < K2 ⇒ a(k2) &sub; a(k1). For this purposes, any Boost::real number has a precision const iterator that iterates the series of intervals representing the number. The series if interval are sorted from larger to smaller, thus, each time the iterator is increased, the number precision increases becuase the new calculated interval is smaller until the interval is the number itself (if possible).

Also, to allow representing irrational numbers as π or e<sup>π</sup>, boost::real has a constructor that takes as parameter a function pointer, functor (function object with the operator ()) or lambda expression that for any integer n > 0, the function returns the n-th digit of the represented number. For example, the number &frac13 can easily be represented by a program that for any input n > 0, the function returns 3.

## The boost::real numbers representation
In boost::real, a number has one of the next three representations:

    1. Explicit number: A number is a vector of digits sorted as in the number natural representation. To determine where the integer part ends and the fractional part starts, an integer is used as the exponent of a floating point number and determines where the integer part start and the fractional ends. Also a boolean is used to set the number as positive (True) or negative (False)
    2. Algorithmic number: This representation is equal to the Explicit number but instead of using a vector of digits, a lambda function must be provided. The lambda function takes an unsigned integer "n" as parameter and returns the n-th digit of the number.
    3. A number is a composition of two numbers related by an operator (+, -, *, /), the number creates pointers to the operands and each time the number is used, the operation is evaluated to return the result.

Because of the third representation type, a number resulting from a complex calculus is a binary tree where each internal vertex is an operation and the vertex children are its operands. The tree leaves are those numbers represented by either (1) or (2) while the internal vertex are those numbers represented by (3). More information about the used number representation can be found in [3]

## The boost::real precision iterator.
The boost::real::const_precision_iterator is a forward iterator [4] that iterates through the number interval precisions. The iterator returns two numbers, a lower and an upper bound that represent the [m<sub>k</sub> - e<sub>k</sub>, m<sub>k</sub> + e<sub>k</sub>] limits of the number for a given precision. Each time the iterator is incremented, the interval range is decreased and a new interval with a better precision is obtained. Normally, there is no need to interact with the precision iterator and it is used by the boost::real operators <<, < and >.

## Interface

### Constructors and destructors
    1. boost::real()
    2. boost::real(initializer_vector<int> il)
    3. boost::real(initializer_vector<int> il, int exponent)
    4. boost::real(initializer_vector<int> il, int exponent, bool positive)
    5. boost::real((unsigned int) -> int digits, int exponent)
    5. boost::real((unsigned int) -> int digits, int exponent, bool positive)
    3. boost::real(const boost::real& x)
    4. boost::~real()
  
> (1) **Default constructor** 
> Creates a real instance that represents the number 0.
>
> (2) **Initializer list constructor** 
> Creates a real instance that represents an integer number where all the il numbers are form the integer part in the same order.
>
> (3) **Initializer list constructor with exponent** 
> Creates a real instance that represents the number where the exponent is used to set the number integer part and the elements of the il list are the digits the number in the same order.
>
> (4) **Initializer list constructor with exponent and sign** 
> Creates a real instance that represents the number where the exponent is used to set the number integer part and the elements of the il list are the digits the number in the same order. This constructor uses the sign to determine if the number is positive or negative.
>
> (3) **Lambda function constructor with exponent** 
> Creates a real instance that represents the number where the exponent is used to set the number integer part and the lambda function digits is used to know the number digit, this function returns the n-th number digit.
>
> (4) **Lambda function constructor with exponent and sign** 
> Creates a real instance that represents the number where the exponent is used to set the number integer part and the lambda function digits is used to know the number digit, this function returns the n-th number digit. This constructor uses the sign to determine if the number is positive or negative.
>
>(3) **Copy constructor** 
> Creates a copy of the number x, if the number is an operation, then, the constructor creates new copies of the x operands.
>
> (4) **Default destructor** 
> If the number is an operator, the destructor destroys its operands.

### Operators

    1. boost::real operator+=(const boost::real& x)
    2. boost::real operator-=(const boost::real& x)
    3. boost::real operator*=(const boost::real& x)
    4. boost::real operator+(const boost::real& x) const
    5. boost::real operator-(const boost::real& x) const
    6. boost::real operator*(const boost::real& x) const
    7. boost::real& operator=(const boost::real& x)
    8. bool operator<(const real& other) const
    9. std::ostream& operator<<(std::ostream& os, const boost::real& x)
    10. int operator[](unsigned int n) const

> (1) Modifies the number to use the third representation. and sets copies of *this and x respectively as the left and right operands and sets addition as the operation.
>
> (2) Modifies the number to use the third representation. and sets copies of *this and x respectively as the left and right operands and sets subtraction as the operation.
>
> (3) Modifies the number to use the third representation. and sets copies of *this and x respectively as the left and right operands and sets multiplication as the operation.
>
> (4) Creates a new boost::real number using the third representation. For this purpose, the operator creates copies of *this and x to use as the new real number operands and defines the addition as the operation.
>
> (5) Creates a new boost::real number using the third representation. For this purpose, the operator creates copies of *this and x to use as the new real number operands and defines the subtraction as the operation.
>
> (6) Creates a new boost::real number using the third representation. For this purpose, the operator creates copies of *this and x to use as the new real number operands and defines the multiplication as the operation.
>
> (7) Uses the copy constructor to create a copy of x stored in *this
>
> (8) Compares *this with x to check if *this is lower than x. This operator creates two precision iterators (one for each number) and iterates until the number ranges stop overlapping when that happens, it compares the ranges bounds to determine if *this is less than x. **WARNING:** If *this is equal to x, then the ranges will always overlap, because of this, the operator uses a max precision limit and if that limit is reached, the operator throws a boost::real::precision_exception.
>
> (9) Creates a const_precision_iterator to print the number using the iterator << operator.
>
> (10) Returns the n-th digit of the represented number. **WARNING:** This operator throws invalid_representation_exception for the third representation because only explicit and algorithmic numbers can be asked for the n-th digit.

### Other methods

    1. boost::real::const_precision_iterator boost::real::cbegin()

> (1) Construct a new const_precision_iterator that iterate over the *this number precisions.

## boost::real::const_precision_iterator interface

### Constructors
    1. boost::real::const_precision_iterator()
    2. boost::real::const_precision_iterator(real const* x)
    3. boost::real::const_precision_iterator(const boost::real::const_precision_iterator& x)
    
> (1) ** Default constructor **
> Create an empty iterator that does not point any number and thus, cannot yet be iterated.
>
> (2) Creates an iterator that points to the x number and iterates the number precision. If the number is deleted, the iterator behaviour is undefined.
>
> (3) ** Copy constructor ** 
> Creates an iterator that points the same number than the x iterator does and is initialized in the same precision that x.


### Operators
    1. void operator++()
    
> (1) Increases the pointer to the next precision range o the pointed number. If the pointed number is represented by (1) and the full number precision is reached, then the operator has no effect because the number approximation lower and upper bounds are equals and the number range is the number itself.
>

## Examples

```cpp
#include <iostream>
#include <real/real.hpp>

int main() {
    int i;
    boost::real::real a,b,c,d,e,f,g,g2,h,j,k;

    c = boost::real::real({9,9,9,9,9,9});
    d = boost::real::real({9,9,9,9,9,9});
    e = c + d;

    std::cout << "c: " << c << std::endl;
    std::cout << "d: " << d << std::endl;

    boost::real::real::const_precision_iterator it = e.cbegin();
    for(i = 0; i < 10; ++i) {
        std::cout << "e iteration " << i <<": " << it << std::endl;
        ++it;
    }

    f = boost::real::real({9,9,9,9,9,9});
    g = boost::real::real({9,9,9,9,9,8});

    if (g < f) {
        std::cout << "g < f --> true" << std::endl;
    } else {
        std::cout << "g < f --> false" << std::endl;
    }

    h = f - g;

    std::cout << "f: " << f << std::endl;
    std::cout << "g: " << g << std::endl;
    std::cout << "h: " << h << std::endl;

    return 0;
}
```

### Output
```cpp
c: 0.99999900
d: 0.99999900
e iteration 0: [1.98, 2.00]
e iteration 1: [1.998, 2.000]
e iteration 2: [1.9998, 2.0000]
e iteration 3: [1.99998, 2.00000]
e iteration 4: 1.999998
e iteration 5: 1.999998
e iteration 6: 1.999998
e iteration 7: 1.999998
e iteration 8: 1.999998
e iteration 9: 1.999998
g < f --> true
f: 0.999999
g: 0.999998
h: 0.000001
```

## References
    1. Computable calculus / Oliver Aberth, San Diego : Academic Press, c2001
    2. Lambov, B. (2007). RealLib: An efficient implementation of exact real arithmetic. Mathematical Structures in Computer Science, 17(1), 81-98.
    3. Aberth, O., & Schaefer, M. J. (1992). Precise computation using range arithmetic, via C++. ACM Transactions on Mathematical Software (TOMS), 18(4), 481-491.
    4. https://en.cppreference.com/w/cpp/concept/ForwardIterator