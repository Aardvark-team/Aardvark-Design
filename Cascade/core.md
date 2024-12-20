# Cascade Programming Language

Cascade is a language that combines mathematical elegance with practical programming power. It draws inspiration from pure mathematics while maintaining the ergonomics of modern programming languages.

## Core Philosophy

1. **Everything is a Transform**
   - Programs are sequences of value transformations
   - Functions are explicit transforms between types
   - Types are sets of valid transformations

2. **Explicit is Better than Implicit**
   - No hidden control flow
   - No implicit type conversions
   - Side effects must be declared

3. **Mathematical Foundation with Practical Power**
   - Direct mathematical notation where beneficial
   - Practical syntax where mathematical notation becomes cumbersome
   - Strong type system based on set theory

## Basic Syntax

### Value Bindings

```cascade
let x = 5                    // Immutable binding
var y = 7                    // Mutable binding
y := y + 1                   // Mutation operator

// Type annotations
let z: Int = 42
let pi: Real = 3.14159
```

### Functions as Transforms

Functions are explicit transforms between types, written in either mathematical or practical notation:

```cascade
// Mathematical notation
let square(x: Real) = x^2

// Traditional notation with explicit return type
let add(x: Int, y: Int) -> Int {
    x + y
}

// Function with domain constraint (using mathematical notation)
let sqrt(x: Real) where x >= 0 = x^(1/2)

// Multiple dispatch based on type
let add(x: Int, y: Int) -> Int = x + y
let add(x: Real, y: Real) -> Real = x + y
```

### Types as Sets

Types are defined using set notation or traditional syntax:

```cascade
// Set notation for type constraints
type Percentage = {x: Real | 0 <= x <= 100}

// Algebraic data types
type Option<T> = Some(T) | None

// Product types (records)
type Point = {
    x: Real,
    y: Real
}

// Type with behavior
type Vector2D = {
    x: Real,
    y: Real,
    
    // Methods
    magnitude() -> Real = (x^2 + y^2)^(1/2)
    normalize() -> Vector2D where magnitude() != 0 = {
        x: x/magnitude(),
        y: y/magnitude()
    }
}
```

### Effect System

Side effects are explicitly tracked through an effect system:

```cascade
// Pure function (no effects)
let pure_fn(x: Int) -> Int = x + 1

// Function with IO effect
effect IO {
    read() -> String,
    write(String) -> Unit
}

// Function declaring IO effect
let greet(name: String) -> Unit with IO {
    write("Hello, " + name + "!")
}
```

### Pattern Matching

Pattern matching combines mathematical and practical notation:

```cascade
let factorial(n: Natural) -> Natural = match n {
    0 => 1
    n => n * factorial(n - 1)
}

let describe(opt: Option<Int>) -> String = match opt {
    Some(x) where x > 0 => "Positive number: " + x.toString()
    Some(x) where x < 0 => "Negative number: " + x.toString()
    Some(0) => "Zero"
    None => "No value"
}
```

## Example Programs

### Mathematical Expression Evaluator

```cascade
type Expr = 
    | Num(Real)
    | Add(Expr, Expr)
    | Mul(Expr, Expr)
    | Div(Expr, Expr)
    | Pow(Expr, Expr)

let evaluate(e: Expr) -> Real = match e {
    Num(x) => x
    Add(a, b) => evaluate(a) + evaluate(b)
    Mul(a, b) => evaluate(a) * evaluate(b)
    Div(a, b) where evaluate(b) != 0 => evaluate(a) / evaluate(b)
    Pow(a, b) => evaluate(a)^evaluate(b)
    _ => error("Invalid expression")
}

// Usage
let expr = Mul(Add(Num(2), Num(3)), Pow(Num(2), Num(3)))
let result = evaluate(expr)  // ((2 + 3) * 2^3) = 40
```

### Safe Database Operations

```cascade
effect DB<T> {
    query(String) -> T,
    update(String, T) -> Unit
}

type User = {
    id: Int,
    name: String,
    email: String
}

// Function that requires DB effect
let get_user(id: Int) -> Option<User> with DB<User> {
    try {
        Some(query("SELECT * FROM users WHERE id = " + id.toString()))
    } catch {
        None
    }
}

let update_email(id: Int, new_email: String) -> Unit 
    with DB<User> 
    where is_valid_email(new_email) {
    
    match get_user(id) {
        Some(user) => {
            let updated = user{email: new_email}
            update("UPDATE users SET email = $1 WHERE id = $2", [new_email, id])
        }
        None => error("User not found")
    }
}
```

This initial design combines the mathematical rigor seen in your work with the practical safety mechanisms from Flow and the expressiveness of ALTRAN. Would you like me to explore any particular aspect in more detail or create additional example programs to test the design?
