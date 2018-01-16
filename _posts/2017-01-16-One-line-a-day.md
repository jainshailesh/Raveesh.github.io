---
layout: post
title: "Learn something new everyday"
date: 2017-01-16
---

# 2017-01-16
***
> Generic types are not covariant. Arrays are covariant; 2 best practices while implementing Generics

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
