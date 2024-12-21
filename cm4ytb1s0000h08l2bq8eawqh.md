---
title: "Modular Arithmetic in Cryptography"
seoDescription: "Modular arithmetic is crucial in cryptography for algorithms, security, and efficient operations like addition, subtraction, and multiplication"
datePublished: Sat Dec 21 2024 23:30:13 GMT+0000 (Coordinated Universal Time)
cuid: cm4ytb1s0000h08l2bq8eawqh
slug: modular-arithmetic-in-cryptography
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1734659076001/458959d6-9f83-40b2-b26d-51052fd35915.png
tags: encryption, math, cryptography, mathematics, arithmentc-operators

---

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text"><strong>Proceed with caution! </strong>Few people believe I’m not good at mathematics. Of course, I disagree. I’m really good at explaining and understanding math concepts; there's a reason why my first article was about math and crypto. Either way, I’m no expert in Mathematics or Cryptography. In any case, if you spot any mistake, let me know. I’ll learn and fix that. At the end, I'm a self-proclaimed mathematician with 6 backlogs in the same subject.</div>
</div>

Modular Arithmetic is about **Integers**. Where things revolves around modules **%** and congruence **≅**. Congruence means two different entities having similar properties but they aren't same. It's important to note in cryptography their is no such thing like equality **\=** mostly everything is congruence **≅**, e.g. **Equivalent Class**.

## Why Should I Study Modular Arithmetic?

1. It's the foundation of almost every Cryptographic algorithms.
    
2. It is used to enhance the security of cryptographic systems, e.g. *Equivalent Class*.
    
3. Nothing is infinite in cryptography so to make things finite or in range we use Modular Arithmetic, e.g. *Rotation Cipher & Finite Fields*.
    
4. Many cryptography algorithms and even programming languages uses Modular Arithmetic to perform calculations more efficiently.
    
5. You Have To Learn, Their is no way around:
    
    * Security
        
    * Optimization
        
    * Mathematics
        
    * Marks: Actually No
        
    * Grades: Definitely No
        
    * Show off, you know math
        
    * etc, etc, etc
        

## Let's Get The Basics Down

If you're completely new to mathematics (unlike me) then you might be wondering what's the meaning of *congruence* or *modular arithmetic*? So, let's take them one-by-one.

### Modular Arithmetic

#### Arithmetic

Arithmetic is a branch of mathematics that deals with the study of numbers and the basic operations performed on them, such as addition, subtraction, multiplication, and division. Nothing Fancy.

#### Modulo

The modulo operation (often denoted as **"mod"** or **"%"**) is just a remainder you get when one integer is divided by another.

**For example:**

* 7 % 3 equals 1, because when 7 is divided by 3, the remainder is 1.
    

#### So, Modulo + Arithmetic = Modular Arithmetic

Modular arithmetic is a system or mathematical framework or set of rules of arithmetic for integers where numbers **"wrap around"** upon reaching a certain value called the modulus. Ceaser Cipher heavy reply on this concept.

In modular arithmetic, instead of working with all integers, we work with a set of integers from *0 to n−1* (often denoted as *{0, 1, 2, …, n−1}*).

*General Equation of Modular Arithmetic -&gt;* ***a = b (mod n)***, where *b* will always be less than *n* hence meaning of **wrap around** in definition.

> ### e.g. <mark>Caesar Cipher /w a = b (mod n)</mark>
> 
> The Caesar cipher can be represented in modular arithmetic using the following general equation:
> 
> #### *E(x) = (x + k) mod n*
> 
> Where:
> 
> * ***E(x)*** represents the encryption of the plaintext letter *x*.
>     
> * ***k*** is the key or the shift value.
>     
> * ***n*** is the size of the alphabet (for example, 26 for the English alphabet).
>     
> * ***mod*** denotes the modulo operation, which ensures that the result stays within the range of the alphabet.
>     

Modular arithmetic has many applications in various fields including cryptography, computer science, and number theory. It's used extensively in encryption algorithms, hash functions, and in solving problems related to periodic phenomena like cycles and repetitions.

### Congruence

As mentioned earlier, Congruence is a mathematical concept that refers to the equality of two geometric figures or shapes in terms of size and shape. Two figures are considered congruent if they have the same shape and size, meaning that all corresponding angles are equal, and all corresponding sides are equal in length.

