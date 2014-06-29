Week 6: Collections

Lecture 6.1 - Other Collections

Vector : an alternative sequence implementation

val nums = Vector(1, 2, 3, -88)
val people = Vector(”Bob”, ”James”, ”Peter”)

x +: xs Create a new vector with leading element x, followed
        by all elements of xs.
xs :+ x Create a new vector with trailing element x, preceded
        by all elements of xs.
(Note that the : always points to the sequence.)

Collection Hierarchy

A common base class of List and Vector is Seq, the class of all
sequences.

Seq itself is a subclass of Iterable.

Iterable, Seq Array List Vector Range String, Map, Set

Arrays and Strings

Arrays and Strings support the same operations as Seq and can
implicitly be converted to sequences where needed.

(They cannot be subclasses of Seq because they come from Java)

val xs: Array[Int] = Array(1, 2, 3)
xs map (x => 2 * x)
val ys: String = ”Hello world!”
ys filter (_.isUpper)


Example: Combinations

To list all combinations of numbers x and y where x is drawn from
1..M and y is drawn from 1..N:

(1 to M) flatMap (x => (1 to N) map (y => (x, y)))

Example: Scalar Product

To compute the scalar product of two vectors:

  def scalarProduct(xs: Vector[Double], ys: Vector[Double]): Double =
    (xs zip ys).map(xy => xy._1 * xy._2).sum

An alternative way to write this is with a pattern matching function
value.

  def scalarProduct(xs: Vector[Double], ys: Vector[Double]): Double =
    (xs zip ys).map{ case (x, y) => x * y }.sum


Exercise:

A number n is prime if the only divisors of n are 1 and n itself.
What is a high-level way to write a test for primality of numbers?
For once, value conciseness over efficiency.

def isPrime(n: Int): Boolean = ???




def isPrime(n: Int): Boolean = 2 until n forall (d => n % d != 0)







Lecture 6.2 - Combinatorial Search and For-Expressions

important














Lecture 6.3 - Combinatorial Search Example

Set

Example: N-Queens

Lecture 6.4 - Queries with For

Queries with for

The for notation is essentially equivalent to the common operations
of query languages for databases.

Example: Suppose that we have a database books, represented as a
list of books.

case class Book(title: String, authors: List[String])



A Mini-Database

val books: List[Book] = List(
  Book(title = ”Structure and Interpretation of Computer Programs”,
    authors = List(”Abelson, Harald”, ”Sussman, Gerald J.”)),
  Book(title = ”Introduction to Functional Programming”,
    authors = List(”Bird, Richard”, ”Wadler, Phil”)),
  Book(title = ”Effective Java”,
    authors = List(”Bloch, Joshua”)),
  Book(title = ”Java Puzzlers”,
    authors = List(”Bloch, Joshua”, ”Gafter, Neal”)),
  Book(title = ”Programming in Scala”,
    authors = List(”Odersky, Martin”, ”Spoon, Lex”, ”Venners, Bill”)))



Some Queries

To find the titles of books whose author’s name is “Bird”:

for (b <- books; a <- b.authors if a startsWith ”Bird,”)
  yield b.title

To find all the books which have the word “Program” in the title:

for (b <- books if b.title indexOf ”Program” >= 0)
  yield b.title



Another Query

To find the names of all authors who have written at least two
books present in the database.

for {
  b1 <- books
  b2 <- books
  if b1 != b2     // b1.title < b2.title
  a1 <- b1.authors
  a2 <- b2.authors
  if a1 == a2
} yield a1

在成set
distinct, val bookSet = books.toSet














Lecture 6.5 - Translation of For


For-Expressions and Higher-Order Functions

The syntax of for is closely related to the higher-order functions
map, flatMap and filter.

First of all, these functions can all be defined in terms of for:

def mapFun[T, U](xs: List[T], f: T => U): List[U] =
  for (x <- xs) yield f(x)
def flatMap[T, U](xs: List[T], f: T => Iterable[U]): List[U] =
  for (x <- xs; y <- f(x)) yield y
def filter[T](xs: List[T], p: T => Boolean): List[T] =
  for (x <- xs if p(x)) yield x



Translation of For (1)

In reality, the Scala compiler expresses for-expressions in terms of
map, flatMap and a lazy variant of filter.

Here is the translation scheme used by the compiler (we limit
ourselves here to simple variables in generators)

1: A simple for-expression

for (x <- e1) yield e2

is translated to

e1.map(x => e2)



2: A for-expression

for (x <- e1 if f; s) yield e2

where f is a filter and s is a (potentially empty) sequence of
generators and filters, is translated to

for (x <- e1.withFilter(x => f); s) yield e2

(and the translation continues with the new expression)
You can think of withFilter as a variant of filter that does not
produce an intermediate list, but instead filters the following map or
flatMap function application.



3: A for-expression

for (x <- e1; y <- e2; s) yield e3

where s is a (potentially empty) sequence of generators and filters,
is translated into

e1.flatMap(x => for (y <- e2; s) yield e3)

(and the translation continues with the new expression)



Example

Take the for-expression that computed pairs whose sum is prime:

