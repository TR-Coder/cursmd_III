# Principis SOLID
Els principis **SOLID** són un conjunt de recomanacions de disseny orientat a objectes que ajuden a crear programes. 

SOLID són les inicials de cinc principis:

* **S**: *Single Responsibility Principle*
* **O**: *Open/Closed Principle*
* **L**: *Liskov Substitution Principle*
* **I**: *Interface Segregation Principle*
* **D**: *Dependency Inversion Principle*


## S — Single Responsibility Principle (Responsabilitat única)

Una classe ha de tindre una única responsabilitat (una única tasca principal). Això significa que una classe no hauria de fer de tot, perquè es fa gran i difícil de mantindre i qualsevol canvi pot provocar errors en parts no relacionades.

**EXEMPLE**<br>
Enlloc d’una classe *Alumnes* que s’encarregue de guardar dades, calcular notes, guardar en arxiu, imprimir informes, etc.

És una bona pràctica, separar en classes més simples i més mantenibles:

- *Alumne*: dades de l’alumne

- *CalculadoraNotes*: càlculs

- *RepositoriAlumnes*: guardar/carregar dades

**EXEMPLE INCORRECTE**<br>
Una classe que fa massa coses

```python
class Alumne:
    def __init__(self, nom):
        self.nom = nom
        self.notes = []

    def afegir_nota(self, nota):
        if nota < 0 or nota > 10:
            raise ValueError("La nota ha d'estar entre 0 i 10.")
        self.notes.append(nota)

    def calcular_mitjana(self):
        if not self.notes:
            return 0
        return sum(self.notes) / len(self.notes)

    def guardar_en_fitxer(self, ruta):
        # Aquesta classe està fent també persistència (guardar dades)
        with open(ruta, "w", encoding="utf-8") as f:
            f.write(f"Nom: {self.nom}\n")
            f.write(f"Notes: {self.notes}\n")

    def imprimir_informe(self):
        # També està fent presentació (mostrar informe)
        print("----- INFORME -----")
        print(f"Alumne: {self.nom}")
        print(f"Notes: {self.notes}")
        print(f"Mitjana: {self.calcular_mitjana():.2f}")
```

Amb el codi anterior, si canvia el format de l’arxiu o de l’informe, hem de modificar *Alumne*, encara que l'alumne “com a model” no hauria de canviar.

**A)** Classe Alumne (només dades i comportament propi)

```python
class Alumne:
    def __init__(self, nom):
        self.nom = nom
        self.notes = []

    def afegir_nota(self, nota):
        if nota < 0 or nota > 10:
            raise ValueError("La nota ha d'estar entre 0 i 10.")
        self.notes.append(nota)
```

**B)** Classe *CalculadoraNotes* (només càlculs)

```python
class CalculadoraNotes:
    @staticmethod
    def mitjana(notes):
        if not notes:
            return 0
        return sum(notes) / len(notes)
```

**C)** Classe *RepositoriAlumnes* (guardar/carregar)

```python
class RepositoriAlumnes:
    @staticmethod
    def guardar(alumne, ruta):
        with open(ruta, "w", encoding="utf-8") as f:
            f.write(f"Nom: {alumne.nom}\n")
            f.write(f"Notes: {alumne.notes}\n")
```

**D)** Classe *InformeAlumne* (mostrar informació)

```python
class InformeAlumne:
    @staticmethod
    def imprimir(alumne):
        mitjana = CalculadoraNotes.mitjana(alumne.notes)

        print("----- INFORME -----")
        print(f"Alumne: {alumne.nom}")
        print(f"Notes: {alumne.notes}")
        print(f"Mitjana: {mitjana:.2f}")
```

```python
a1 = Alumne("Carles")
a1.afegir_nota(7)
a1.afegir_nota(9)

InformeAlumne.imprimir(a1)
RepositoriAlumnes.guardar(a1, "alumne_carles.txt")
```


## O — Open/Closed Principle (Obert/Tancat)

El codi ha d’estar obert per a ampliar-se però tancat per a modificar-se. És a dir, hem de poder afegir funcionalitats noves sense haver de tocar codi antic que ja funcionava.

**EXEMPLE INCORRECTE** (if/elif )<br>
Suposem que volem aplicar descomptes a un producte.

```python
class Producte:
    def __init__(self, nom, preu):
        self.nom = nom
        self.preu = preu
```

