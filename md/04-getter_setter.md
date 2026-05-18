# Getter, setter i @property

Els **getters** i els **setters** són mètodes que s’utilitzen per accedir (lectura) i modificar (escriptura) els atributs d’un objecte d’una manera controlada.

La idea és que, per aplicar el concepte d’encapsulació, l’estat intern d’un objecte no s’hauria de manipular directament des de fora de la classe. Per això, és habitual definir els atributs com a interns (o privats) i fer que qualsevol accés es realitze a través de mètodes específics. D’esta manera, s’evita que qualsevol part del programa puga modificar dades directament i sense control, cosa que podria deixar l’objecte en un estat inconsistent.

En este sentit:

* Un **getter** permet llegir el valor d’un atribut.
* Un **setter** permet modificar el valor d’un atribut, normalment aplicant validacions o regles abans d’acceptar el canvi.

En Python, la manera més habitual de definir **getters** i **setters** és mitjançant propietats, utilitzant els següents decoradors:

- **@property**, per al *getter*.
- **@atribut.setter**, per al *setter*.

Això permet accedir als valors com si foren atributs normals, però mantenint el control intern propi de l’encapsulació.

## @property

El decorador **@property** permet convertir un mètode en una propietat, de manera que es puga accedir com si fora un atribut, però executant codi intern. Des de fora, el mètode sembla un atribut normal.

```python
class Alumne:
    def __init__(self, nom):
        self.__nom = nom

    @property
    def nom(self):
        return self.__nom

a = Alumne("Carles")
print(a.nom)			# a.nom sembla un atribut, però en realitat és un mètode.
```

## @nom.setter

El següent codi permet modificar *nom*, però si el valor és incorrecte, el programa llançarà una excepció.

```python
class Alumne:
    def __init__(self, nom):
        self.__nom = nom

    @property
    def nom(self):
        return self.__nom

    @nom.setter
    def nom(self, nou_nom):
        if nou_nom == "":
            raise ValueError("El nom no pot estar buit.")
        self.__nom = nou_nom

a = Alumne("Carles")
a.nom = "Maria"
print(a.nom)
```

## Propietat de només lectura

De vegades, volem que un atribut es puga consultar però no modificar directament. Per a fer una propietat de només lectura, definim **@property**, però no definim el **setter**.

```python
class Alumne:
    def __init__(self, nom, dni):
        self.__nom = nom
        self.__dni = dni

    @property
    def dni(self):
        return self.__dni

a = Alumne("Carles", "12345678A")
print(a.dni)

a.dni = "99999999Z"  # ERROR, dni no té setter.
```

**NOTA**<br>
Normalment els nom dels decoradors i mètodes *setter* i *getter* coincidixen amb el de la propietat interna que manipulen, encara que no és obligatori.

En el següent exemple, fixem-nos amb la coincidència en el nom de les funcions *ff(self)*, *ff(self,x)* i el decorador *@ff.setter*. S'han posat estos noms tant estranys per qüestions didàtiques.

```python
class Base:
    def __init__(self, n):
        self.nom_ocult = n

    @property
    def ff(self):
        print('Dins getter')
        return self.nom_ocult

    @ff.setter
    def ff(self, x):
        print('Dins setter')
        self.nom_ocult = x

base = Base('ordinador')
print(base.ff)				# Dins getter	ordinador
base.ff = 'impressora'		# Dins setter
print(base.ff)				# Dins getter	impressora
```

## Propietats calculades

Una propietat també pot ser un valor calculat a partir d’altres atributs, sense necessitat de guardar-la físicament.

**EXEMPLE**<br>
Una propietat *adult* que indica si una persona és major d’edat.

```python
class Persona:
    def __init__(self, nom, edat):
        self.nom = nom
        self.edat = edat

    @property
    def adult(self):		#  és un atribut guardat, sinó una propietat calculada.
        return self.edat >= 18

p = Persona("Maria", 17)
print(p.adult)  # False
```

**EXEMPLE**

```python
class CompteBancari:
    def __init__(self, titular, saldo_inicial=0):
        if saldo_inicial < 0:
            raise ValueError("El saldo inicial no pot ser negatiu.")

        self.titular = titular
        self.__saldo = saldo_inicial

    @property
    def saldo(self):
        return self.__saldo

    def ingressar(self, quantitat):
        if quantitat <= 0:
            raise ValueError("La quantitat ha de ser positiva.")
        self.__saldo += quantitat

    def retirar(self, quantitat):
        if quantitat <= 0:
            raise ValueError("La quantitat ha de ser positiva.")
        if quantitat > self.__saldo:
            raise ValueError("Saldo insuficient.")
        self.__saldo -= quantitat
```

```python
c = CompteBancari("Carles", 100)
print(c.saldo)   # podem consultar-lo

c.ingressar(50)
print(c.saldo)

c.retirar(30)
print(c.saldo)

c.saldo = 9999   # ERROR: no hi ha setter
```
