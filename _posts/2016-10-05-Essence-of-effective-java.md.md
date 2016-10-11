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

## [Classes and Interfaces] 

#### [Item 13: Minimize the accessibility of classes and members]

1. A well-designed module hides all of its implementation details, cleanly separating its API from its implementation. Modules then communicate only through their APIs and are oblivious to each others’ inner workings. This concept, known as information hiding or encapsulation, is one of the fundamental tenets of software design
2. information hiding decreases the risk in building large systems, because individual modules may prove successful even if the system does not.
3. Instance fields should never be public
4. classes with public mutable fields are not thread-safe.
5. You can expose constants via public static final fields, assuming the constants form an integral part of the abstraction provided by the class. By convention, such fields have names consisting of capital letters, with words separated by underscores It is critical that these fields contain either primitive values or references to immutable objects (Item 15). A final field containing a reference to a mutable object has all the disadvantages of a nonfinal field.

#### [Item 14: In public classes, use accessor methods, not public fields]

1. Classic Example of encapsulation

```
class Point {
private double x;
private double y;
public Point(double x, double y) {
this.x = x;
this.y = y;
}
public double getX() { return x; }
public double getY() { return y; }
public void setX(double x) { this.x = x; }
public void setY(double y) { this.y = y; }
}
```

#### [Item 15: Minimize mutability]

1. To make a class immutable follow these Rules: 
      1. Don’t provide any methods that modify the object’s state (known as mutators).
      2. Ensure that the class can’t be extended. e.g by making it final 
      3. Make all fields final 
      4. Make all fields private
      5. Ensure exclusive access to any mutable components by making defensive copies in constructor, accesor, read methods etc
2. Immutability is enhanced using functional approach because methods return the result of applying a function to their operand without modifying it.
3. An immutable object can be in only one state and that is the state in which it was created. 
4. Immutable objects are inherently thread-safe; they require no synchronization.
5. The alternative to making an immutable class final is to make all of its constructors private or package-private, and to add public static factories in place of the public constructors
6. resist the urge to write a set method for every get method. Classes should be immutable unless there’s a very good reason to make them mutable.

#### [Item 16: Favor composition over inheritance]

1. Unlike method invocation, inheritance violates encapsulation
2. Any class that is extending a super class has to get updated whenever there is a new implementation in the superclass. Moreover, it may hppen that once the super class is modified any moethod that is overridden in the child class may cause a compilation failure.

#### [Item 17: Design and document for inheritance or else prohibit it]

1. For each public or protected method or constructor, the documentation must indicate which overridable methods the method or constructor invokes, in what sequence, and how the results of each invocation affect subsequent processing. (By overridable, we mean nonfinal and either public or protected.)
2. By convention, a method that invokes overridable methods contains a description of these invocations at the end of its documentation comment. The description begins with the phrase “This implementation.”
3. To allow programmers to write efficient subclasses without undue pain, a class may have to provide hooks into its internal workings in the form of judiciously chosen protected methods or, in rare instances, protected fields
4. The only way to test a class designed for inheritance is to write subclasses.If you omit a crucial protected member, trying to write a subclass will make the omission painfully obvious.
5. Constructors must not invoke overridable methods, directly or indirectly. If you violate this rule, program failure will result. The superclass constructor runs before the subclass constructor, so the overriding method in the subclass will get invoked before the subclass constructor has run. If the overriding method depends on any initialization performed by the subclass constructor, the method will not behave as expected

#### [Item 18: Prefer interfaces to abstract classes]

1. Existing classes can be easily retrofitted to implement a new interface.
2. Interfaces are ideal for defining mixins. Loosely speaking, a mixin is a type that a class can implement in addition to its “primary type” to declare that it provides some optional behavior. For example, Comparable is a mixin interface that allows a class to declare that its instances are ordered with respect to other mutually comparable objects.
3. Interfaces allow the construction of nonhierarchical type frameworks.
4. Interfaces enable safe, powerful functionality enhancements via the wrap-per class idiom
5. You can combine the virtues of interfaces and abstract classes by providing an abstract skeletal implementation class to go with each nontrivial interface that you export.

#### [Item 19: Use interfaces only to define types]

