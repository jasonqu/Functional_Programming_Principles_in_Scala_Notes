Lecture 4.7 - Lists

练习 插入排序
def isort(xs: List[Int]): List[Int] = xs match {
  case List() => List()
  case y :: ys => insert(y, isort(ys))
} 

def insert(x:Int, xs: List[Int]): List[Int] = xs match {
  case Nil => x :: Nil
  case y :: ys => if(x < y) x :: xs else y :: insert(x, ys)
}  



Lecture 5.1 - More Functions on Lists
   
last
init
concat
reverse
removeAt
def removeAt[T](x:Int, xs: List[T]): List[T] = (x,xs) match {
  case (x,Nil) => throw new RuntimeException()
  case (0, xs) => xs.tail
  case (x, xs) => xs.head :: removeAt(x - 1, xs.tail)
}

flatten
  def flatten(ls: List[Any]): List[Any] = ls flatMap {
    case ms: List[_] => flatten(ms)
    case e => List(e)
  }


Lecture 5.2 - Pairs and Tuples

  def msort(xs: List[Int]): List[Int] = {
    val n = xs.length / 2
    if (n == 0) xs
    else {
      def merge(xs: List[Int], ys: List[Int]) : List[Int]= (xs, ys) match {
        case (Nil, ys) => ys
        case (xs, Nil) => xs
        case (x :: xxs, y :: yys) =>
          if(x < y) x :: merge(xxs, ys)
          else y :: merge(xs, yys)
      }
      val (fst, snd) = xs splitAt n
      merge(msort(fst), msort(snd))
    }
  }
 
Lecture 5.3 - Implicit Parameters

Making Sort more General

Problem: How to parameterize msort so that it can also be used for
lists with elements other than Int?

def msort[T](xs: List[T]): List[T] = ...

does not work, because the comparison < in merge is not defined for
arbitrary types T.

Idea: Parameterize merge with the necessary comparison function.














Lecture 5.4 - Higher-Order List Functions       

略 但是很重要










Lecture 5.5 - Reduction of Lists

def reverse[a](xs: List[T]):List[T] =
  (xs foldleft list[T]()) ((xs, x) => x :: xs) 

def mapFun[T, U](xs: List[T], f:T=>U): List[U] =
  (xs foldRight list[U]()) (...) 

def lengthFun[T](xs: List[T]):Int =
  (xs foldRight 0) (...) 











Lecture 5.6 - Reasoning About Concat            

Lecture 5.7 - A Larger Equational Proof on Lists