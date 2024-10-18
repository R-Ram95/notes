# Scenario

- `Shape` class with two subclasses, `Circle` and `Square`
- extend to incorporate colour: `Red` and `Blue`
- problem:
    - if we add `Triangle`, we need to add two more classes
        - `RedTriangle`, `BlueTriangle`
    - if we add `Green` colour, we need to create three more classes:
        - `GreenTriangle`, `GreenCircle`, `GreenSquare`
- This problem is exponential as we add more shapes and colours

![[/Untitled 81.png|Untitled 81.png]]

# Why does this happen?

- we are trying to extend the shape classes in two dimensions, form and colour ⇒ this is the main issue with inheritance

# Solution

- extract one dimension into a separate class hierarchy
- the original class references this new hierarchy

![[/Untitled 1 57.png|Untitled 1 57.png]]

- shape class can delegate colour related work to the colour hierarchy
- the reference between `Shape` and `Colour` is the bridge between the hierarchies