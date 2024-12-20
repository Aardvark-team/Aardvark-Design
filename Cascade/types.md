# Cascade Type System

The Cascade type system combines set theory, algebraic types, and effect tracking to provide both mathematical rigor and practical safety.

## Core Types

### Primitive Types
```cascade
Unit    // The empty type, similar to void
Bool    // true or false
Int     // Arbitrary precision integers
Real    // Arbitrary precision real numbers
String  // UTF-8 string
```

### Type Constructors

#### Set-Based Types
```cascade
// Range types
type Age = {x: Int | 0 <= x <= 150}
type Probability = {x: Real | 0 <= x <= 1}

// Enumerated sets
type Direction = {North, South, East, West}
type Weekday = {Mon, Tue, Wed, Thu, Fri, Sat, Sun}

// Dependent types
type Vector<n: Natural> = {x: Array<Real> | x.length = n}
type Square<n: Natural> = {x: Matrix<Real> | x.rows = n and x.cols = n}
```

#### Algebraic Types
```cascade
// Sum types (unions)
type Option<T> = Some(T) | None
type Result<T, E> = Ok(T) | Err(E)

// Product types (records)
type Point3D = {
    x: Real,
    y: Real,
    z: Real
}

// Recursive types
type List<T> = Cons(T, List<T>) | Nil
type Tree<T> = Node(T, List<Tree<T>>)
```

### Type Classes

Type classes define behavior that types can implement:

```cascade
class Eq<T> {
    eq(T, T) -> Bool
    neq(a: T, b: T) -> Bool = not eq(a, b)
}

class Ord<T: Eq> {
    compare(T, T) -> Ordering
    lt(a: T, b: T) -> Bool = compare(a, b) == Less
    gt(a: T, b: T) -> Bool = compare(a, b) == Greater
    lte(a: T, b: T) -> Bool = not gt(a, b)
    gte(a: T, b: T) -> Bool = not lt(a, b)
}

// Implementation example
implement Eq<Point3D> {
    eq(a: Point3D, b: Point3D) -> Bool {
        a.x == b.x and a.y == b.y and a.z == b.z
    }
}
```

## Effect System

Effects are tracked in the type system:

```cascade
// Effect definition
effect State<S> {
    get() -> S
    put(S) -> Unit
}

effect IO {
    read() -> String
    write(String) -> Unit
}

// Function with multiple effects
let interactive_counter() -> Unit with State<Int>, IO {
    let current = get()
    write("Current count: " + current.toString())
    put(current + 1)
}

// Pure functions are marked implicitly by lack of effects
let pure_add(x: Int, y: Int) -> Int = x + y
```

## Type Inference

Cascade uses bidirectional type inference:

```cascade
// Type inferred from value
let x = 5                // x: Int
let y = 5.0              // y: Real
let z = Some(5)          // z: Option<Int>

// Type inferred from usage
let double<T: Num>(x: T) -> T = x + x

let a = double(5)        // a: Int
let b = double(5.0)      // b: Real

// Type inference with effects
let print_value<T: Show>(x: T) with IO {
    write(x.show())
}
```

## Subtyping

Cascade supports structural subtyping for records and bounded quantification for generics:

```cascade
type Named = { name: String }
type Person = { name: String, age: Int }

// Person is a subtype of Named because it has all required fields
let print_name(x: Named) with IO {
    write("Name: " + x.name)
}

let person = { name: "Alice", age: 30 }
print_name(person)  // Valid because Person <: Named

// Bounded quantification
let sort<T: Ord>(list: List<T>) -> List<T> {
    // Implementation
}
```

## Type-Level Programming

Types can be manipulated at compile time:

```cascade
// Type-level functions
type Map<F<_>, T> = match T {
    List<A> => List<F<A>>
    Option<A> => Option<F<A>>
    (A, B) => (F<A>, F<B>)
}

// Type-level predicates
type IsNumeric<T> = T is Int or T is Real
type HasLength<T> = T has method length() -> Int

// Conditional types
type ElementType<T> = match T {
    Array<E> => E
    List<E> => E
    String => Char
    _ => Never
}
```

This type system provides both the rigor of mathematical type theory and the practicality needed for real-world programming. Would you like me to explore any specific aspect in more detail or show how it handles particular programming patterns?
