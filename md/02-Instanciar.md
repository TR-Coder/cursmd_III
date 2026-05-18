# Instanciar objectes

Una classe es definix amb la paraula reservada **class**, seguida del nom de la classe. Per convenció, els noms s’escriuen en *PascalCase*.

A continuació, definirem una classe buida anomenada *Alumne* i crearem dos instàncies *a1* i *a2*:

```python
class Alumne:
    pass

a1 = Alumne()
a2 = Alumne()
```

## Atributs d'instància

Els **atributs d’instància** són variables que pertanyen a un objecte concret. En el següent exemple, creem els atributs *nom* i *edat* en l’objecte *a1*:

```python
a1.nom = "Carles"
a1.edat = 18

print(a1.nom)
print(a1.edat)
```

Ara bé, encara que podem crear atributs directament «sobre la marxa», el que es fa habitualment es definir-los d’una manera controlada mitjançant un mètode. 

Quan definim un mètode d’instància dins d’una classe, el primer paràmetre ha de ser **`self`**. Este paràmetre és una referència a l’objecte que executa el mètode i permet accedir als seus atributs i altres mètodes.

Quan fem,
```python
a1.saludar()
```

el que realment estem fent internament és:
```python
Alumne.saludar(a1)
```
**EXEMPLE**

```python
class Alumne:
    def assignar_dades(self, nom, edat):
        self.nom = nom
        self.edat = edat

    def presentar(self):
        print(f"Em dic {self.nom} i tinc {self.edat} anys.")

a1 = Alumne()
a1.assignar_dades("Carles", 18)
a1.presentar()

a2 = Alumne()
a2.assignar_dades("Maria", 19)
a2.presentar()
```

D’esta manera,  quan fem:
```python
a1.assignar_dades("Carles", 18)     # self=a1, nom="Carles", edat=18
```

De manera anàloga:
```python
a2.assignar_dades("Maria", 19)      # self=a2, nom="Maria", edat=19
```

**IMPORTANT**<br>
Si dins d’un mètode no usem **`self`**, estem creant una *variable local*, l’àmbit de la qual és només el propi mètode.


## Constructor `__init__()`

En l’exemple anterior hem creat el mètode *assignar_dades()* per a crear els atributs de la instància. Com que este procés quasi sempre es necessari, el procés de la creació dels atributs es fa sempre a través d’un **mètode constructor** específic anomenat **`__init__()`**. 


El mètode **`__init__(self)`** es caracteritza per:

* S’executa automàticament quan es crea una nova instància de la classe.

* S’utilitza habitualment per inicialitzar els atributs d’instància de l’objecte.

* Retorna implícitament `None`, No pot retornar cap altre valor.

* És opcional i es pot ometre si la classe no necessita inicialitzar atributs d’instància.


```python
class Alumne:
    def __init__(self, nom, edat):
        self.nom = nom
        self.edat = edat

a1 = Alumne("Carles", 18)
a2 = Alumne("Maria", 19)
```

En l’exemple, *nom* i *edat* són variables locals al mètode `__init__()`. En realitat, els noms d’estos paràmetres pot ser qualsevol, encara que és comú que coincidisquen amb els dels atributs.

```python
class Alumne:
    def __init__(self, parametre_1, parametre_2):
        self.nom  = parametre_1
        self.edat = parametre_2
```

El mètode `__init__()` és un mètode normal i pot, per exemple, tindre paràmetres amb valors inicials per defecte:

```python
class Alumne:
    def __init__(self, nom, edat, nota=0):
        self.nom = nom
        self.edat = edat
        self.nota = nota

a1 = Alumne("Carles", 18)          # nota = 0
a2 = Alumne("Maria", 19, 8.5)      # nota = 8.5
```

## Validació bàsica al constructor

El constructor és un lloc ideal per validar dades i assegurar-nos que l’objecte no naix en un estat incorrecte.

```python
class Alumne:
    def __init__(self, nom, edat):
        if nom == "":
            raise ValueError("El nom no pot estar buit.")

        if edat < 0:
            raise ValueError("L'edat no pot ser negativa.")

        self.nom = nom
        self.edat = edat
```


**EXEMPLE**<br>
Objectes coherents i inicialitzats correctament.

```python
class Alumne:
    def __init__(self, nom, edat):
        if nom == "":
            raise ValueError("El nom no pot estar buit.")
        if edat < 0:
            raise ValueError("L'edat no pot ser negativa.")

        self.nom = nom
        self.edat = edat
        self.notes = []

    def afegir_nota(self, nota):
        if nota < 0 or nota > 10:
            raise ValueError("La nota ha d'estar entre 0 i 10.")
        self.notes.append(nota)

    def calcular_mitjana(self):
        if len(self.notes) == 0:
            return 0
        return sum(self.notes) / len(self.notes)

    def mostrar_info(self):
        mitjana = self.calcular_mitjana()
        print(f"Alumne: {self.nom} ({self.edat} anys) - Mitjana: {mitjana:.2f}")
```