1. The constant interface pattern is a poor use of interfaces. That a class uses some constants internally is an implementation detail. Implementing a constant interface causes this implementation detail to leak into the class’s exported API.
2. In summary, interfaces should be used only to define types. They should not be used to export constants.

#### [Item 20: Prefer class hierarchies to tagged classes]

1. From the Book 

```
Such tagged classes have numerous shortcomings. They are cluttered with boilerplate, including enum declarations, tag fields, and switch statements. Readability is further harmed because multiple implementations are jumbled together in a single class. Memory footprint is increased because instances are burdened with irrelevant fields belonging to other flavors. Fields can’t be made final unless constructors initialize irrelevant fields, resulting in more boilerplate. Constructors must set the tag field and initialize the right data fields with no help from the compiler: if you initialize the wrong fields, the program will fail at runtime. You can’t add a flavor to a tagged class unless you can modify its source file. If you do add a flavor, you must remember to add a case to every switch statement, or the class will fail at runtime. Finally, the data type of an instance gives no clue as to its flavor. In short, tagged classes are verbose, error-prone, and inefficient.
```

2. If you’re tempted to write a class with an explicit tag field, think about whether the tag could be eliminated and the class replaced by a hierarchy. When you encounter an existing class with a tag field, consider refactoring it into a hierarchy.

#### [Item 21: Use function objects to represent strategies]

1. a primary use of function pointers is to implement the Strategy pattern. To implement this pattern in Java, declare an interface to represent the strategy, and a class that implements this interface for each concrete strategy.When a concrete strategy is used only once, it is typically declared and instantiated as an anonymous class. When a concrete strategy is designed for repeated use, it is generally implemented as a private static member class and exported in a public static final field whose type is the strategy interface.

#### [Item 22: Favor static member classes over nonstatic]

1. A nested class is a class defined within another class. A nested class should exist only to serve its enclosing class. If a nested class would be useful in some other context, then it should be a top-level class. There are four kinds of nested classes: static member classes, nonstatic member classes, anonymous classes, and local classes.
2. One common use of a nonstatic member class is to define an Adapter that allows an instance of the outer class to be viewed as an instance of some unrelated class. For example, implementations of the Map interface typically use nonstatic member classes to implement their collection views, which are returned by Map ’s keySet , entrySet , and values methods.

## [Generics]

#### [Item 23: Don’t use raw types in new code]

1. If you use raw types, you lose all the safety and expressiveness benefits of generics. 
2. You lose type safety if you use a raw type like List , but not if you use a parameterized type like List &lt;Object&gt;3
3. While you can pass a List&lt;String&gt; to a parameter of type List , you can’t pass it to a parameter of type List&lt;Object&gt;.There are subtyping rules for generics, and List&lt;String&gt; is a subtype of the raw type List , but not of the parameterized type List&lt;Object&gt;
4. you can’t put any element (other than null ) into a Collection<?>

#### [Item 24: Eliminate unchecked warnings]

1. Eliminate every unchecked warning that you can.
2. Every time you use an @SuppressWarnings("unchecked") annotation, add a comment saying why it’s safe to do so. This will help others understand the code, and more importantly, it will decrease the odds that someone will modify the code so as to make the computation unsafe.

#### [Item 25: Prefer lists to arrays]
1. arrays are covariant. This scary-sounding word means simply that if Sub is a subtype of Super, then the array type Sub[] is a subtype of Super[] . Generics, by contrast, are invariant: for any two distinct types Type1 and Type2 , List&lt;Type1&gt; is neither a subtype nor a supertype of List&lt;Type2&gt;
2. From the Book 

```
The second major difference between arrays and generics is that arrays are reified [JLS, 4.7]. This means that arrays know and enforce their element types at runtime. As noted above, if you try to store a String into an array of Long , you’ll get an ArrayStoreException . Generics, by contrast, are implemented by erasure [JLS, 4.6]. This means that they enforce their type constraints only at compile time and discard (or erase) their element type information at runtime. Erasure is what allows generic types to interoperate freely with legacy code that does not use generics .
```

3. Types such as E , List&lt;E&gt; , and List&lt;String&gt; are technically known as non reifiable types [JLS, 4.7]. Intuitively speaking, a non-reifiable type is one whose runtime representation contains less information than its compile-time representation. The only parameterized types that are reifiable are unbounded wildcard types such as List&lt;?&gt; and Map&lt;?,?&gt; . It is legal, though infrequently useful, to create arrays of unbounded wildcard types