But In modular arithmetic, Congruence refers to the concept of two integers having the same remainder when divided by a specified modulus. Two integers `a` and `b` are said to be congruent modulo `m` if they leave the same remainder when divided by `m`.

**For example, in modulo 5 arithmetic:**

* 14 ≅ 4 (mod 5) because both 14 and 4 leave a remainder of 4 when divided by 5.
    
* 23 ≅ 3 (mod 5) because both 23 and 3 leave a remainder of 3 when divided by 5.
    

#### How to solve modular arithmetic equations?

**Modular Arithmetic:** ***a = b (mod n)***

* Lets see for the above examples. In first equation we have: **14 = 4 (mod 5)**.
    
    1. **Divide 14 by 5**: 14 ÷ 5 = 2 with a remainder of 4.
        
    2. **Divide 4 by 5**: 4 ÷ 5 = 0 with a remainder of 4.
        
    
    Since both 14 and 4 leave a remainder of 4 when divided by 5, we can conclude that **14 ≅ 4 (mod 5)**.
    
* Now let's see something which isn't **≅ (congruent)** like: **9 = 2 (mod 4)**.
    
    1. **Divide 9 by 4**: 9 ÷ 4 = 2 with a remainder of 1.
        
    2. **Divide 2 by 4**: 2 ÷ 4 = 0 with a remainder of 2.
        
    
    Since the remainders are different when 9 and 2 are divided by 4, they are not congruent to modulo 4. Therefore, **9 !≅ 2 (mod 4)**.
    

So now by you might have got an idea of **"what is and isn't congruent?"**

#### \= vs ≅

One mistake I deliberately made in Modular Arithmetic section was using **\=** in modular arithmetic. If I ask which equation is correct **14 ≅ 4 (mod 5)** or **14 = 4 (mod 5)**. Answer is both, lets understand it first.

In mathematics, both equations are true but not in cryptography. I mentioned it before in cryptography their is no such thing called **\=** majority is **≅**. And, I did it because by then you didn't know the meaning of congruence. So, From mathematical standpoint equation used in Modular Arithmetic section is true but from cryptography standpoint it's not.

Furthermore, you might be thinking then why did I use **\=** in Congruence section? Here we did know what does it mean to have congruence right? Hmm... Yes, But if you notice I only used **\=** before the solution of equation. How can you tell if a equation is or isn't equal without solving it. So, I started at **\=** like *14 = 4 (mod 5)* and concluded with **≅** like **14 ≅ 4 (mod 5)** after solving the equation.

Other way if you don't want to use **\=** you can go for **?≅**, which is this equation is congruent? I'm not saying, I'm asking.

**For example:**

* *14 ?≅ 4 (mod 5)*, yes *14 ≅ 4 (mod 5)*
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">The real Modular Arithmetic equation in cryptography is: <strong>a ≅ b (mod m)</strong></div>
</div>

## Basic Operations & Their Significance In Modular Arithmetic

### 1\. Modular Addition

In modular arithmetic, addition involves adding two numbers and then taking the remainder when divided by the modulus. Often denoted as **a + b mod m**.

* Let's consider an example: *(7 + 5) mod  3*
    
    * Here, *7 + 5 = 12*, and when divided by *3*, the remainder is *0*. Therefore, *(7 + 5) mod  3 = 0*.
        
* Let's consider one more example but this time in cryptographic way: *(5 + 9) ≅ ? (mod  7)*
    
    1. Add the two numbers: *5 + 9 = 14*.
        
    2. Divide the result by the modulus: *14 ÷ 7 = 2* with a remainder of *0*.
        
    3. Result, **5 + 9 ≅ 0 (mod 7)**.
        
        * This also satisfies our previous statement about congruency: **Two integers** `a` and `b` are said to be congruent modulo `m` if they leave the same remainder when divided by `m`.
            

#### Properties

* **Commutative Property**: a + b ≅ b + a (mod m) This property states that the order of addition doesn't matter in modular arithmetic. The sum of `a` and `b` modulo `m` is the same as the sum of `b` and `a` modulo `m`. This can also be expressed as: *a ≅ b (mod m) ⟹ b ≅ a (mod m)*.
    
