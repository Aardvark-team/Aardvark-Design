# Cascade Language Specification
Version 1.0.0

## 1. Lexical Structure

### 1.1 Source Code Representation
- Source files are UTF-8 encoded
- Line endings are LF or CRLF
- Source code is parsed as a sequence of tokens

### 1.2 Tokens
```ebnf
token ::= keyword | identifier | literal | operator | delimiter

keyword ::= 'let' | 'var' | 'type' | 'effect' | 'space' | 'where' | 'match'
          | 'if' | 'else' | 'for' | 'while' | 'return' | 'with'

identifier ::= letter (letter | digit | '_')*

literal ::= integer_lit | float_lit | string_lit | bool_lit
integer_lit ::= digit+ | '0x' hex_digit+ | '0b' bin_digit+
float_lit ::= digit+ '.' digit+ ['e' ['+' | '-'] digit+]
string_lit ::= '"' char* '"'
bool_lit ::= 'true' | 'false'

operator ::= '+' | '-' | '*' | '/' | '^' | '=' | ':=' | '==' | '!=' | '<' | '>'
           | '<=' | '>=' | '->' | '=>' | '|' | '&' | '.'

delimiter ::= '(' | ')' | '{' | '}' | '[' | ']' | ',' | ';' | ':'
```

## 2. Types

### 2.1 Basic Types
```cascade
// Core types
Unit    // The unit type, represents no value
Bool    // true or false
Int     // Arbitrary precision integer
Real    // Arbitrary precision real number
String  // UTF-8 string sequence

// Type constructors
Option<T>    // Some(T) or None
Result<T, E> // Ok(T) or Err(E)
Array<T>     // Ordered sequence of T
Map<K, V>    // Key-value mapping
```

### 2.2 Type Expressions
```ebnf
type_expr ::= basic_type
            | type_var
            | type_app
            | function_type
            | effect_type
            | where_type
            | set_type

basic_type ::= 'Unit' | 'Bool' | 'Int' | 'Real' | 'String'

type_var ::= identifier

type_app ::= identifier '<' type_list '>'

function_type ::= '(' param_types ')' '->' return_type ['with' effect_list]

effect_type ::= effect_name ['<' type_list '>']

where_type ::= type_expr 'where' predicate

set_type ::= '{' identifier ':' type_expr '|' predicate '}'
```

### 2.3 Type Rules
```cascade
// Subtyping
Γ ⊢ S <: T   // S is a subtype of T

// Type equality
Γ ⊢ S = T    // S is equal to T

// Type membership
Γ ⊢ e : T    // Expression e has type T

// Effect tracking
Γ ⊢ e : T ! E  // Expression e has type T and effects E
```

## 3. Effects

### 3.1 Effect Definition
```cascade
effect IO {
    read() -> String
    write(String) -> Unit
}

effect State<S> {
    get() -> S
    put(S) -> Unit
}
```

### 3.2 Effect Composition
```ebnf
effect_expr ::= effect_name
              | effect_var
              | effect_app
              | effect_union
              | effect_transform

effect_union ::= effect_expr '+' effect_expr

effect_transform ::= effect_name '<' effect_expr '>'
```

### 3.3 Effect Rules
```cascade
// Effect subsumption
Γ ⊢ E1 <: E2   // Effect E1 is subsumed by E2

// Effect composition
Γ ⊢ E1 + E2    // Union of effects E1 and E2

// Effect handling
Γ ⊢ handle e with h : T ! E
```

## 4. Spaces

### 4.1 Space Definition
```ebnf
space_def ::= ['distributed'] 'space' identifier '{' space_body '}'

space_body ::= (state_decl | transform_decl | view_decl)*

state_decl ::= 'state' identifier ':' type_expr ['=' expr]

transform_decl ::= 'transform' identifier '(' params ')' '->' type_expr
                  ['with' effect_list] block

view_decl ::= ['parallel'] 'view' identifier '(' params ')' '->' type_expr
              ['=' expr]
```

### 4.2 Space Rules
```cascade
// Space composition
Γ ⊢ S1 + S2    // Composition of spaces S1 and S2

// Space inheritance
Γ ⊢ S1 <: S2   // Space S1 inherits from S2

// Space effects
Γ ⊢ S : E      // Space S has effects E
```

## 5. Expressions

### 5.1 Expression Grammar
```ebnf
expr ::= literal
       | identifier
       | unary_expr
       | binary_expr
       | if_expr
       | match_expr
       | lambda_expr
       | block_expr
       | space_expr

unary_expr ::= operator expr

binary_expr ::= expr operator expr

if_expr ::= 'if' expr block_expr ['else' block_expr]

match_expr ::= 'match' expr '{' match_arms '}'

lambda_expr ::= ['async'] 'fn' '(' params ')' '->' type_expr block_expr

block_expr ::= '{' stmt* expr? '}'
```

### 5.2 Expression Rules
```cascade
// Value typing
Γ ⊢ e : T     // Expression e has type T

// Effect typing
Γ ⊢ e : T ! E // Expression e has type T and effects E

// Space typing
Γ ⊢ e : S     // Expression e has space type S
```

## 6. Progressive Complexity Levels

### 6.1 Level 1 - Basic
```cascade
// Available features:
- Simple let bindings
- Basic types (Int, String, Bool)
- Simple functions
- If expressions
- Basic pattern matching
```

### 6.2 Level 2 - Intermediate
```cascade
// Additional features:
- Type annotations
- Generic types
- Basic effect system
- Simple spaces
- Error handling with Result
```

### 6.3 Level 3 - Advanced
```cascade
// Additional features:
- Full effect system
- Distributed spaces
- Type-level programming
- Custom effects
- Advanced pattern matching
```

### 6.4 Level 4 - Expert
```cascade
// Additional features:
- Mathematical notation
- Dependent types
- Effect polymorphism
- Space composition
- Meta-programming
```

## 7. Memory Model

### 7.1 Ownership
```cascade
// Ownership rules
1. Each value has exactly one owner
2. Ownership can be transferred
3. References must not outlive their referent
```

### 7.2 Borrowing
```cascade
// Borrowing rules
1. One mutable reference OR many immutable references
2. References must be valid for their entire lifetime
3. References cannot outlive their space
```

### 7.3 Space Memory
```cascade
// Space memory rules
1. Space state is isolated
2. Transforms can only access their space's state
3. Views cannot modify state
4. Distributed spaces handle remote memory
```

## 8. Concurrency Model

### 8.1 Space Concurrency
```cascade
// Space concurrency rules
1. Spaces are concurrency boundaries
2. Transforms are atomic
3. Views can run in parallel
4. Distributed spaces handle network partitions
```

### 8.2 Effect Concurrency
```cascade
// Effect concurrency rules
1. Pure computations can run in parallel
2. Effect handlers coordinate access
3. Effect composition preserves safety
```

## 9. Type System Properties

### 9.1 Soundness
```cascade
// Type soundness theorem
If Γ ⊢ e : T and e →* v, then Γ ⊢ v : T
```

### 9.2 Effect Safety
```cascade
// Effect safety theorem
If Γ ⊢ e : T ! E and e →* v, then all effects in evaluation are in E
```

### 9.3 Space Safety
```cascade
// Space safety theorem
If Γ ⊢ e : S and e →* v, then all space operations preserve invariants
```

Would you like me to:
1. Add more detailed rules for specific features?
2. Provide formal proofs for the theorems?
3. Add more examples for each section?
4. Expand the progressive complexity specification?
