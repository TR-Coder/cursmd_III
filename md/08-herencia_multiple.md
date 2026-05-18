# Herència múltiple

## Introducció
L’herència múltiple significa que una classe derivada hereta de més d’una classe base i permet combinar funcionalitats de diverses classes.

Ara bé, l’herència múltiple pot comportar un sèrie de problemes. Per exemple, quan dos classes base tenen un mètode amb el mateix nom, no està clar quin s’ha d’executar.

```text
    A   B
     \ /
      C
```

```python
class A:
    def saludar(self):
        print("Salutació des de A")

class B:
    def saludar(self):
        print("Salutació des de B")

class C(A, B):
    pass

c = C()
c.saludar()		# Quina salutació apareix?
```

## MRO (Method Resolution Order)

Per a resoldre el possibles conflictes, Python definix un ordre en què buscar un atribut o un mètode quan el cridem sobre un objecte. Este ordre s'anomena **MRO** (*Method Resolution Order*) i es calcula seguint l’anomenant algoritme de *linealització C3*.

En general, quan fem una crida amb
```python
 obj.metode()
```

 se seguixen estos passos:

* Python busca *metode()* en la classe del propi objecte.
* Si no el troba, busca en les classes base en l’ordre marcat per l’*MRO*.
* Si tampoc el troba, continua avançant fins arribar a la classe object.
* Si no existix enlloc, llança un *AttributeError*.

Podem vore l'MRO d'una classe utilitzant l'atribut `.__mro__` o el mètode de classe `.mro()`.

**EXEMPLE**

```text
      A
     / \
    B   C
     \ /
      D
```

```python
class A:
    def saluda(self):
        print('Hola, sóc A')

class B(A):
    def saluda(self):
        print('Hola, sóc B')
    
class C(A):
    def saluda(self):
        print('Hola, sóc C')
    
class D(B,C):       # Com l'ordre de declaració és B,C es mira primer
    pass            	     # els atributs/mètodes de B (de la rama de B)
```

```python
hola = D().saluda() # 'Hola, sóc B'
print(D.mro())
[<class '__main__.D'>, <class '__main__.B'>, <class '__main__.C'>, <class '__main__.A'>, <class 'object'>]
```

**EXEMPLE**

```text
   A1  A2
    |   |
   B1  B2
     \ /
      C
```

```python
class A1:
    def saluda(self):
        print('Hola, sóc A1')

class A2:
    def saluda(self):
        print('Hola, sóc A2')

class B1(A1):
    pass
        
class B2(A2):
    pass
    
class C(B1,B2):     # Com l'ordre de declaració és B1, B3 es mira primer
    pass                   # la rama de B1  
```

```python
hola = C().saluda()     # 'Hola, sóc A1'

print(C.mro())
[<class '__main__.C'>, <class '__main__.B1'>, <class '__main__.A1'>, <class '__main__.B2'>, <class '__main__.A2'>, <class 'object'>]
```

## Problema del diamant

Vegem el clàssic problema del diamant (*diamon problem*)

Suposem unes classe A, B, C i D que mantenen la següent forma d’herència:

```text
      A
     / \
    B   C
     \ /
      D
```

És a dir:

```text
class A: ...
class B(A): ...
class C(A): ...
class D(B, C): ...
```

Analitzem diferents problemes que es poden donar:

#### Conflicte per heretar el mateix mètode
```python
class A:
    def saludar(self):
        print("Hola des de A")

class B(A):
    pass

class C(A):
    pass

class D(B, C):
    pass
```

Quan fem:

```python
d = D()
d.saludar()
```

Quin s’executa? Segon l’RMO es D

```text
D -> B -> C -> A -> object
```

#### B i C redefineixen el mateix mètode

```python
class A:
    def saludar(self):
        print("A")

class B(A):
    def saludar(self):
        print("B")

class C(A):
    def saludar(self):
        print("C")

class D(B, C):
    pass
```

Segon l’*RMO* s’executarà el B. Python busca primer en D, després en B (i el troba allí). Per tant, C ni s’arriba a mirar

#### Crides duplicades al pare

El problema més greu és quan les classes criden al pare i podrien acabar executant-lo dos vegades. El problema del diamant en essència que múltiples camins d’herència fan que el mateix «pare comú» puga executar-se més d’una vegada.

El següent exemple mostra que en una jerarquia en forma de diamant és possible arribar al mateix pare comú (A) per dos camins diferents, i per tant, el mètode de A pot acabar executant-se més d’una vegada.

```python
class A:
    def proces(self):
        print("A")

class B(A):
    def proces(self):
        print("B")
        A.proces(self)

class C(A):
    def proces(self):
        print("C")
        A.proces(self)

class D(B, C):
    def proces(self):
        print("D")
        B.proces(self)
        C.proces(self)
```

```python
d = D()
d.proces()		#  D  B  A  C  A	A s’executa 2 vegades
```


**IMPORTANT**<br>
**super()** NO significa «*crida al pare directe*», sinó «*crida la següent classe segons l’*MRO**». Això es nota sobretot en herència múltiple.


```python
class A:
    def proces(self):
        print("A")

class B(A):
    def proces(self):
        print("B")
        super().proces()

class C(A):
    def proces(self):
        print("C")
        super().proces()

class D(B, C):
    def proces(self):
        print("D")
        super().proces()
```

```python
d = D()
d.proces()		# D → B → C → A
```

Quan hi ha herència múltiple no convé cridar directament a una classe pare:

```python
A.proces(self)
```

Perquè això se «salta» l’*MRO* i pot causar crides duplicades, crides en ordre incorrecte o classes que es queden sense executar.

Com es veu, en un disseny amb herència múltiple, el problema del diamant es manifesta sobretot quan una classe redefineix un mètode X(), i dins de X() vol «aprofitar» o «completar» el comportament cridant també el X() del pare, i això passa en diverses branques de la jerarquia (B i C), que acaben cridant al mateix pare comú (A). En este cas, si les crides no són «cooperatives», A.X() poden executar-se més d’una vegada.