* **Associative Property**: (a +b) + c ≅ a + (b + c) (mod m) This property states that the grouping of numbers being added doesn't affect the result in modular arithmetic. The sum of `a`, `b`, and `c` modulo `m` is the same regardless of how the addition is grouped.
    
* **Closed under Addition**: This property states that if you add two numbers within this modulus, the result will still be within that modulus. For example:
    
    * (2 + 3) mod 5 = 0
        
    * (4 + 4) mod  5 = 3
        
    
    In both cases, the result is within the range of 0 to 4, which is the modulus 5.
    

#### Significance

* **Non-linearity**: Modular addition is a non-linear operation, which is important in cryptographic algorithms to prevent various attacks.
    
* **Compatibility with Modular Arithmetic**: Modular addition fits well with modular arithmetic, which is often used in cryptography due to its computational properties and the ability to work with finite sets of numbers.
    

### 2\. Modular Subtraction

In modular arithmetic, subtraction involves subtracting one number from another and then taking the remainder when divided by the modulus. It's often denoted as **(a - b) mod m**.

* Let's consider an example: *(7 - 5) mod  3* Here, *7 - 5 = 2*, and when divided by *3*, the remainder is *2*. Therefore, *(7 - 5) mod  3 = 2*.
    
* Let's delve into a cryptographic example: *(9 - 5) ≅ ? (mod  7)*
    
    1. Subtract the second number from the first: *9 - 5 = 4*.
        
    2. Take the result modulo the modulus: *4 ÷ 7* leaves us with a remainder of *4*.
        
    3. Thus, **9 - 5 ≅ 4 (mod 7)**.
        

#### Properties

* **Non-commutativity**: Subtraction in modular arithmetic is not commutative. In other words, the order of subtraction does matter. For instance, *7 - 5 ≅ 2 (mod 3)*, but *5 - 7 ≅ 1 (mod 3)*.
    
* **Non-associativity**: Similar to non-commutativity, subtraction in modular arithmetic is also non-associative. Grouping matters when subtracting multiple numbers. For example, *(7 - 5) - 1 ≅ 1 (mod 3)*, but *7 - (5 - 1) ≅ 2 (mod 3)*.
    
* **Closed under Subtraction**: Just like addition, subtraction within a modulus maintains closure. For example:
    
    * *(7 - 5) mod 3 = 2*
        
    * *(5 - 7) mod 3 = 1*
        
    
    Both results remain within the range of 0 to 2, which is the modulus 3.
    

#### Significance

* **Non-linearity**: Modular subtraction, akin to modular addition, is a non-linear operation. This property is crucial in cryptographic schemes to thwart various attacks, similar to its additive counterpart.
    
* **Compatibility with Modular Arithmetic**: Subtraction fits seamlessly within modular arithmetic frameworks, making it suitable for cryptographic algorithms where modular arithmetic is prevalent due to its computational efficiency and finite number set operations.
    

### 3\. Modular Multiplication

In modular arithmetic, multiplication involves multiplying two numbers and then taking the remainder when divided by the modulus. It's often denoted as **(a \* b) mod m**.

* Let's illustrate with an example: *(7 5) mod  3 Here, 7 5 = 35*, and when divided by *3*, the remainder is *2*. Therefore, *(7 \* 5) mod  3 = 2*.
    
* Now, let's explore a cryptographic scenario: *(5 \* 9) ≅ ? (mod  7)*
    
    1. Multiply the two numbers: *5 \* 9 = 45*.
        
    2. Divide the result by the modulus: *45 ÷ 7* leaves us with a remainder of *3*.
        
    3. Thus, **5 \* 9 ≅ 3 (mod 7)**.
        

#### Properties

* **Commutative Property**: Multiplication in modular arithmetic follows commutativity. The order of multiplication does not affect the result modulo the modulus. For example, *a b ≅ b a (mod m)*.
    
* **Associative Property**: Similarly, multiplication in modular arithmetic is associative. The grouping of numbers being multiplied does not impact the result modulo the modulus. For example, *(a b) c ≅ a (b c) (mod m)*.
    
* **Closed under Multiplication**: Multiplying two numbers within a modulus maintains closure. For instance:
    
    * *(2 \* 3) mod 5 = 1*
        
    * *(4 \* 4) mod  5 = 1*
        
    
    Both results fall within the range of 0 to 4, which is the modulus 5.
    