## Atributs de classe i d'instància

En Python, els atributs d'una classe solen ser de dos tipus principals:

* **Atributs d’instància**:<br>
    Els *atributs d’instància* pertanyen a cada objecte individualment. Són els que definim normalment dins del constructor `__init__()` amb *self*. Cada objecte té el seu estat independent.

* **Atributs de classe**:<br>
    Els *atributs de classe* pertanyen a la classe i són compartits per totes les instàncies. Es definixen directament dins de la classe, però fora d' `__init__()` i fora de qualsevol mètode.

    Els *atributs de classe* són útils quan tenim dades que són iguals per a totes les instàncies, o representen informació comuna del tipus d’objecte.

**EXEMPLE**<br>
En el següent exemple, *centre* és un **atribut de classe** i és un valor comú i compartit per tots els alumnes. Per altra banda, l’**atribut d'instància** *nom* és particular de cada alumne.


```python
class Alumne:
    centre = "IES Example"

    def __init__(self, nom):
        self.nom = nom


a1 = Alumne("Carles")
a2 = Alumne("Maria")

# Accés a atribut d'instància:
print(a1.nom)			# Carles
print(a2.nom)			# Maria

# Accés a atribut de classe a través de l'objecte.:
print(a1.centre)		# IES Ramón Cid
print(a2.centre)		# IES Ramón Cid

# Accés a atribut de classe a través de la classe:
print(Alumne.center)		# IES Ramón Cid
```


**EXEMPLE**: Comptador d’instàncies<br>
Fixem-nos que estem modificant el comptador amb ***Alumne.total_alumnes*** i no amb ***self.total_alumnes***

```python
class Alumne:
    total_alumnes = 0

    def __init__(self, nom):
        self.nom = nom
        Alumne.total_alumnes += 1


a1 = Alumne("Carles")
a2 = Alumne("Maria")
a3 = Alumne("Aina")

print(Alumne.total_alumnes)		# 3
```

## Shadowing

En Python, podem llegir un atribut de classe tant des de la classe com des d’un objecte:

```python
print(Alumne.total_alumnes)
print(a1.total_alumnes)
```

Si intentem modificar-lo des d’un objecte,

```python
a1.total_alumnes = 99
```

no estem modificant l’atribut de classe, sinó que estem creant un nou atribut d’instància que “tapa” (fa ombra a) l’atribut de classe.

```python
a1.total_alumnes = 100       # crea un atribut d'instància
print(a1.total_alumnes)      # 100 (atribut d'instància)
print(a2.total_alumnes)      # 2   (atribut de classe)
print(Alumne.total_alumnes)  # 2   (atribut de classe)
```

***
**ERRORS COMUNS**

* Oblidar **self** a l’hora de crear els atributs d’instància

    ```python
    class Base:
        n = 0					# Atribut de classe
        def __init__(self):
            n = 2				# Variable local a __init__() ja que no hem utilitzat self.n

    b = Base()
    Base.n	# 0  Accés a l'atribut de classe n
    b.n		# 0	 No es pot accedir a n d'init() ja que és una variable local, accedirem a l'atribut de classe n
    ```

    ```python
    class Base:
        n = 0					# Atribut de classe
        def __init__(self):
            self.n = 2			# Atribut d'instància

    b = Base()
    Base.n			# 0		Accés a l'tribut de classe n
    b.n				# 2		Accés a l'atribut d'instància n
    ```

* Utilitzar una variable d'instància abans de crear-la:

    ```python
    class Base:
        n = 9					# Atribut de classe
        def __init__(self):
            print(self.n)		# Com no hi ha atribut d'instància n mira si hi ha atribut de classe n.

    b = Base()			# 9
    ```

    ```python
    class Base:
        def __init__(self):
            self.n = self.n + 1  # ERROR, intentem un self.n abans que existica.
                                # AttributeError: 'Base' object has no attribute 'n'
    b = Base()
    ```

    ```python
    class Base:
        n = 9					# Atribut de classe
        def __init__(self):
            self.n = self.n + 1  # ERROR, intentem un self.n abans que existica.

    b1 = Base()
    Base.n			# 9
    b1.n			# 10

    b2 = Base()
    Base.n			# 9
    b2.n			# 10
    ```