#### [Item 26: Favor generic types]

1. In summary, generic types are safer and easier to use than types that require casts in client code. When you design new types, make sure that they can be used without such casts. This will often mean making the types generic. Generify your existing types as time permits. This will make life easier for new users of these types without breaking existing clients.

#### [Item 27: Favor generic methods]

1. generic methods, like generic types, are safer and easier to use than methods that require their clients to cast input parameters and return values. Like types, you should make sure that your new methods can be used without casts, which will often mean making them generic. And like types, you should generify your existing methods to make life easier for new users without breaking existing clients

2. e.g to calculate max in a list 

```
// Returns the maximum value in a list - uses recursive type bound
public static <T extends Comparable<T>> T max(List<T> list) {
Iterator<T> i = list.iterator();
T result = i.next();
while (i.hasNext()) {
T t = i.next();
if (t.compareTo(result) > 0)
result = t;
}
return result;
}
```

#### [Item 28: Use bounded wildcards to increase API flexibility]

1. The language provides a special kind of parameterized type call a bounded wildcard type.
2. For maximum flexibility, use wildcard types on input parameters that represent producers or consumers.
3. PECS stands for producer- extends , consumer- super .
4. if a parameterized type represents a T producer, use &lt;? extends T&gt; ; if it represents a T consumer, use &lt;? super T&gt;
5. Do not use wildcard types as return types. Rather than providing additional flexibility for your users, it would force them to use wildcard types in client code.
6. Comparables are always consumers, so you should always use Comparable&lt;? super T&gt; in preference to Comparable&lt;T&gt; . The same is true of comparators, so you should always use Comparator&lt;? super T&gt; in preference to Comparator&lt;T&gt; .
7. if a type parameter appears only once in a method declaration, replace it with a wildcard.

#### [Item 29: Consider typesafe heterogeneous containers]

1. The thing to notice is that the wildcard type is nested: it’s not the type of the Map that’s a wildcard type but the type of its key. This means that every key can have a different parameterized type: one can be Class&lt;String&gt; , the next Class&lt;Integer&gt; , and so on. That’s where the heterogeneity comes from.

```
// Typesafe heterogeneous container pattern - implementation
public class Favorites {
private Map<Class<?>, Object> favorites =
new HashMap<Class<?>, Object>();
public <T> void putFavorite(Class<T> type, T instance) {
if (type == null)
throw new NullPointerException("Type is null");
favorites.put(type, instance);
}
public <T> T getFavorite(Class<T> type) {
return type.cast(favorites.get(type));
}
}
```

## [Enums and Annotations]

#### [Item 30: Use enums instead of int constants]

1. An enumerated type is a type whose legal values consist of a fixed set of constants, such as the seasons of the year, the planets in the solar system, or the suits in a deck of playing cards.
2. The basic idea behind Java’s enum types is simple: they are classes that export one instance for each enumeration constant via a public static final field. Enum types are effectively final, by virtue of having no accessible constructors. Because clients can neither create instances of an enum type nor extend it, there can be no instances but the declared enum constants.
3. To associate data with enum constants, declare instance fields and write a constructor that takes the data and stores it in the fields.
4. Switches on enums are good for augmenting external enum types with constant-specific behavior.

#### [Item 31: Use instance fields instead of ordinals]

1. Never derive a value associated with an enum from its ordinal; store it in an instance field instead

#### [Item 32: Use EnumSet instead of bit fields]

1. The java.util package provides the EnumSet class to efficiently represent sets of values drawn from a single enum type
2. In summary, just because an enumerated type will be used in sets, there is no reason to represent it with bit fields. The EnumSet class combines the conciseness and performance of bit fields with all the many advantages of enum types described

#### [Item 33: Use EnumMap instead of ordinal indexing]

```
// Using an EnumMap to associate data with an enum
Map<Herb.Type, Set<Herb>> herbsByType =
new EnumMap<Herb.Type, Set<Herb>>(Herb.Type.class);
for (Herb.Type t : Herb.Type.values())
herbsByType.put(t, new HashSet<Herb>());
for (Herb h : garden)
herbsByType.get(h.type).add(h);
System.out.println(herbsByType);
```

