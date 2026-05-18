# Encapsulació

L’**encapsulació** és un dels pilars fonamentals de la OPP. Consistix a agrupar dades i comportament dins d’un objecte, i controlar com s’accedix i es modifica l’estat intern d’eixe objecte.

La idea principal és que un objecte ha de ser com una «caixa negra». Des de fora podem utilitzar-lo, però no hem de manipular directament el seu interior sense control. Això evita molts errors i fa que el codi siga més mantenible.

En programes xicotets pot semblar que no és tan important, però en projectes grans és essencial, perquè ajuda a evitar modificacions incorrectes de dades i permet modificar la implementació interna sense trencar altres parts del programa.

La següent assignació, encara que correcta, podria ser un problema perquè una edat negativa no té sentit i l’objecte queda en un estat incorrecte

```python
a1.edat = -5
```

L’encapsulació pretén evitar que qualsevol part del programa puga canviar l’estat intern de manera arbitrària.

L’accés als atributs en Python no és com en altres llenguatges com Java o C++ on l’accés als atributs que delimitat per paraules reservades com: *private*, *protected* i *public*.

La filosofia de Python és la de no limitar l’accés als atributs, assumint que el programador sap el que està fent. Enlloc de limitar, usa **convencions** per indicar quin ús és el recomanat.

## Atributs protegits

Quan un atribut comença amb un guió baix indica que no l'hauríem de tocar des de fora de la classe, a menys que siguem conscients del que fem. No és una protecció real, però és una convenció molt utilitzada.

**EXEMPLE**

```python
class CompteBancari:
    def __init__(self, titular, saldo):
        self.titular = titular
        self._saldo = saldo
```

Ací, *saldo* es considera **intern** i el codi extern no hauria de fer:

```python
compte._saldo = 999999		# Es pot fer, però no és una bona pràctica.
```

## Atributs privats (name mangling)

Si escrivim un atribut amb doble guió baix:

```python
self.__pin
```

Python li aplica un mecanisme anomenat **name mangling**, que canvia internament el nom de l’atribut per dificultar el seu accés des de fora.

```python
class Targeta:
    def __init__(self, pin):
        self.__pin = pin

t = Targeta(1234)
print(t.__pin)			# AttributeError
```

El **name mangling** que consistix a modificar el nom de l'atribut amb un prefix _Classe, on Classe és el nom de la classe.

Tot i això, l’atribut no es convertix en realment privat, ja que continua sent accessible utilitzant el nom intern generat per Python. En el següent exemple, es veu com sabent com es reanomenen les variables som capaços d'accedir a ella.

```python
class Base:
    def __init__(self, nom):
        self.__nom = nom

b = Base('ordinador')		# ordinador
print(b._Base__nom)
```