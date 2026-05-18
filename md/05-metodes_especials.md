# Mètodes especials (Dunder Methods)

En Python, les classes es comporten d’una manera natural dins del llenguatge, de manera que els objectes es poden comparar, sumar, ordenar, imprimir o, fins i tot, utilitzar amb funcions com len().

Tot això és possible gràcies als mètodes especials, també anomenats dunder methods (de double underscore), o mètodes màgics. Estos mètodes tenen noms especials, sempre amb doble guió baix al principi i al final:

```text
__init__
__str__
__repr__
__len__
__eq__
  ...
```

## `__str__()` i `__repr__()`

Si fem el print d’un objecte, Python mostra una representació poc útil:

```python
class Alumne:
    def __init__(self, nom):
        self.nom = nom

a = Alumne("Carles")
print(a)                    # <__main__.Alumne object at 0x000001A2...>
```

Com es veu, mostra informació poc rellevant. Per a solucionar-ho, definim `__str__()` i `__repr__()`.

* `__str__()` definix la representació en text d’un objecte orientada a mostrar informació de manera amigable i fàcil de llegir. S’utilitza sobretot en print(obj) o str(obj). El que s’espera es que mostre l’objecte sense massa detall tècnic. Alguna cosa com:
  
    ```text
    Alumne: Carles (18 anys)
    ```

* `__repr__()` definix una representació pensada sobretot per a depurar (debugging) i entendre l’estat intern de l’objecte amb informació més completa i exacta. El que s’espera es que mostre l’objecte amb més detall tècnic. Alguna cosa com:
  
    ```text
    Alumne(nom='Carles', edat=18)
    ```
Si existix `__str__()`, print(obj) mostrarà este format, sinó utilitzarà `__repr__()`. Si no existixen cap dels dos, es mostra el format per defecte (memòria i tipus).


**EXEMPLE**

```python
class Alumne:
    def __init__(self, nom, edat):
        self.nom = nom
        self.edat = edat

    def __str__(self):
        return f"Alumne: {self.nom} ({self.edat} anys)"

    def __repr__(self):
        return f"Alumne(nom={self.nom!r}, edat={self.edat})"
```


## `__len__()`

El mètode `__len__()` definix la longitud d’un objecte i permet usar **len(obj)**.

```python
class Grup:
    def __init__(self, nom):
        self.nom = nom
        self.alumnes = []

    def afegir_alumne(self, alumne):
        self.alumnes.append(alumne)

    def __len__(self):
        return len(self.alumnes)

g = Grup("1r DAW")
g.afegir_alumne("Carles")
g.afegir_alumne("Maria")

print(len(g))			# 2 
```

## `__bool__()`

Definix quan un objecte es considera *True* o *False*.

```python
class Grup:
    def __init__(self):
        self.alumnes = []

    def __bool__(self):
        return len(self.alumnes) > 0

g = Grup()

if g:
    print("Grup amb alumnes")
else:
    print("Grup buit")
```


## Comparacions

Per defecte, comparar objectes amb == compara si són el mateix objecte en memòria, no si tenen les mateixes dades.

```python
class Alumne:
    def __init__(self, dni):
        self.dni = dni

a1 = Alumne("123A")
a2 = Alumne("123A")

print(a1 == a2)		# False
```

Això pot no ser el que volem. Normalment voldríem que dos alumnes amb el mateix *dni* es consideren iguals.

### `__eq__()` : Definir igualtat

```python
class Alumne:
    def __init__(self, dni):
        self.dni = dni

    def __eq__(self, other):
        if not isinstance(other, Alumne):
            return False
        return self.dni == other.dni

a1 = Alumne("123A")
a2 = Alumne("123A")

print(a1 == a2)		# True
```

Per a ordenar una llista amb objectes, Python només necessita que existisca un criteri. Normalment, amb `__lt__()` (menor que) és suficient:

```python
class Alumne:
    def __init__(self, nom, edat):
        self.nom = nom
        self.edat = edat

    def __lt__(self, other):
        return self.edat < other.edat

    def __repr__(self):
        return f"{self.nom}({self.edat})"

alumnes = [Alumne("Carles", 18), Alumne("Maria", 19), Alumne("Aina", 17)]
alumnes.sort()
print(alumnes)			# [Aina(17), Carles(18), Maria(19)]
```

### Restar d'operadors de comparació

A continuació, una taula amb els operadors i el mètodes especials corresponents:

| Operador | Mètode     |
|---------|------------|
| `==`    | `__eq__()` |
| `!=`    | `__ne__()` |
| `<`     | `__lt__()` |
| `<=`    | `__le__()` |
| `>`     | `__gt__()` |
| `>=`    | `__ge__()` |

Si no s'implementen cap d’estos mètodes, == compara si són el mateix objecte (identitat en memòria), la resta (<, >, etc.) solen donar error perquè Python no sap quin criteri usar

Si volem que totes les comparacions funcionen (<, <=, >, >=, ==, !=), hem d’implementar les mètodes corresponents, o utilitzar el decorador **@total_ordering**.

El decorador **@total_ordering** permet que, definint `__eq__()` i un dels mètodes d’ordre (`__lt__` o `__gt__`, etc.), generar la resta de manera automàtica.


**EXEMPLE**\
El següent exemple definix `__eq__` i `__lt__`, la resta es generen de manera automàtica:

```python
from functools import total_ordering

@total_ordering
class Alumne:
    def __init__(self, nom, nota):
        self.nom = nom
        self.nota = nota

    def __eq__(self, other):
        if not isinstance(other, Alumne):
            return NotImplemented
        return self.nota == other.nota

    def __lt__(self, other):
        if not isinstance(other, Alumne):
            return NotImplemented
        return self.nota < other.nota
```

En el codi anterior, quan es comparem objectes de classes diferents, retornar **NotImplemented** és una manera d’indicar que no se sap comparar. Això fa que Python intente cridar el mètode de l’altre objecte o acabe retornant False/llançe error, segons el cas. És més correcte que retornar False directament.

***
**Comportament en sets i diccionaris**\
Si volem usar objectes com a claus de diccionari (dict) o dins d’un set necessitem una definició coherent de `__eq__` i `__hash__`.

La Norma bàsica és que si dos objectes són iguals (a == b True), han de tindre el mateix hash. Això significa que si redefinim `__eq__`, també hem de definir `__hash__`, sinó Python pot considerar l’objecte unhashable (no es pot posar en set ni com a clau).

### Operadors aritmètics

* Es Poden definir: +, -, *, /, //, %, **, unaris, += (i altres), versions invertides i també operadors de bits.

* No es poden definir directament: and, or, not (però sí controlar True/False amb `__bool__`). També podem definir el comportament d’operadors aritmètics.

```python
class Vector2D:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __add__(self, other):
        return Vector2D(self.x + other.x, self.y + other.y)

    def __repr__(self):
        return f"Vector2D({self.x}, {self.y})"

v1 = Vector2D(2, 3)
v2 = Vector2D(4, 1)

print(v1 + v2)			# Vector2D(6, 4)
```

A continuació, una taula completa:

| Categoria | Operador / Ús | Mètode especial (dunder) |
|----------|----------------|---------------------------|
| **Aritmètics** | `a + b` | `__add__()` |
| **Aritmètics** | `a - b` | `__sub__()` |
| **Aritmètics** | `a * b` | `__mul__()` |
| **Aritmètics** | `a / b` | `__truediv__()` |
| **Aritmètics** | `a // b` | `__floordiv__()` |
| **Aritmètics** | `a % b` | `__mod__()` |
| **Aritmètics** | `a ** b` | `__pow__()` |
| **Unaris** | `+a` | `__pos__()` |
| **Unaris** | `-a` | `__neg__()` |
| **Unaris** | `abs(a)` | `__abs__()` |
| **Aritmètics in-place** | `a += b` | `__iadd__()` |
| **Aritmètics in-place** | `a -= b` | `__isub__()` |
| **Aritmètics in-place** | `a *= b` | `__imul__()` |
| **Aritmètics in-place** | `a /= b` | `__itruediv__()` |
| **Aritmètics in-place** | `a //= b` | `__ifloordiv__()` |
| **Aritmètics in-place** | `a %= b` | `__imod__()` |
| **Aritmètics in-place** | `a **= b` | `__ipow__()` |
| **Aritmètics (invertits)** | `a + b` (si `a` no pot) | `__radd__()` |
| **Aritmètics (invertits)** | `a - b` (si `a` no pot) | `__rsub__()` |
| **Aritmètics (invertits)** | `a * b` (si `a` no pot) | `__rmul__()` |
| **Aritmètics (invertits)** | `a / b` (si `a` no pot) | `__rtruediv__()` |
| **Aritmètics (invertits)** | `a // b` (si `a` no pot) | `__rfloordiv__()` |
| **Aritmètics (invertits)** | `a % b` (si `a` no pot) | `__rmod__()` |
| **Aritmètics (invertits)** | `a ** b` (si `a` no pot) | `__rpow__()` |
| **Comparació** | `a == b` | `__eq__()` |
| **Comparació** | `a != b` | `__ne__()` |
| **Comparació** | `a < b` | `__lt__()` |
| **Comparació** | `a <= b` | `__le__()` |
| **Comparació** | `a > b` | `__gt__()` |
| **Comparació** | `a >= b` | `__ge__()` |
| **Bits (bitwise)** | `a & b` | `__and__()` |
| **Bits (bitwise)** | `a | b` | `__or__()` |
| **Bits (bitwise)** | `a ^ b` | `__xor__()` |
| **Bits (bitwise)** | `~a` | `__invert__()` |
| **Bits (bitwise)** | `a << b` | `__lshift__()` |
| **Bits (bitwise)** | `a >> b` | `__rshift__()` |
| **Matrius** | `a @ b` | `__matmul__()` |
| **Conversió a text** | `str(a)` / `print(a)` | `__str__()` |
| **Representació tècnica** | consola / llistes / depurar | `__repr__()` |
| **Longitud** | `len(a)` | `__len__()` |
| **Valor lògic** | `if a:` | `__bool__()` |
| **Accés per índex** | `a[i]` | `__getitem__()` |
| **Assignació per índex** | `a[i] = v` | `__setitem__()` |
| **Esborrar índex** | `del a[i]` | `__delitem__()` |
| **Iteració** | `for x in a` | `__iter__()` |
| **Iteració (manual)** | `next(it)` | `__next__()` |
| **Contindre** | `x in a` | `__contains__()` |
| **Cridada com funció** | `a()` | `__call__()` |


NOTA: and, or, not no es poden sobrecarregar amb dunder methods. El seu comportament depén de `__bool__()`.
