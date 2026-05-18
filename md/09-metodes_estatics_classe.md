# Mètodes estàtics i de classe

En Python, a més dels mètodes d’instància, existixen dos tipus de mètodes addicionals:

* **Mètodes estàtics** (@staticmethod)
* **Mètodes de classe** (@classmethod)

Recordem que un mètode d’instància rep *`self`* com a paràmetre i treballa amb les dades de l’objecte.

## Mètodes estàtics

Un **mètode estàtic** és un mètode que està dins d’una classe per organització, però no rep **`self`** ni **`cls`**, per la qual cosa no accedix directament a l’estat de l’objecte ni de la classe (a menys que se li passe com a paràmetre)

És com una funció normal, però col·locada dins d’una classe perquè té sentit semànticament que estiga allí.

Exemple:
```python
class Calculadora:
    @staticmethod
    def sumar(a, b):
        return a + b
```

```python
print(Calculadora.sumar(3, 4)) 	# 7
```

## Mètodes de classe

Els *mètodes d’instància* treballen amb l’estat de l’objecte i els **mètodes de classe** treballen amb informació comuna del tipus de classe.

Un **mètode de classe** rep com paràmetre **`cls`**, que és una referència a la classe (no a l’objecte com *`self`*), que li permet accedir a atributs de classe i també pot crear instàncies de manera controlada.

```python
class Alumne:
    centre = "IES Example"

    @classmethod
    def mostrar_centre(cls):
        print(f"Centre: {cls.centre}")

Alumne.mostrar_centre()		# Centre: IES Example
```

## Constructors alternatius: factories

Un dels usos més importants dels mètodes de classe és crear constructors alternatius, també anomenats **factory methods**.

**EXEMPLE**<br>
crear un alumne a partir d’un text.

```python
class Alumne:
    def __init__(self, nom, edat):
        self.nom = nom
        self.edat = edat

    @classmethod
    def from_string(cls, text):
        nom, edat = text.split(";")
        return cls(nom, int(edat))
```

```python
a = Alumne.from_string("Carles;18")
print(a.nom, a.edat)				# Carles 18
```


Fixem-nos que fem un **return cls()** i no un **return Alumne()**. Este detall és important quan usem herència. *cls* és la classe sobre la qual s’ha cridat el mètode, no necessàriament *Alumne*.

Això vol dir que: 

* Si cridem *Alumne.from_string(...)*, aleshores *`cls`* serà *Alumne*.
* Si cridem *AlumneBecat.from_string(...)*, aleshores *`cls`* serà *AlumneBecat*.

**EXEMPLE**

```python
class Alumne:
    def __init__(self, nom, edat):
        self.nom = nom
        self.edat = edat

    @classmethod
    def from_string(cls, text):
        nom, edat = text.split(";")
        return cls(nom, int(edat))

class AlumneBecat(Alumne):
    pass
```

```python
a1 = Alumne.from_string("Carles;18")
a2 = AlumneBecat.from_string("Maria;19")

print(type(a1))		# <class '__main__.Alumne'>
print(type(a2))		#  <class '__main__.AlumneBecat'>
```

Si férem:

```python
return Alumne(nom, int(edat))
```

```python
print(type(a2))		#  <class '__main__.Alumne'>
```

En este cas, encara que en este cas estem cridant el mètode correcte, este creant un **Alumne** normal enlloc d’un **AlumneBecat**.

