---
layout: post
title: "Essence of Effective Java"
Date: 2016-10-06
---

## Creating and Destroying Objects.

#### [Item 1: Consider static factory methods instead of Constructors]

Advantages:

1. We can use proper names for the methods rather than use a constructor. This will greatly enhance readability.
2. They need not create a new object every time they are invoked unlike a constructor.
3. We can return the instances from the static method and thus maybe even cache them and control the number of instances that are produced. e.g Flyweight Design pattern 
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
Another common source of memory leaks is caches. Once you put an object reference into a cache, 
it’s easy to forget that it’s there and leave it in the cache long after it becomes irrelevant. 
There are several solutions to this problem. If you’re lucky enough to implement a cache for 
which an entry is relevant exactly so long as there are references to its key outside of the 
cache, represent the cache as a WeakHashMap ; entries will be removed automatically after they 
become obsolete. Remember that WeakHashMap is useful only if the desired lifetime of cache entries
is determined by external references to the key, not the value. More commonly, the useful 
lifetime of a cache entry is less well defined, with entries becoming  less valuable over time.
Under these circumstances, the cache should occasionally be cleansed of entries that have fallen
into disuse. This can be done by a background thread (perhaps a Timer or ScheduledThreadPoolExecutor ) 
or as a side effect of adding new entries to the cache. The LinkedHashMap class facilitates the 
latter approach with its removeEldestEntry method. For more sophisticated caches, you may need 
to use java. lang. ref directly.
```

5. Listeners and callbacks should be explicitly deregistered, else they will keep accumulating in the heap. Hence its safe to save them as a weakReference HashMap. these accumulated objects can be viewed using a Heap Profiler. 

#### [Item 7: Avoid Finalizers]

1. No guarantee that finalizers will be executed. Hence never perform any time critical operations in a finalizer.
2. Provide explicit Termination like File Operations , sql Connections or graphics. 
3. Finalizers are safety nets for native peer objects. But even when we are handing off a call to the native method, it better to ensure explicit termination. 

## Methods Common to all Objects

#### [Item 8:Obey the general contract when overriding equals]

Easiest way is to avoid overriding the equals method in the following scenarios. 

1. Each instance of the class is inherently unique. ex. Thread class equals follows that of Object class
2. Sometimes we may not care for the implementation of equals method. ex Random Class
3. If a super class has already overridden the euqals method, the behavior may hold good for the inherting class
4. If the class is private or package private and we are certain the equals method will never be invoked. 
5. From the book 

```
 The equals method implements an equivalence relation. It is:
   • Reflexive: For any non-null reference value x , x.equals(x) must return true .
   • Symmetric: For any non-null reference values x and y , x.equals(y) must return true if and only if y.equals(x) returns  true .  
   • Transitive: For any non-null reference values x , y , z , if x.equals(y) returns true and y.equals(z) rweturns true , then x.equals(z) must return true .
   • Consistent: For any non-null reference values x and y , multiple invocations of x.equals(y) consistently return true or consistently return false , provided no information used in equals comparisons on the objects is modified.
   • For any non-null reference value x , x.equals(null) must return false .
