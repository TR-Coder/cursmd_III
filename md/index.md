# Introducció a la OPP

## Conceptes
La **Programació Orientada a Objectes** (**OOP**, de l’anglés *Object-Oriented Programming*) és un paradigma de programació que proposa una manera d’organitzar el codi basada en la idea de representar elements del món real (o del problema que volem resoldre) mitjançant **objectes**.

Un **objecte** és un element d’un programa que agrupa:

* **Dades**: Informació que descriu l’objecte.

* **Comportament**: Accions que l’objecte pot realitzar.

Esta manera de treballar i d’organitzar el codi és especialment útil quan es volen construir programes grans i complexos, perquè facilita la separació de responsabilitats, la reutilització de codi i el manteniment del programari.

Fins ara, hem estudiat el que s’anomena **programació estructurada**, on els programes s’organitzen com un conjunt de funcions que treballen sobre unes dades.

Per exemple, podem tindre la informació sobre alumnes, i un conjunt de funcions com *calcular_mitjana()*, *mostrar_dades()*, *afegir_nota()*, etc., que operen sobre eixos alumnes. El problema és que, segons el programa creix, les dades i les funcions poden quedar disperses en diferents parts del codi, i resulta difícil controlar què modifica què, així com garantir que el programa continue sent coherent.

En canvi, en l’**OOP** el programa s’organitza com una col·lecció d’objectes que:

* Guarden les seues pròpies dades (**estat intern**).

* Tenen **mètodes** per operar amb eixes dades.

És a dir, les dades i les funcions queden agrupades dins d’una mateixa estructura.

Este enfocament aporta una manera de programar més pròxima a com pensem molts problemes reals. No pensem només en *funcions*, sinó en *entitats* (alumnes, productes, comptes, usuaris, etc.) que tenen propietats i realitzen accions.

## Terminologia

Una **classe** és com una plantilla que definix:

* **Atributs**: Unes **dades** relacionades entre elles (*variables*)

* **Mètodes**: Unes accions que permeten treballar sobre eixes dades (*funcions*).

Per exemple: <br>
Una classe *Alumne* pot estar definida per atributs com el seu *nom*, *edat*, *dni*, *assignatures* i les *notes* obtingudes. Per a consultar o modificar estos *atributs*, es fa a través de mètodes integrats en la pròpia classe, com per exemple *afegir_nota()* o *calcular_nota_final()*. Estos mètodes poden accedir i manipular directament l’**estat intern** de l’objecte.

L'**estat d’un objecte** és el conjunt de valors que tenen els seus atributs en un moment determinat de l’execució.

Com es veu, no parlem de _variables_ i _funcions_ com en la programació estructurada, sinó de:

* **Atributs** (propietats o característiques).

* **Mètodes** (accions o comportaments).

Els atributs i els mètodes, en conjunt, s’anomenen **membres d’una classe**.

La classe *Alumne*, per tant, és una definició genèrica que establix un conjunt d’*atributs*, que representen l’*estat* d'un *Alumne*, i un conjunt de *mètodes*, que representen el seu comportament.

A partir d’esta plantilla es poden crear casos particulars, els **objectes**. D’esta manera, podem generar un alumne *Carles* i un altre *Maria*, i cadascun d’ells tindrà el seu propi estat independent de l’altre: el seu nom, la seua edat, el seu dni, les seues assignatures i les seues notes. Això significa que els canvis en l’alumne *Carles* no afecten *Maria*, i viceversa.

*Alumne* és una classe. *Carles* i *Maria* són **objectes** creats a partir de la classe *Alumne*. El procés de crear un objecte a partir d’una classe s’anomena **instanciar** la classe, i per això es diu que *Carles* i *Maria* són **instàncies de la classe Alumne**.


