# Dataclasses

En moltes ocasions necessitem objectes que simplement guarden informació i, potser, tenen algun mètode senzill. Quan creem classes, solem repetir molt de codi: constructor `__init__()`, la representació `__repr__`, comparacions com `==`,  etc.

Per resoldre este cas, Python incorpora el **mòdul dataclasses**, que permet crear classes d’una manera més ràpida i clara.

Enlloc d’este codi:

```python
class Alumne:
    def __init__(self, nom, edat, grup):
        self.nom = nom
        self.edat = edat
        self.grup = grup

    def __repr__(self):
        return f"Alumne(nom={self.nom!r}, edat={self.edat}, grup={self.grup!r})"
```

Amb **dataclasses** podem fer el mateix de manera molt més simple:

```python
from dataclasses import dataclass

@dataclass
class Alumne:
    nom: str
    edat: int
    grup: str
```

Les **dataclasses** generen de manera automàtica `__init__()`, `__repr__`, `__eq__`, etc.


## Valors per defecte

En les **dataclasses** podem assignar valors per defecte:

```python
from dataclasses import dataclass

@dataclass
class Producte:
    nom: str
    preu: float
    stock: int = 0
```

Ara bé, ens trobem davant el problema típic de valors mutables com a defecte (llistes, diccionaris).

Si creem un atribut com:

```python
notes: list = []
```

L’atribut nom seria compartit per totes les instàncies.

La solució és usar **field(default_factory=...)**

```python
from dataclasses import dataclass, field

@dataclass
class Alumne:
    nom: str
    notes: list = field(default_factory=list)
```

D’esta manera, cada alumne tindrà la seua pròpia llista.


## dataclasses immutables: frozen=True

En alguns casos interessa que un objecte siga immutable (que no es puguen canviar els atributs després de crear-lo).

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Punt:
    x: float
    y: float
```
```python
p = Punt(2, 3)
p.x = 10   # Error
```

## validacions després del constructor: `__post_init__()`

Quan usem dataclasses, el `__init__()` es genera automàticament. Però si volem fer validacions, podem usar `__post_init__()` el qual s’executa després del `__init__()` automàtic.

```python
from dataclasses import dataclass

@dataclass
class Persona:
    nom: str
    edat: int

    def __post_init__(self):
        if self.edat < 0:
            raise ValueError("L'edat no pot ser negativa.")
```