for {
  i <- 1 until n
  j <- 1 until i
  if isPrime(i + j)
} yield (i, j)

Applying the translation scheme to this expression gives:

(1 until n).flatMap(i =>
  (1 until i).withFilter(j => isPrime(i+j))
    .map(j => (i, j)))
    
This is almost exactly the expression which we came up with first!



Exercise

Translate

for (b <- books; a <- b.authors if a startsWith ”Bird”)
  yield b.title

into higher-order functions.



Generalization of for

Interestingly, the translation of for is not limited to lists or
sequences, or even collections;

It is based solely on the presence of the methods map, flatMap and
withFilter.

This lets you use the for syntax for your own types as well – you
must only define map, flatMap and withFilter for these types.

There are many types for which this is useful: arrays, iterators,
databases, XML data, optional values, parsers, etc.



For and Databases

For example, books might not be a list, but a database stored on
some server.

As long as the client interface to the database defines the methods
map, flatMap and withFilter, we can use the for syntax for querying
the database.

This is the basis of the Scala data base connection frameworks
ScalaQuery and Slick.

Similar ideas underly Microsoft’s LINQ.











Lecture 6.6 - Maps


trait Option[+A]
case class Some[+A](value: A) extends Option[A]
object None extends Option[Nothing]



Default Values

So far, maps were partial functions: Applying a map to a key value
in map(key) could lead to an exception, if the key was not stored in
the map.

There is an operation withDefaultValue that turns a map into a
total function:

val cap1 = capitalOfCountry withDefaultValue ”<unknown>”
cap1(”Andorra”) // ”<unknown>”



Final Implementation of Polynom











Lecture 6.7 - Putting the Pieces Together

Week 7: Lazy Evaluation

Lecture 7.1 - Structural Induction on Trees

Lecture 7.2 - Streams


Delayed Evaluation

However, we can make the short-code efficient by using a trick:

Avoid computing the tail of a sequence until it is needed
for the evaluation result (which might be never)

This idea is implemented in a new class, the Stream.

Streams are similar to lists, but their tail is evaluated only on
demand.


...


Implementation of Streams

The implementation of streams is quite close to the one of lists.
Here’s the trait Stream:

trait Stream[+A] extends Seq[A] {
  def isEmpty: Boolean
  def head: A
  def tail: Stream[A]
  ...
}

As for lists, all other methods can be defined in terms of these three.

Concrete implementations of streams are defined in the Stream
companion object. Here’s a first draft:

object Stream {
  def cons[T](hd: T, tl: => Stream[T]) = new Stream[T] {
    def isEmpty = false
    def head = hd
    def tail = tl
  }

  val empty = new Stream[Nothing] {
    def isEmpty = true
    def head = throw new NoSuchElementException(”empty.head”)
    def tail = throw new NoSuchElementException(”empty.tail”)
  }
}


Difference to List

The only important difference between the implementations of List
and Stream concern tl, the second parameter of Stream.cons.

For streams, this is a by-name parameter.

That’s why the second argument to Stream.cons is not evaluated at
the point of call.

Instead, it will be evaluated each time someone calls tail on a
Stream object.






Lecture 7.3 - Lazy Evaluation


Lazy Vals and Streams

Using a lazy value for tail, Stream.cons can be implemented more
efficiently:

def cons[T](hd: T, tl: => Stream[T]) = new Stream[T] {
  def head = hd
  lazy val tail = tl
  ...
}



Lecture 7.4 - Computing with Infinite Sequences

important

The stream of all natural numbers:

def from(n: Int): Stream[Int] = n #:: from(n+1)



The Sieve of Eratosthenes in Code

Here’s a function that implements this principle:

def sieve(s: Stream[Int]): Stream[Int] =
  s.head #:: sieve(s.tail filter (_ % s.head != 0))
val primes = sieve(from(2))

To see the list of the first N prime numbers, you can write

(primes take N).toList



Lecture 7.5 - Case Study: the Water Pouring Problem

Lecture 7.6 - Course Conclusion

Traits of Functional Programming

Functional programming provides a coherent set of notations and
methods based on

* higher-order functions,
* case classes and pattern matching,
* immutable collections,
* absence of mutable state,
* flexible evaluation strategies: strict/lazy/by name.

A useful toolkit for every programmer.

A different way of thinking about programs.





More Material on Scala

Reference material:

Scala Ref Card (adapted from a forum post by Laurent Poulain)
https://class.coursera.org/progfun-004/wiki/CheatSheet

Twitter’s Scala School
Programming in Scala
Scala Tour
http://docs.scala-lang.org/tutorials/tour/tour-of-scala.html

To stay current:

Scala Meetups
Typesafe Blog and Newsletter
This Week in Scala Blogs
http://www.cakesolutions.net/teamblogs



What Remains to Be Covered

Worthwhile topics we did not cover in this course:

Functional programming and state
* what does it mean to have mutable state?
* what changes if we add it?

Parallelism
* how to exploit immutability for parallel execution.

Domain-Specific Languages
* high-level libraries as embedded DSLs.
* interpretation techniques for external DSLs.