```python
class CalculadoraPreu:
    def preu_amb_descompte(self, producte, tipus, valor):
        if tipus == "fix":
            return producte.preu - valor

        elif tipus == "percentatge":
            return producte.preu * (1 - valor / 100)

        else:
            raise ValueError("Tipus de descompte no vàlid")
```

Amb este codi, cada vegada que apareix un descompte nou, hem de modificar la classe I afegir una nova branca elif. Estem tocant codi existent (i això pot trencar coses).

**EXEMPLE CORRECTE**<br>
Polimorfisme amb classes

```python
class Producte:
    def __init__(self, nom, preu):
        self.nom = nom
        self.preu = preu
```

Classe base Descompte (contracte)

```python
from abc import ABC, abstractmethod

class Descompte(ABC):
    @abstractmethod
    def aplicar(self, preu):
        pass
```

Descompte Fix

```python
class DescompteFix(Descompte):
    def __init__(self, quantitat):
        self.quantitat = quantitat

    def aplicar(self, preu):
        resultat = preu - self.quantitat
        return max(resultat, 0)  # evitem preus negatius
```


Descompte Percentatge

```python
class DescomptePercentatge(Descompte):
    def __init__(self, percentatge):
        self.percentatge = percentatge

    def aplicar(self, preu):
        return preu * (1 - self.percentatge / 100)
```

Calculadora de preu (no canvia mai)

Esta classe és «tancada a modificacions». No depén de tipus ni condicions.

```python
class CalculadoraPreu:
    def calcular(self, producte, descompte: Descompte):
        return descompte.aplicar(producte.preu)
```

```python
p = Producte("Teclat", 50)

calc = CalculadoraPreu()

d1 = DescompteFix(10)
print(calc.calcular(p, d1))  # 40

d2 = DescomptePercentatge(20)
print(calc.calcular(p, d2))  # 40
```

Ara podem afegir un nou descompte sense tocar la classe calculadora.

```python
class DescompteCompraMinima(Descompte):
    def __init__(self, minim, percentatge):
        self.minim = minim
        self.percentatge = percentatge

    def aplicar(self, preu):
        if preu >= self.minim:
            return preu * (1 - self.percentatge / 100)
        return preu
```

```python
d3 = DescompteCompraMinima(100, 15)

p2 = Producte("Monitor", 120)
print(calc.calcular(p2, d3))  # aplica el descompte

p3 = Producte("Ratolí", 30)
print(calc.calcular(p3, d3))  # no aplica res
```



## L — Liskov Substitution Principle (Substitució de Liskov)

Si una *classe B* hereta d’una *classe A*, qualsevol objecte de B s’ha de poder usar com si fora un D'A, sense trencar el programa.

Dit d’una manera més simple: una subclasse ha de comportar-se de manera coherent amb la classe pare.

**EXEMPLE 1**

**EXEMPLE INCORRECTE**<br>
Viola Liskov.

```python
class Pardal:
    def volar(self):
        print("L'ocell està volant")


class Pingui(Pardal):
    def volar(self):
        raise Exception("Un pingüí no pot volar")
```

```python
def fer_volar(pardal: Pardal):
    pardal.volar()

fer_volar(Pardal())
fer_volar(Pingui())
```
Quan executem *fer_volar(Pingui())* falla.

El problema és que *fer_volar()* espera que qualsevol pardal puga volar. Això viola Liskov perquè *Pingui* no pot substituir *Pardal* en qualsevol context on s’espera un pardal volador.

**SOLUCIÓ CORRECTA**<br>
Separem Pardal de la seua capacitat de volar.

```python
from abc import ABC, abstractmethod

class Ocell:
    pass

class Volador(ABC):
    @abstractmethod
    def volar(self):
        pass

class Pardal(Ocell, Volador):
    def volar(self):
        print("El pardal vola")

class Pingui(Ocell):
    def nadar(self):
        print("El pingüí neda")
```
```python
def fer_volar(volador: Volador):
    volador.volar()
```

**EXEMPLE 2**

**EXEMPLE INCORRECTE**<br>
Viola Liskov.

Classe base, permet retirar diners

```python
class CompteBancari:
    def __init__(self, titular, saldo=0):
        self.titular = titular
        self.saldo = saldo

    def retirar(self, quantitat):
        if quantitat <= 0:
            raise ValueError("La quantitat ha de ser positiva.")
        if quantitat > self.saldo:
            raise ValueError("Saldo insuficient.")
        self.saldo -= quantitat
```

Subclasse (impedix sempre retirar diners)

