# Cascade Type System Specification

## 1. Type Judgments

### 1.1 Basic Judgments
```
Γ ⊢ e : τ         Expression typing
Γ ⊢ τ₁ <: τ₂      Subtyping
Γ ⊢ e : τ ! ε     Effect typing
Γ ⊢ σ space       Space typing
```

### 1.2 Context Rules
```
-------------- (Ctx-Empty)
⊢ ∅ ctx

Γ ⊢ τ type    x ∉ dom(Γ)
------------------------ (Ctx-Var)
Γ, x:τ ⊢ ctx

Γ ⊢ ε effect  X ∉ dom(Γ)
------------------------ (Ctx-Effect)
Γ, X:ε ⊢ ctx
```

## 2. Type Formation

### 2.1 Basic Types
```
--------------- (Type-Unit)
Γ ⊢ Unit type

--------------- (Type-Bool)
Γ ⊢ Bool type

--------------- (Type-Int)
Γ ⊢ Int type

--------------- (Type-Real)
Γ ⊢ Real type

--------------- (Type-String)
Γ ⊢ String type
```

### 2.2 Type Constructors
```
Γ ⊢ τ type
------------------- (Type-Option)
Γ ⊢ Option<τ> type

Γ ⊢ τ type   Γ ⊢ ε type
--------------------------- (Type-Result)
Γ ⊢ Result<τ,ε> type

Γ ⊢ τ type
------------------- (Type-Array)
Γ ⊢ Array<τ> type

Γ ⊢ κ type   Γ ⊢ ν type
--------------------------- (Type-Map)
Γ ⊢ Map<κ,ν> type
```

### 2.3 Function Types
```
Γ ⊢ τ₁ type   Γ ⊢ τ₂ type
-------------------------------- (Type-Fun)
Γ ⊢ (τ₁ → τ₂) type

Γ ⊢ τ₁ type   Γ ⊢ τ₂ type   Γ ⊢ ε effect
----------------------------------------- (Type-Fun-Effect)
Γ ⊢ (τ₁ →ε τ₂) type
```

## 3. Subtyping Rules

### 3.1 Basic Subtyping
```
Γ ⊢ τ type
------------ (Sub-Refl)
Γ ⊢ τ <: τ

Γ ⊢ τ₁ <: τ₂   Γ ⊢ τ₂ <: τ₃
---------------------------- (Sub-Trans)
Γ ⊢ τ₁ <: τ₃
```

### 3.2 Type Constructor Subtyping
```
Γ ⊢ τ₁ <: τ₂
-------------------------- (Sub-Option)
Γ ⊢ Option<τ₁> <: Option<τ₂>

Γ ⊢ τ₁ <: τ₂   Γ ⊢ ε₁ <: ε₂
-------------------------------- (Sub-Result)
Γ ⊢ Result<τ₁,ε₁> <: Result<τ₂,ε₂>
```

### 3.3 Function Subtyping
```
Γ ⊢ σ₁ <: τ₁   Γ ⊢ τ₂ <: σ₂
-------------------------------- (Sub-Fun)
Γ ⊢ (τ₁ → τ₂) <: (σ₁ → σ₂)

Γ ⊢ σ₁ <: τ₁   Γ ⊢ τ₂ <: σ₂   Γ ⊢ ε₁ <: ε₂
--------------------------------------------- (Sub-Fun-Effect)
Γ ⊢ (τ₁ →ε₁ τ₂) <: (σ₁ →ε₂ σ₂)
```

## 4. Effect System

### 4.1 Effect Formation
```
---------------- (Effect-Pure)
Γ ⊢ Pure effect

------------------ (Effect-IO)
Γ ⊢ IO effect

Γ ⊢ τ type
---------------------- (Effect-State)
Γ ⊢ State<τ> effect
```

### 4.2 Effect Composition
```
Γ ⊢ ε₁ effect   Γ ⊢ ε₂ effect
------------------------------ (Effect-Union)
Γ ⊢ (ε₁ + ε₂) effect

Γ ⊢ ε₁ effect   Γ ⊢ ε₂ effect
------------------------------ (Effect-Transform)
Γ ⊢ Transform<ε₁,ε₂> effect
```

### 4.3 Effect Subtyping
```
Γ ⊢ ε effect
------------- (Effect-Sub-Refl)
Γ ⊢ ε <: ε

Γ ⊢ ε₁ <: ε₂   Γ ⊢ ε₂ <: ε₃
---------------------------- (Effect-Sub-Trans)
Γ ⊢ ε₁ <: ε₃

---------------- (Effect-Sub-Pure)
Γ ⊢ Pure <: ε
```

## 5. Space System

### 5.1 Space Formation
```
Γ ⊢ τ type
-------------------- (Space-State)
Γ ⊢ State<τ> space

Γ ⊢ σ₁ space   Γ ⊢ σ₂ space
------------------------------ (Space-Compose)
Γ ⊢ (σ₁ + σ₂) space
```

### 5.2 Space Operations
```
Γ ⊢ σ space   Γ ⊢ e : τ ! ε
-------------------------------- (Space-Transform)
Γ ⊢ transform(σ,e) : τ ! (ε + σ)

Γ ⊢ σ space   Γ ⊢ e : τ
---------------------------- (Space-View)
Γ ⊢ view(σ,e) : τ ! Pure
```

## 6. Progressive Type System

### 6.1 Level 1 - Basic Types
```
Γ ⊢ e : τ   τ ∈ {Unit, Bool, Int, String}
----------------------------------------- (L1-Basic)
Γ ⊢₁ e : τ

x:τ ∈ Γ   τ is basic
--------------------- (L1-Var)
Γ ⊢₁ x : τ
```

### 6.2 Level 2 - Generic Types
```
Γ ⊢₁ e : τ
------------ (L2-Up)
Γ ⊢₂ e : τ

Γ ⊢ τ type   Γ ⊢ e : τ
----------------------- (L2-Generic)
Γ ⊢₂ e : τ
```

### 6.3 Level 3 - Effect Types
```
Γ ⊢₂ e : τ
------------ (L3-Up)
Γ ⊢₃ e : τ

Γ ⊢ e : τ ! ε
---------------- (L3-Effect)
Γ ⊢₃ e : τ ! ε
```

### 6.4 Level 4 - Dependent Types
```
Γ ⊢₃ e : τ
------------ (L4-Up)
Γ ⊢₄ e : τ

Γ ⊢ τ type   Γ, x:τ ⊢ φ prop
------------------------------ (L4-Dependent)
Γ ⊢₄ {x:τ | φ} type
```

## 7. Type Safety

### 7.1 Progress
```
If Γ ⊢ e : τ and e is not a value,
then ∃e'. e → e'
```

### 7.2 Preservation
```
If Γ ⊢ e : τ and e → e',
then Γ ⊢ e' : τ
```

### 7.3 Effect Safety
```
If Γ ⊢ e : τ ! ε and e →* v,
then all effects in the evaluation are in ε
```

Would you like me to:
1. Add more typing rules for specific features?
2. Provide formal proofs for type safety?
3. Expand the progressive type system?
4. Add more examples of type derivations?
