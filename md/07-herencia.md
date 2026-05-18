# Herència

## Conceptes

La **herència** és un mecanisme de la OPP que permet crear una classe nova a partir d’una classe existent. La nova classe hereta els atributs i els mètodes de la classe base, el que li permet reutilitzar el que ja està implementat i afegir noves característiques.

La herència es basa en una relació del tipus «**és un**». És a dir, la classe filla és un tipus especial o concret de la classe pare.

Exemples:

* Un Gos és un Animal
* Un Professor és una Persona
* Un CotxeElectric és un Cotxe

La herència és adequada quan es dona alguna de les següents condicions:

* Hi ha característiques comunes que volem reutilitzar.
* Les subclasses compartixen una estructura similar.
* Té sentit que la classe filla siga substituïble per la classe pare.

La sintaxi de l’herència és:

```python
class Subclasse(ClasseBase)
```

Per exemple:
```python
class Animal:
    pass

class Gos(Animal):
    pass
```

Esta relació indica un jerarquia, de manera que:

- Animal és la **classe base** o **classe pare**.
- Gos és la **classe derivada**, **classe filla** o **subclasse**.


## Heretar atributs i mètodes

El següent exemple, *Gos* hereta el comportament d’*Animal* i afegix una nova funcionalitat. Un *Animal* pot *dormir*, un *Gos* per ser un *Animal* pot *dormir* i, a més a més, sap *lladrar*.

Definim la classe pare:
```python
class Animal:
    def __init__(self, nom):
        self.nom = nom

    def dormir(self):
        print(f"{self.nom} està dormint.")
```

Ara creem una classe filla:
```python
class Gos(Animal):
    def lladrar(self):
        print(f"{self.nom} diu: guau guau!")

g = Gos("Toby")
g.dormir()     # heretat de Animal.	Toby està dormint.
g.lladrar()    # propi de Gos.	Toby diu: guau guau!
```

## Sobreescriptura (override)

Una subclasse pot redefinir un mètode de la classe base per a canviar el seu comportament.

En el següent exemple, la classe *Gos* sobreescriu el mètode *fer_so()* heretat de la seua classe base.

```python
class Animal:
    def fer_so(self):
        print("So genèric d'animal")

class Gos(Animal):
    def fer_so(self):
        print("guau, guau")

a = Animal()
g = Gos()

a.fer_so()		#  So genèric d'animal
g.fer_so()		# guau, guau
```

## super()

La funció **super()** permet accedir als **mètodes i atributs** de la **classe pare** des d'una classe filla. Amb **super()** evitem especificar implícitament el nom de la classe pare, pel que si la canviarem, la classe **super()** continuaria apuntant la classe pare.

```python
class Persona:
    def __init__(self, nom, edat):
        self.nom = nom
        self.edat = edat

class Professor(Persona):
    def __init__(self, nom, edat, especialitat):
        super().__init__(nom, edat)  		# inicialitza la part Persona
        self.especialitat = especialitat

p = Professor("Marta", 35, "Informàtica")
print(p.nom, p.edat, p.especialitat)			# Marta 35 Informàtica
```


**EXEMPLE**<br>
En el següent exemple, com la classe Filla no ha redefinit el mètode `__init__()` sinó que hereta el de la classe Pare:

```python
class Pare:
    def __init__(self,num):
        print('constructor Pare')
        self.n = num

class Filla(Pare):
    def mostra(self):
        print(self.n)

f = Filla(8)        # constructor Pare
f.mostra()          # 8
```

Ara redefinim `__init__()` en la classe Filla. Obtenim un error ja que, com no hem cridat explícitament a l'`__init__()` de Pare, no s'ha creat l'atribut *n*:

```python
class Filla(Pare):
    def __init__(self, num):
        self.n = num
        
    def mostra(self):
        print(self.n)       AttributeError: 'Filla' object has no attribute 'n'
```

Ara ho fem bé:

```python
class Filla(Pare):
    def __init__(self, num):
        super().__init__(num)
        self.m = num
        
    def mostra(self):
        print(self.n)  
```


***
**EXEMPLE COMPLET**:<br>
Jerarquia d’Empleats<br>

L’objectiu és que totes les subclasses compartisquen la mateixa «*interfície*» *calcular_sou()*, però cadascuna la implemente segons les seues regles.