```python
class CompteBloquejat(CompteBancari):
    def retirar(self, quantitat):
        raise PermissionError("Compte bloquejat: no es pot retirar diners.")
```

Funció que espera un compte bancari normal

```python
def pagar_rebut(compte: CompteBancari, import_rebut):
    compte.retirar(import_rebut)
    print("Rebut pagat correctament.")
```

Prova amb un compte normal (funciona)

```python
c1 = CompteBancari("Carles", 100)
pagar_rebut(c1, 50)
print("Saldo restant:", c1.saldo)
```

Prova amb un compte bloquejat (falla)

```python
c2 = CompteBloquejat("Maria", 100)
pagar_rebut(c2, 50)
PermissionError: Compte bloquejat: no es pot retirar diners.
```

Viola Liskov perquè *pagar_rebut()* espera que qualsevol CompteBancari puga fer:

```text
compte.retirar(import)
```

I això és cert per al compte base, però no per a la subclasse.

**SOLUCIÓ CORRECTA**<br>
Complix Liskov

Interfície: 

```python
from abc import ABC, abstractmethod

class Retirable(ABC):
    @abstractmethod
    def retirar(self, quantitat):
        pass
```

Compte bancari normal (sí que és retirable)

```python
class CompteBancari(Retirable):
    def __init__(self, titular, saldo=0):
        self.titular = titular
        self.saldo = saldo

    def retirar(self, quantitat):
        if quantitat <= 0:
            raise ValueError("La quantitat ha de ser positiva.")
        if quantitat > self.saldo:
            raise ValueError("Saldo insuficient.")
        self.saldo -= quantitat
```

Compte bloquejat (NO és retirable)

Important: no hereta de Retirable.

un compte bloquejat pot existir, però no es pot utilitzar en llocs on es necessita retirar.

```python
class CompteBloquejat:
    def __init__(self, titular, saldo=0):
        self.titular = titular
        self.saldo = saldo
```

```python
Funció que paga rebuts (només accepta objectes retirable)
def pagar_rebut(compte: Retirable, import_rebut):
    compte.retirar(import_rebut)
    print("Rebut pagat correctament.")
```

Compte normal (funciona)

```python
c1 = CompteBancari("Carles", 100)
pagar_rebut(c1, 50)
print("Saldo restant:", c1.saldo)
```

Compte bloquejat (ara ni tan sols encaixa en el contracte)

```python
c2 = CompteBloquejat("Maria", 100)
pagar_rebut(c2, 50)	# AttributeError: 'CompteBloquejat' object has no attribute 'retirar'
```


## I — Interface Segregation Principle (Segregació d’interfícies)

És millor tindre diverses interfícies xicotetes i específiques que una interfície gran amb molts mètodes.

En Python, això s’aplica quan usem classes abstractes que actuen com a interfícies.

**EXEMPLE INCORRECTE**

Una interfície Dispositiu que obliga a implementar:
* imprimir()
* escanejar()
* enviar_fax()

Però després tenim una impressora simple que no pot escanejar ni enviar fax. Això obliga a implementar mètodes inútils.

**EXEMPLE CORRECTE**

Crear interfícies separades:

* Imprimible

* Escanejable

* Faxable

I cada classe implementa només el que necessita.

***

**EXEMPLE INCORRECTE**<br>
Una interfície massa gran obliga totes les classes a implementar mètodes que no necessiten.

```python
from abc import ABC, abstractmethod

class Dispositiu(ABC):
    @abstractmethod
    def imprimir(self, document):
        pass

    @abstractmethod
    def escanejar(self):
        pass

    @abstractmethod
    def enviar_fax(self, numero):
        pass
```

Impressora simple (obligada a implementar coses inútils). El codi que la use pot fallar si prova *escanejar()* o *enviar_fax()*.

```python
class ImpressoraSimple(Dispositiu):
    def imprimir(self, document):
        print(f"Imprimint: {document}")

    def escanejar(self):
        # No té sentit, però l'hem d'implementar per força
        raise NotImplementedError("Aquesta impressora no pot escanejar")

    def enviar_fax(self, numero):
        #  No té sentit, però l'hem d'implementar per força
        raise NotImplementedError("Aquesta impressora no pot enviar fax")
```

**EXEMPLE CORRECTE**<br>
Interfícies separades (xicotetes)

**A)** Imprimible

```python
from abc import ABC, abstractmethod

class Imprimible(ABC):
    @abstractmethod
    def imprimir(self, document):
        pass
```

**B)** Escanejable