#### Significance

* **Non-linearity**: Similar to modular addition and subtraction, modular multiplication is a non-linear operation. This characteristic is essential in cryptographic algorithms to enhance security against various attacks.
    
* **Compatibility with Modular Arithmetic**: Modular multiplication seamlessly integrates with modular arithmetic, which is widely utilized in cryptography due to its computational efficiency and the ability to work with finite sets of numbers.
    

### 4\. Modular Inverse

In modular arithmetic, the modular inverse of a number `a` with respect to a modulus `m` is another number `b` such that *(a \* b) mod m = 1*. It's denoted as *a⁻¹ (mod m)* or *1/a (mod m)*.

* Let's illustrate with an example: Find the modular inverse of *5 modulo 11*. We need to find a number *b* such that *(5 \* b) mod 11 = 1*.
    
    * By trying out various values of *b*, we find that *9* is the modular inverse of *5 modulo 11*, because *(5 \* 9) mod 11 = 45 mod 11 = 1*.
        
* Now, let's explore a cryptographic scenario: Find the modular inverse of *7 modulo 13*. We seek a number *b* such that *(7 \* b) mod 13 = 1*.
    
    * By testing different values, we discover that *2* is the modular inverse of *7 modulo 13*, since *(7 \* 2) mod 13 = 14 mod 13 = 1*.
        

#### Properties

* **Existence**: The modular inverse exists for a number `a` modulo `m` if and only if `a` and `m` are coprime (i.e., their greatest common divisor is 1).
    
* **Uniqueness**: The modular inverse of a number `a` modulo `m` is unique within the range \[0, m-1\].
    
* **Multiplicative Property**: If `b` is the modular inverse of `a` modulo `m`, then the modular inverse of `a` modulo `m` is also the modular inverse of `b` modulo `m`.
    

#### Significance

* **Cryptographic Applications**: Modular inverses play a crucial role in cryptographic algorithms, such as **RSA encryption**, where they are used to calculate the private key from the public key.
    
* **Efficient Computations**: Modular inverses are essential for various mathematical computations, particularly in modular arithmetic-based algorithms, due to their ability to efficiently compute divisions within a finite field.
    

### 5\. Modular Exponentiation

In modular arithmetic, modular exponentiation involves raising a base to an exponent and then taking the remainder when divided by a modulus. It's denoted as **(a^b) mod m**.

* Let's illustrate with an example: Find *(2 ^ 5) mod 7*. Here, *2 ^ 5 = 32*, and when divided by *7*, the remainder is *4*. Therefore, *(2 ^ 5) mod 7 = 4*.
    
* Now, let's explore a cryptographic scenario: Compute *(3 ^ 10) ≅ ? (mod  13)*.
    
    1. Calculate *3 ^ 10*: *3 ^ 10 = 59049*.
        
    2. Divide the result by the modulus: *59049 ÷ 13* leaves us with a remainder of *3*.
        
    3. Thus, **3 ^ 10 ≅ 3 (mod 13)**.
        

#### Properties

* **Efficient Computation**: Modular exponentiation can be efficiently computed using algorithms like exponentiation by squaring or the repeated squaring method, particularly useful for large exponents.
    
* **Distributive Property**: Modular exponentiation follows distributive over multiplication. That is, *(a ^ b \* a ^ c) mod m = (a ^ (b + c)) mod m*.
    
* **Closed under Exponentiation**: Raising a number to a power within a modulus maintains closure. For example:
    
    * *(2 ^ 3) mod 5 = 3*
        
    * *(4 ^ 2) mod  5 = 1*
        
    
    Both results fall within the range of 0 to 4, which is the modulus 5.
    

#### Significance

* **Cryptographic Protocols**: Modular exponentiation is extensively used in cryptographic protocols, such as **RSA encryption and Diffie-Hellman key exchange**, for secure communication and data encryption.
    
* **Efficient Key Generation**: Modular exponentiation plays a crucial role in generating and manipulating **cryptographic keys efficiently**, contributing to the security and scalability of cryptographic systems.
    

> ### Anyone, from the most clueless amateur to the best cryptographer, can create an algorithm that he himself can't break.
> 
> \- Bruce Schneier