**Classe Base**
```python
class Empleat:
    def __init__(self, nom, sou_base):
        self.nom = nom
        self.sou_base = sou_base

    def calcular_sou(self):
        return self.sou_base
```

**Subclasse 1**
```python
class EmpleatFix(Empleat):
    pass
```

**Subclasse 2**
```python
class EmpleatPerHores(Empleat):
    def __init__(self, nom, sou_hora, hores):
        super().__init__(nom, 0)
        self.sou_hora = sou_hora
        self.hores = hores

    def calcular_sou(self):
        return self.sou_hora * self.hores
```

```python
e1 = EmpleatFix("Carles", 1500)
e2 = EmpleatPerHores("Aina", 12, 80)

print(e1.nom, e1.calcular_sou())			# Carles 1500
print(e2.nom, e2.calcular_sou())			# Aina 960
```

## Polimorfisme

En general, en programació, **polimorfisme** vol dir que diferents objectes poden respondre al mateix mètode, però executant comportaments diferents segons el seu tipus.

En OOP, parlem de **polimorfisme** quan tenim diferents classes (normalment relacionades per herència) que compartixen un mateix mètode (mateix nom), però cada classe el pot implementar de manera diferent.

### Sobreescriptura de mètodes (override)

La forma més clàssica de **polimorfisme** en OOP és a través de la **sobreescriptura de mètodes**.

En el següent exemple, cridem el mateix mètode *fer_so()*, però el resultat depén de la classe real de l’objecte.

**Classe base**
```python
class Animal:
    def fer_so(self):
        print("So genèric d'animal")
```

**Subclasses**
```python
class Gos(Animal):
    def fer_so(self):
        print("Gua Guau!")

class Gat(Animal):
    def fer_so(self):
        print("Meu meu!")
```

```python
animals = [Gos(), Gat(), Gos()]

for a in animals:
    a.fer_so()

Bup bup!
Meu meu!
Bup bup!
```


### Polimorfisme en col·leccions

El **polimorfisme** és especialment útil quan treballes amb col·leccions (llistes, tuples, etc.).

Per exemple, una llista pot contindre diferents subclasses d’una mateixa classe base:

```python
llista = [Gos(), Gat(), Animal()]
```

I el programa pot executar el mateix mètode per a tots, sense fer condicions:

```python
for obj in llista:
    obj.fer_so()
```

Açò evita codi com:

```python
if isinstance(obj, Gos):
    ...
elif isinstance(obj, Gat):
    ...
```

### Duck typing

En Python, el **polimorfisme** també es basa molt en el concepte de **Duck Typing** que diu: «Si camina com un ànec i sona com un ànec, és un ànec».

Exemple:
```python
class Impressora:
    def imprimir(self):
        print("Imprimint document...")

class Pantalla:
    def imprimir(self):
        print("Mostrant en pantalla...")
```

Encara que *Impressora* i *Pantalla* no compartisquen cap herència, les dos tenen el mètode *imprimir()*. Per tant, podem fer:

```python
def executar_impressio(dispositiu):
    dispositiu.imprimir()

executar_impressio(Impressora())
executar_impressio(Pantalla())
```

```python
Imprimint document...
Mostrant en pantalla...
```

**EXEMPLE**

**Classe base**
```python
class Pagament:
    def pagar(self, quantitat):
        raise NotImplementedError("Aquest mètode s'ha d'implementar en una subclasse.")
```

**Subclasses**
```python
class PagamentTargeta(Pagament):
    def pagar(self, quantitat):
        print(f"Pagant {quantitat}€ amb targeta...")

class PagamentBizum(Pagament):
    def pagar(self, quantitat):
        print(f"Pagant {quantitat}€ amb Bizum...")

class PagamentEfectiu(Pagament):
    def pagar(self, quantitat):
        print(f"Pagant {quantitat}€ en efectiu...")
```

Ús polimòrfic:

```python
pagaments = [PagamentTargeta(), PagamentBizum(), PagamentEfectiu()]

for metode in pagaments:
    metode.pagar(50)
```

```
Pagant 50€ amb targeta...
Pagant 50€ amb Bizum...
Pagant 50€ en efectiu...
```

## Classes abstractes i interfícies

### Concepte

Una **classe abstracta** és una classe que servix com a base per a altres classes, definix una estructura comuna, i declara mètodes que han de ser implementats per les subclasses.

