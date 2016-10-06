---
layout: post
title: "Essence of Effective Java"
Date: 2016-10-06
---

#### [Item 1: Consider static factory methods instead of Constructors]

Advantages:
1. We can use proper names for the methods rather than use a constructor. This will greatly enhance readability.
2. They need not create a new object every time they are invoked unlike a constructor.
3. We can return the instances from the static method and thus maybe even cache them and control the number of instances that 
are produced. e.g Flyweight Design pattern 
4. They reduce the verbosity of creating parametrized type instances
5. Common Names of static methods can be valueOf, Of,getInstance(), newInstance(), getType(),newType() etc. 

#### [Item 2: Consider a builder when faced with many constructor parameters]

```
public class NutritionFacts {
private final int servingSize;
private final int servings;
private final int calories;

public static class Builder {
// Required parameters
private final int servingSize;
private final int servings;
// Optional
private int calories =0;
private int fat=0;
private int carbohydrate=0;

public Builder(int servingSize, int servings) {
this.servingSize = servingSize;
this.servings = servings;
}
public Builder calories(int val)
{ calories = val;
return this;}
public NutritionFacts build() {
return new NutritionFacts(this);
}
}
private NutritionFacts(Builder builder) {
servingSize = builder.servingSize;
servings = builder.servings;
calories = builder.calories;
fat = builder.fat;
carbohydrate = builder.carbohydrate;
}
}
```

#### [Item 3: Enforce the singleton property with a private constructor or an enum type]

1. Make an enum with one element
```
   public enum ELVIS {
   INSTANCE;
    public void leaveTheBuilding(){
    }
   }
```
This mechanism provides the serialization aspect for free. No need for the readResolve method.Ensures that a singleton is 
created in JVM. 

#### [Item 4: Enforce noninstantiability with a private constructor]

1. Utility Classes are meant to be non instantiable. Ex. Arrays, Collections etc .The easiest way to make it non instantiable is
to make a private Constructor and throw an AssertionError if the constructor is called. 

```
    public class UtilityClass {
        private UtilityClass() {
            throw new AssertionError();
        }
    }
```

#### [Item 5: Avoid creating unnecessary objects]

1. Do not perform Calendar.getInstance in a method as its expensive . 
2. No need to create a String using new String().
3. prefer primitives to Boxed Primitives 

#### [Item 6: Eliminate obsolete object references]

1. An obsolete reference is a reference that is never dereferenced once its created. 
E.x Popping from a stack

```

  public Object pop() {
    if (size == 0)
    throw new EmptyStackException();
    Object result = elements[--size];
    elements[size] = null; // Eliminate obsolete reference
    return result;
  }
```
2. The safest way to eliminate obsolete references is by defining variables in the naroowest scope. 
3. Whenever a class is maintaining its own pool, be wary of memory leaks. 
4. From the book 
```
Another common source of memory leaks is caches. Once you put an object reference into a cache, it’s easy to forget that 
it’s there and leave it in the cache long after it becomes irrelevant. There are several solutions to this problem. If you’re 
lucky enough to implement a cache for which an entry is relevant exactly so long as there are references to its key 
outside of the cache, represent the cache as a WeakHashMap ; entries will be removed automatically after they become obsolete. 
Remember that WeakHashMap is useful only if the desired lifetime of cache entries is determined by external references to
the key, not the value. More commonly, the useful lifetime of a cache entry is less well defined, with entries becoming 
less valuable over time. Under these circumstances, the cache should occasionally be cleansed of entries that have fallen
into disuse. This can be done by a background thread (perhaps a Timer or ScheduledThreadPoolExecutor ) or as a side effect 
of adding new entries to the cache. The LinkedHashMap class facilitates the latter approach with its removeEldestEntry
method. For more sophisticated caches, you may need to use java.lang.ref directly.
```
5. Listeners and callbacks should be explicitly deregistered, else they will keep accumulating in the heap. Hence its safe to save
them as a weakReference HashMap. these accumulated objects can be viewed using a Heap Profiler. 

#### [Item 7: Avoid Finalizers]

1. No guarantee that finalizers will be executed. Hence never perform any time critical operations in a finalizer.
2. Provide explicit Termination like File Operations , sql Connections or graphics. 
3. Finalizers are safety nets for native peer objects. But even when we are handing off a call to the native method, it better to
ensure sxplicit termination. 

