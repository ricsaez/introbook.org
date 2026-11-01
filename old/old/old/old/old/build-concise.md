## 1. Values

    T : Type
    T = c₁ : T | c₂ : T | ...

## 2. Functions

    f : T → B
    f = (c₁) ↦ v₁ | (c₂) ↦ v₂ | ...

    f(cᵢ) >> vᵢ

## 3. Calls inside calls

    v = cᵢ | f(v)

    f(g(cᵢ)) >> f(vᵢ)

## 4. Variables

    v = cᵢ | f(v) | x

    f = (x) ↦ g(x)

    f(cᵢ) >> g(cᵢ)

## 5. Several inputs

    f : (A, B) → C
    f = (c₁, x) ↦ g(x) | (c₂, x) ↦ h(x) | ...

    v = cᵢ | f(v, ...) | x

    f(c₁, cⱼ) >> g(cⱼ)

## 6. Values that hold values

    T = c₁ : (A, B) → T | ...

    p = cᵢ | cᵢ(p, ...) | x
    v = cᵢ | cᵢ(v, ...) | f(v, ...) | x
    d = cᵢ | cᵢ(d, ...)

    f = (c₁(x, y)) ↦ g(x, y)

    f(c₁(d₁, d₂)) >> g(d₁, d₂)

## 7. Values that hold their own type

    T = c₁ : T | c₂ : T → T

    c₁, c₂(c₁), c₂(c₂(c₁)), ...

## 8. Functions that call themselves

    f : T → B
    f = (c₁) ↦ v | (c₂(x)) ↦ g(f(x))

    f(c₂(c₂(c₁))) >> g(f(c₂(c₁))) >> g(g(f(c₁))) >> g(g(v))

## 9. Running forever

    f = (x) ↦ f(x)

    f(cᵢ) >> f(cᵢ) >> f(cᵢ) >> ...

## 10. Nothing is missing

    T : Type
    T = c₁ : T | c₂ : (A, ...) → T | ...

    f : (A, ...) → B
    f = (p, ...) ↦ v | (p, ...) ↦ v | ...

    p = cᵢ | cᵢ(p, ...) | x
    v = cᵢ | cᵢ(v, ...) | f(v, ...) | x
    d = cᵢ | cᵢ(d, ...)

    f(d, ...) >> v

## 11. Functions as inputs

    v = cᵢ | f | x | v(v, ...)

    f : (A → B, A) → B
    f = (x, y) ↦ x(y)

    f(g, cᵢ) >> g(cᵢ)

## 12. Functions made while working out

    v = cᵢ | f | x | v(v, ...) | ((p, ...) ↦ v | ...)

    f : A → (B → C)
    f = (x) ↦ ((y) ↦ g(x, y))

    f(cᵢ) >> (y) ↦ g(cᵢ, y)
    ((y) ↦ g(cᵢ, y))(cⱼ) >> g(cᵢ, cⱼ)

## 13. Types that take types

    T : (A : Type) → Type
    T = c₁ : T(A) | c₂ : (A, T(A)) → T(A) | ...

    T(B) : Type

    f : (A → B, T(A)) → T(B)