L'objectiu d’una **classe abstracta** és la de definir un contracte, de manera que totes les subclasses han de proporcionar una implementació real dels mètodes abstractes.

Una **classe abstracta** normalment no té sentit instanciar-la directament ja que no te definit tot el codi. El seu objectiu és actuar com un “contracte” o “model” per a les subclasses.

Implementar una **classe abstracta** és senzill. En el següent exemple la classe *Figura* declara un mètode *area()* abstracte (sense implementar).

```python
class Figura:
    def area(self):
        pass
```

Instanciar la classe anterior, encara que possible, no té cap sentit ja que el mètode *area()* no està implementat (pass).

```python
f = Figura()
print(f.area())
```

Un altre problema del codi anterior és que algú podria crear una subclasse i oblidar-se d’implementar *area()* i el programa fallaria en temps d’execució.

```python
class Rectangle(Figura):
    def __init__(self, ample, alt):
        self.ample = ample
        self.alt = alt
```
Ací *Rectangle* hereta *area()* però continua sense implementar-la. El programa no fallarà fins que intentem usar area().


### ABC i @abstractmethod

Python inclou el **mòdul abc** (*Abstract Base Classes*) per a definir classes abstractes.

Per fer-ho necessitem:

* Heretar d'**ABC**: Classe base per a crear classes abstractes.
* Marcar els mètodes abstractes com a obligatoris amb el decorador **@abstractmethod**.

```python
from abc import ABC, abstractmethod

class Figura(ABC):

    @abstractmethod
    def area(self):
        pass
```

**EXEMPLE**<br>
En el següent codi la classe *Figura* deriva d’**ABC**, el que indica que és un *classe abstracta*. El decorador *@abstractmethod* indica que *area()* és un mètode obligatori. Ara, qualsevol subclasse de *Figura* està obligada a implementar *area()*.

```python
from abc import ABC, abstractmethod

class Figura(ABC):
    @abstractmethod
    def area(self):
        pass
```

Si intentem instanciar *Figura* Python ho impedix i llançarà un error.

```python
class Rectangle(Figura):
    def __init__(self, ample, alt):
        self.ample = ample
        self.alt = alt

    def area(self):
        return self.ample * self.alt

class Cercle(Figura):
    def __init__(self, radi):
        self.radi = radi

    def area(self):
        return 3.1416 * self.radi ** 2
```

```python
figures = [Rectangle(5, 3), Cercle(2)]

for f in figures:
    print(f.area())
15
12.5664
```

Si una subclasse no implementa area(), Python no ens deixarà instanciar-la. Per exemple:

```python
class Triangle(Figura):
    def __init__(self, base, altura):
        self.base = base
        self.altura = altura

t = Triangle(3, 4)
TypeError: Can't instantiate abstract class Triangle with abstract method area
```

Una classe abstracta pot tindre tant mètodes abstractes (obligatoris) i també mètodes normals que ja estan implementats i s’hereten. Per exemple:

```python
from abc import ABC, abstractmethod

class Figura(ABC):

    @abstractmethod
    def area(self):
        pass

    def descriure(self):
        return f"Soc una figura amb àrea {self.area()}"
```

### Interfícies

En llenguatges com Java, una interfície és una estructura que definix mètodes obligatoris, però sense implementació. En Python no existix una paraula reservada *interface*, però podem fer el mateix amb una classe abstracta que només tinga mètodes abstractes

Exemple:
```python
from abc import ABC, abstractmethod

class Exportable(ABC):
    @abstractmethod
    def exportar(self):
        pass
```

Això actua com una interfície: qualsevol classe “exportable” ha d’implementar exportar()

**EXEMPLE**<br>
Suposem que volem exportar dades de diferents format com *json*, *csv* o *txt*.

```python
from abc import ABC, abstractmethod
import json

class Exportador(ABC):
    @abstractmethod
    def exportar(self, dades):
        pass
```

Subclasses:
```python
class ExportadorJSON(Exportador):
    def exportar(self, dades):
        return json.dumps(dades)

class ExportadorTXT(Exportador):
    def exportar(self, dades):
        return str(dades)
```

```python
exportadors = [ExportadorJSON(), ExportadorTXT()]
dades = {"nom": "Carles", "edat": 18}

for e in exportadors:
    print(e.exportar(dades))
```