```python
class Escanejable(ABC):
    @abstractmethod
    def escanejar(self):
        pass
```

**C)** Faxable

```python
class Faxable(ABC):
    @abstractmethod
    def enviar_fax(self, numero):
        pass
```

**Classes concretes**

*Impressora simple* (només imprimeix)

```python
class ImpressoraSimple(Imprimible):
    def imprimir(self, document):
        print(f"Imprimint: {document}")
```

*Escàner simple* (només escaneja)

```python
class EscanerSimple(Escanejable):
    def escanejar(self):
        print("Escanejant document...")
```

*Impressora multifunció* (imprimeix + escaneja + fax)

```python
class Multifuncio(Imprimible, Escanejable, Faxable):
    def imprimir(self, document):
        print(f"Imprimint: {document}")

    def escanejar(self):
        print("Escanejant document...")

    def enviar_fax(self, numero):
        print(f"Enviant fax al número {numero}...")
```

***
**EXEMPLE CORRECTE** (polimorfisme)

Funció que només necessita imprimir

```python
def imprimir_document(dispositiu: Imprimible, document):
    dispositiu.imprimir(document)
```

Funciona amb ImpressoraSimple i Multifuncio:

```python
i1 = ImpressoraSimple()
m1 = Multifuncio()
imprimir_document(i1, "examen.pdf")
imprimir_document(m1, "examen.pdf")
```

Funció que només necessita escanejar

```python
def escanejar_document(dispositiu: Escanejable):
    dispositiu.escanejar()
```

Funciona amb EscanerSimple i Multifuncio:

```python
e1 = EscanerSimple()
escanejar_document(e1)
escanejar_document(m1)
```

## D — Dependency Inversion Principle (Inversió de dependències)

Les classes d’alt nivell no haurien de dependre de classes concretes, sinó d’abstraccions.

D'altra manera:<br>
El codi ha de dependre de «què es pot fer» (una interfície), no de «qui ho fa» (una classe concreta).

**EXEMPLE**<br>
Si una classe *ServeiNotificacions* només funciona amb *Email*, tenim un sistema rígid. Millor és que funcione amb qualsevol classe que implemente enviar():

```python
class Email:
    def enviar(self, missatge):
        print(f"Enviant EMAIL: {missatge}")
```

Ara tenim una classe “important” que depén directament d’Email:

```python
class ServeiNotificacions:
    def __init__(self):
        self.email = Email()   # dependència directa

    def notificar(self, missatge):
        self.email.enviar(missatge)
```

Encara que este codi funciona, podem tindre diferents problemes:

**Problema 1**: Suposem que ara volem enviar un SMS.

```python
class SMS:
    def enviar(self, missatge):
        print(f"Enviant SMS: {missatge}")
```

Perquè *ServeiNotificacions* siga capaç d’enviar SMS hem de tocar el seu codi. I això passarà cada vegada que afegim un nou sistema d’enviament, com per exemple WhatsApp.

El DIP diu que la classe principal no ha de dependre d’una implementació concreta (Email o SMS), sinó d’una abstracció (alguna cosa que “sap enviar”).

En este cas es resol amb **Injecció de dependència** (*Dependency Injection*): Enlloc de crear l’*Email()* dins de *ServeiNotificacions*, li’l passem des de fora.

```python
class Email:
    def enviar(self, missatge):
        print(f"Enviant EMAIL: {missatge}")

class SMS:
    def enviar(self, missatge):
        print(f"Enviant SMS: {missatge}")

class ServeiNotificacions:
    def __init__(self, canal):
        self.canal = canal

    def notificar(self, missatge):
        self.canal.enviar(missatge)
```

```python
servei1 = ServeiNotificacions(Email())
servei1.notificar("Hola!")			# Enviant EMAIL: Hola

servei2 = ServeiNotificacions(SMS())
servei2.notificar("Hola!")			# Enviant SMS: Hola!
```

Millor encara, és usar interfícies:

```python
from abc import ABC, abstractmethod

class CanalNotificacio(ABC):
    @abstractmethod
    def enviar(self, missatge):
        pass

class Email(CanalNotificacio):
    def enviar(self, missatge):
        print(f"Enviant EMAIL: {missatge}")

class SMS(CanalNotificacio):
    def enviar(self, missatge):
        print(f"Enviant SMS: {missatge}")

class ServeiNotificacions:
    def __init__(self, canal: CanalNotificacio):
        self.canal = canal

    def notificar(self, missatge):
        self.canal.enviar(missatge)
```
