# Composició d’objectes

En programes reals, els objectes sovint estan formats per altres objectes. A això li diem **composició** i consistix a crear una classe que conté dins d’ella una o més instàncies d’altres classes.

En la majoria de casos, la composició es pot entendre amb la relació «*tindre un*».

Exemples típics:

* Un Cotxe té un Motor
* Un alumne té una llista de notes.
* Un Institut té Aules
* Un Ordinador té components (CPU, RAM, Disc)

Vegem diferents exemples de composició:

## Composició 1 a 1

```python
class Motor:
    def __init__(self, tipus, potencia):
        self.tipus = tipus
        self.potencia = potencia

    def __str__(self):
        return f"Motor {self.tipus} ({self.potencia} CV)"

class Cotxe:
    def __init__(self, marca, motor):
        self.marca = marca
        self.motor = motor  # composició: un cotxe TÉ un motor

    def mostrar_info(self):
        print(f"Cotxe {self.marca} amb {self.motor}")

m1 = Motor("Diesel", 110)
c1 = Cotxe("Seat", m1)

c1.mostrar_info()			# Cotxe Seat amb Motor Diesel (110 CV)
```

## Composició 1 a N

```python
class Alumne:
    def __init__(self, nom):
        self.nom = nom

    def __repr__(self):
        return f"Alumne({self.nom!r})"

class Grup:
    def __init__(self, nom):
        self.nom = nom
        self.alumnes = []        # llista d’objectes Alumne

    def afegir_alumne(self, alumne):
        self.alumnes.append(alumne)

    def llistar_alumnes(self):
        print(f"Grup: {self.nom}")
        for alumne in self.alumnes:
            print(f"- {alumne.nom}")

g = Grup("1r DAW")

g.afegir_alumne(Alumne("Carles"))
g.afegir_alumne(Alumne("Maria"))
g.afegir_alumne(Alumne("Aina"))

g.llistar_alumnes()

# Grup: 1r DAW
# - Carles
# - Maria
# - Joan
```
