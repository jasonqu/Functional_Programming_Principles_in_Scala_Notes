Lecture 2.5 - Functions and Data

定义类
class Rational(x: Int, y: Int) {
  def numer = x
  def denom = y
}

Lecture 2.6 - More Fun With Rationals

class Rational(x: Int, y: Int) {
  private def gcd(a: Int, b: Int): Int = if (b == 0) a else gcd(b, a % b)
  val numer = x / gcd(x, y)
  val denom = y / gcd(x, y)
}


Clients observe exactly the same behavior in each case.

This ability to choose different implementations of the data without
affecting clients is called data abstraction.

It is a cornerstone of software engineering.


Preconditions

require(y > 0, ”denominator must be positive”)
assert(x >= 0)
AssertionError for assert, IllegalArgumentException for require.

Constructors

In Scala, a class implicitly introduces a constructor. This one is
called the primary constructor of the class.

The primary constructor

* takes the parameters of the class
* and executes all statements in the class body (such as the
require a couple of slides back).


Auxiliary Constructors

class Rational(x: Int, y: Int) {
  def this(x: Int) = this(x, 1)
  ...
}

Lecture 2.7 - Evaluation and Operators

Classes and Substitutions  omit

Precedence Rules 操作符优先级

(all letters)
|
^
&
< >
= !
:
+ -
* / %
(all other special characters)




Lecture 3.1 - Class Hierarchies

a binary tree 

abstract class IntSet {
  def incl(x: Int): IntSet
  def contains(x: Int): Boolean
}

class Empty extends IntSet {
  def contains(x: Int): Boolean = false
  def incl(x: Int): IntSet = new NonEmpty(x, new Empty, new Empty)
}

class NonEmpty(elem: Int, left: IntSet, right: IntSet) extends IntSet {
  def contains(x: Int): Boolean =
    if (x < elem) left contains x
    else if (x > elem) right contains x
    else true

  def incl(x: Int): IntSet =
    if (x < elem) new NonEmpty(elem, left incl x, right)
    else if (x > elem) new NonEmpty(elem, left, right incl x)
    else this
}










Lecture 3.2 - How Classes Are Organized

Lecture 3.3 - Polymorphism