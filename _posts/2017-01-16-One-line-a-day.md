---
layout: post
title: "Learn something new everyday"
date: 2017-01-16
---

# 2017-01-16
***
* Generic types are not covariant. Arrays are covariant.
* 2 best practices while implementing Generics

```java
// Capture Helper Idiom 
public void rebox(Box<?> box) {
    reboxHelper(box);
}
 
private<V> void reboxHelper(Box<V> box) {
    box.put(box.get());
}
```

```java
// Generic Factory method to enfore DRY
public class BoxImpl<T> implements Box<T> {
 
    public static<V> Box<V> make() {
        return new BoxImpl<V>();
    }
 
    ...
}
...
Box<String> myBox = BoxImpl.make();
```
> -- [Brian Goetz .. Going wild with Generics](https://www.ibm.com/developerworks/library/j-jtp04298/index.html)

# 2017-01-17
***
* Maintaining an ownership hierarchy, where each resource owns the resources it acquires and is responsible for releasing them, helps keep the mess from getting out of control.

* For any object that may directly or indirectly own an object that requires explicit release, you must provide lifecycle methods -- close(), release(), destroy(), and the like -- to ensure reliable cleanup.

 -- [Brian Goetz .. Good Housekeeping Practices](https://www.ibm.com/developerworks/java/library/j-jtp03216/index.html?ca=drs-)

* Generating tests dynamically in python. Config driven unit tests.

 -- [Dynamically generating Python test cases](https://eli.thegreenplace.net/2014/04/02/dynamically-generating-python-test-cases)

 # 2017-01-21
 ***
 * A persistent gambler who raises his bet to a fixed fraction of bankroll when he wins, but does not reduce it when he loses, will eventually and inevitably go broke, even if he has a positive expected value on each bet.

  -- [Random walk](https://en.wikipedia.org/wiki/Random_walk)
  -- [Gambler's ruin](https://en.wikipedia.org/wiki/Gambler%27s_ruin)