```

6. The equals method becomes important when we are adding custom classes to a List or a Map . The list.contains method has to decide based on the contract if the objects are equal or not. 
7. The Liskov substitution principle says that any important property of a type should also hold for its subtypes, so that any method written for the type should work equally well on its subtypes.
8. From the Book 

```
There are some classes in the Java platform libraries that do extend an instantiable class and add a value component. 
For example, java.sql.Timestamp extends java.util.Date and adds a nanoseconds field. 
The equals implementation for Timestamp does violate symmetry and can cause erratic behavior if Timestamp and Date objects are used in the same collection or are otherwise intermixed. 
The Timestamp class has a disclaimer cautioning programmers against mixing dates and timestamps. 
While you won’t get into trouble as long as you keep them separate, there’s nothing to prevent you from mixing them, and the resulting errors can be hard to debug. 
This behavior of the Timestamp class was a mistake and should not be emulated.
```

9. Best ways to write the equals method. 
    1. Use the == operator to check if the argument is a reference to this object. If so, return true
    2. Use the instanceof operator to check if the argument has the correct type. (Check Collection interfaces for e.g)
    3. Cast the argument to the correct type. 
    4. For each “significant” field in the class, check if that field of the argument matches the corresponding field of this   object.
    
10. When you are finished writing your equals method, ask yourself three questions: Is it symmetric? Is it transitive? Is it consistent?

#### [Item 9: Always override hashcode when you override equals.]

1. From the Book [The HashCode contract]

```
Whenever it is invoked on the same object more than once during an execution of an application, 
the hashCode method must consistently return the same integer, provided no information used in equals 
comparisons on the object is modified. This integer need not remain consistent from one execution of an application
to another execution of the same application. 
If two objects are equal according to the equals(Object) method, then calling the hashCode method on each of 
the two objects must produce the same integer result.
It is not required that if two objects are unequal according to the equals(Object) method, then calling
the hashCode method on each of the two objects must produce distinct integer results. 
However, the programmer should be aware that producing distinct integer results for unequal objects may improve the performance of hash tables.
```

2. Recipe for hashCode

```
Store some constant nonzero value, say, 17, in an int variable called result .
a. Compute an int hash code c for the field:
      i. If the field is a boolean , compute (f ? 1 : 0) .
      ii. If the field is a byte , char , short , or int , compute (int) f .
      iii. If the field is a long , compute (int) (f ^ (f >>> 32)) .
      iv. If the field is a float , compute Float.floatToIntBits(f) .
      v. If the field is a double , compute Double.doubleToLongBits(f) , and then hash the resulting long as in step 2.a.iii.
      vi. If the field is an object reference and this class’s equals method compares the field by recursively invoking equals , recursively invoke hashCode on the field. If a more complex comparison is required, compute a “canonical representation” for this field and invoke hashCode on the canonical representation. If the value of the field is null , return 0 (or some other constant, but 0 is traditional).
      vii. If the field is an array, treat it as if each element were a separate field. That is, compute a hash code for each significant element by applying these rules recursively, and combine these values per step 2.b. If every element in an array field is significant, you can use one of the Arrays.hashCode methods added in release 1.5.
b. Combine the hash code c computed in step 2.a into result as follows:

result = 31 * result + c;

c. Return result .
When you are finished writing the hashCode method, ask yourself whether equal instances have equal hash codes. Write unit tests to verify your intuition!
```

#### [Item 10: Always override toString]

1. Make your class more pleasant to use 
2. Provide programmatic access to all of the information contained in the value returned by toString .

#### [Item 11: Override Clone judiciously] [TODO]

#### [Item 12: Consider implementing Comparable]

1. By implementing Comparable , a class indicates that its instances have a natural ordering.
2. Compares this object with the specified object for order. Returns a negative integer, zero, or a positive integer as this object is less than, equal to, or greater than the specified object. Throws ClassCastException if the specified object’s type prevents it from being compared to this object.
3. If you want to add a value component to a class that implements Comparable , don’t extend it; write an unrelated class containing an instance of the first class. Then provide a “view” method that returns this instance. This frees you to implement whatever compareTo method you like on the second class, while allowing its client to view an instance of the second class as an instance of the first class when needed.
4. A caveat in BigDecimal For example, consider the BigDecimal class, whose compareTo method is inconsistent with equals . If you create a HashSet instance and add new BigDecimal("1.0") and new BigDecimal("1.00") , the set will contain two elements because the two BigDecimal instances added to the set are unequal when compared using the equals method. If, however, you perform the same procedure using a TreeSet instead of a HashSet , the set will contain only one element because the two BigDecimal instances are equal when compared using the compareTo method. (See the BigDecimal documentation for details.)
