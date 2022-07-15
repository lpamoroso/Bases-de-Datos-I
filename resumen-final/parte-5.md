# Proceso de normalización

## Glosario

+ **DF**: dependencia funcional.
+ **FN**: forma normal.
+ **1FN**: Primera Forma Normal.
+ **2FN**: Segunda Forma Normal.
+ **3FN**: Tercera Forma Normal.
+ **BCNF**: _Boyce Codd Normal Form_.
+ **CC**: clave candidata.

## Introducción

El **proceso de normalización** garantiza relaciones que cumplan **ciertas condiciones de un buen diseño** (ausencia de anomalías). La forma de hacerlo es a través de la **descomposición de un esquema**, es decir, **separar** los atributos de una **relación** en **dos nuevas relaciones** (siempre siguiendo ciertos criterios). Durante este procedimiento no se debe perder **información** ni **DFs**.

La idea es la siguiente: dado un esquema R donde vale la DF _X->Y_, R se descompone en R1(<u>X</u>,Y) y R2(R-Y). Luego del particionamiento, ha de verificarse que no se ha perdido información y DFs de la siguiente forma:

+ **Información**: al particionar un esquema R en dos subesquemas R1 y R2, se debe cumplir alguna de las siguientes condiciones:
	- R1 ∩ R2 clave en el esquema R1.
	- R1 ∩ R2 clave en el esquema R2.
+ **DFs**: al particionar un esquema R en dos subesquemas R1 y R2, verificar que cada una de las DFs que valían en el esquema R **sigan valiendo en alguna de esas particiones**. Durante ese chequeo pueden pasar dos cosas:
	- Los atributos de las DF original quedaron todos incluídos en **alguna** de las particiones generadas. En este caso, basta una **validación simple** que corrobora que la DF sigue valiendo.
	- Los atributos de la DF original quedaron distribuídos en **más de una partición**. En este caso, hace falta una **validación formal mediante un algoritmo** para chequear que la DF siga valiendo y no se haya perdido.

## Formas normales (FNs)

Una forma normal (FN) es un **criterio** para determinar **cierto grado de vulnerabilidad** ante ciertas **inconsistencias** o **anomalías**. Hay cuatro FNs y cuanto **mayor** sea la FN, **menos vulnerabilidades** tendrá el esquema.

+ **Primera Forma Normal (1FN)**: el esquema no debe tener atributos polivalentes o compuestos. Es la forma de la que partimos para normalizar.
+ **Segunda Forma Normal (2FN)**.
+ **Tercera Forma Normal (3FN)**.
+ **FN de Boyce y Codd (_BCNF_)**: esta FN **libra de todas las anomalías** (excepto la **redundancia**), **conserva la información** y, en algunos casos, **asegura que no se pierden DFs**.

	Se dice que un esquema está en **BCNF** si, siempre que una DF de forma X->A sea válida en R, entonces se cumple que **X es superclave de R**, en particular una superclave _minimal_ o bien **X->A es una DFT**, es decir, que A esté incluído en X.

	Sabiendo que las FNs sacan anomalías y que a mayor FN, menos anomalías, entonces la idea va a ser siempre **intentar llevar el esquema a BCNF**.

En reglas generales, si un esquema está en alguna FN, **también está en las FN que la preceden**. Esto significa, por ejemplo, que si un esquema está en **BCNF**, **también está en 3FN, 2FN y 1FN**.

## ¿Cómo normalizar?

A priori, hay que tener en cuenta lo siguiente:

+ Las **DFs** del esquema.
+ **CCs**.
+ La definición de **BCNF**.
+ La descomposición del esquema.

En este caso, vamos a partir de un esquema que ya está en 1FN. El esquema es ```FIESTAS (#salon, direccion, capacidad, fecha_fiesta, nombre_invitado, mesa_invitado, servicio_contratado, dni_invitado)``` y su CC es ```(#salon, fecha_fiesta, dni_invitado, servicio_contratado)```.

Las DFs son:
1. ```#salon -> direccion, capacidad```
2. ```#salon, fecha_fiesta, dni_invitado -> mesa_invitado```
3. ```dni_invitado -> nombre_invitado```

¿FIESTAS cumple con la definición de BCNF? Recordando [la definición de BCNF](##Formas-normales-(FNs)), ya analizando la DF1 se puede ver que {#salon} no es superclave del esquema FIESTAS. Entonces empiezo a particionar considerando la DF1:

1. ```#salon -> direccion, capacidad```

```
F1 (--#salon--, direccion, capacidad)
F2 = FIESTAS - F1
```

Es decir, F2 me queda así: F2 (<u>#salon</u>, <u>fecha_fiesta</u>, nombre_invitado, mesa_invitado, <u>servicio_contratado</u>, <u>dni_invitado</u>)

Con este particionamiento ¿Perdí **información** o **DFs**? F1 ∩ F2 es **clave** del esquema, o sea que **no perdí información**. Con las DFs, en este caso, la **validación simple** basta: en F1 vale DF1 y en F2 vale DF2 y DF3. Entonces puedo concluir que **tampoco perdí DFs**. Entonces, el particionamiento que hice es **válido**, solo resta **chequear** que ambas **particiones** estén en **BCNF**.

```
F1 (--#salon--, direccion, capacidad)
F2 (--#salon--, --fecha_fiesta--, nombre_invitado, mesa_invitado, --servicio_contratado--, --dni_invitado--)
```

¿F1 y F2 cumplen con la definición de BCNF?

En F1, vale **solo** la DF1 y se cumple que {#salon} es **superclave**, entonces **F1 cumple la definición de BCNF**.

En F2 valen la DF2 y DF3. Más específicamente, {dni_invitado} **no es superclave** en F2. Entonces, F2 **no cumple** con la definición de BCNF, por lo que **descompongo** F2 considerando la DF3.

```
F3 (--dni_invitado--, nombre_invitado)
F4 (--#salon--, --fecha_fiesta--, mesa_invitado, --servicio_contratado--, --dni_invitado--)
```

Con este particionamiento ¿Perdí **información** o **DFs**? F3 ∩ F4 es clave del esquema, o sea que **no perdí información**. Con las DFs, la **validación simple** basta: en F3 vale DF3 y en F4 vale DF2. Entonces puedo concluir que **tampoco perdí DFs**. Entonces, el particionamiento que hice es **válido**, solo resta **chequear** que ambas **particiones** estén en **BCNF**.

¿F3 y F4 cumplen con la definición de BCNF?

En F3, vale **solo** la DF3 y se cumple que {dni_invitado} es **superclave**, entonces F3 **cumple** la definición de BCNF.

En F4 vale la DF2. En particular, {#salon, fecha_fiesta, dni_invitado} **no es superclave** en F4. Entonces, F4 **no cumple** con la definición de BCNF, por lo que **descompongo** F4 considerando la DF2.

```
F5 (--#salon--, --fecha_fiesta--, --dni_invitado--, mesa_invitado)
F6 (--#salon--, --fecha_fiesta--, --servicio_contratado--, --dni_invitado--)
```

Con este particionamiento ¿Perdí **información** o **DFs**? F5 ∩ F6 es **clave** del esquema, o sea que **no perdí información**. Con las DFs, la **validación simple** basta: en F5 vale DF2 y en F6 todos los atributos forman parte de la clave. Entonces puedo concluir que **tampoco perdí DFs**. Entonces, el particionamiento que hice es válido, solo resta chequear que ambas particiones estén en BCNF.

¿F5 y F6 cumplen con la definición de BCNF?

En F5, vale **solo** la DF2 y se cumple que {#salon, fecha_fiesta, dni_invitado} es **superclave**, entonces F5 **cumple** la definición de BCNF.

En F6 **todos los atributos forman parte de la clave**. Esto significa que **cualquier dependencia funcional** que detecte va a ser **trivial**. Entonces F6 **cumple** con la definición de BCNF.

Por último, así queda el esquema FIESTAS en BCNF:

```
F1 (--#salon--, direccion, capacidad)
F3 (--dni_invitado--, nombre_invitado)
F5 (--#salon--, --fecha_fiesta--, --dni_invitado--, mesa_invitado)
F6 (--#salon--, --fecha_fiesta--, --servicio_contratado--, --dni_invitado--)
```

En resumen, el **algoritmo para normalizar** es el que sigue:

1. Analizar si en el esquema R existe alguna DF que lleva al esquema a no cumplir con la definición de BCNF.
   1. Si existe tal DF, particionar el esquema en dos nuevos esquemas Ri y Ri+1, contemplando la DF en cuestión. Para las dos particiones generadas:
      1. ¿Se pierde información?
         1. No, entonces sigo a 1.1.2.
         2. Si, entonces la partición está mal hecho. Reanalizar.
      2. ¿Se pierden DFs?
         1. No, entonces sigo a 1.1.3.
         2. Si, entonces no es posible llevar a BCNF. Cambiar la FN analizada.
      3. Determinar en qué FN está Ri y Ri+1.
         1. Si hay alguna que no esté en BCNF, reiniciar desde 1.
         2. Si ambas están en BCNF, ir a 1.2.
   2. Si no existe, el esquema está en BCNF.

## Validación por algoritmo

En el proceso anterior, la validación fue **simple en todo momento**, es decir que, luego del particionamiento, la DF queda en una partición o en la otra. Lo cierto es que también puede pasar que la **DF quede repartida entre las dos particiones**. Ejemplo:

**R**(a,b,c,d) y vale **F**={a->b, b->c, c->d, d->a}

Alguien propone el siguiente particionamiento:

```
R1(a,b)
R2(b,c)
R3(c,d)
```

Se puede apreciar que **no se pierde información**, pero con las DFs pasa lo siguiente:

+ a->b vale en R1
+ b->c vale en R2
+ c->d vale en R3
+ d->a ?????

Lo que pasa acá es que **no se puede determinar** _a ojo_(mediante validación simple) si se perdió o no la DF, ya que los atributos quedaron repartidos en **diferentes particiones**. Así, para **verificar efectivamente** que no hayamos perdido DFs hay que aplicar el **algoritmo** que sigue:

```
res = x
while (res cambió)
	for i=1; i <= cant_particiones; i++
		res = res ∪ ((res ∩ Ri)+ ∩ Ri)
```

Siendo:

+ **x** el determinante de la DF que quiero analizar si se perdió.
+ **res** un temporal donde voy guardando los atributos que se pueden recuperar en cada iteración.
+ **Ri** el conjunto de atributos de la partición representada por Ri.
+ **((res ∩ Ri)+ ∩ Ri)** asegura que solo quedan los atributos que pertenecen a la partición que se está tratando.

La idea del algoritmo es la siguiente: iterar sobre las particiones realizadas e intentar recuperar los atributos de la DF que creo haber perdido.

**NOTA**: el que se use este algoritmo en estos casos **no significa** que no pueda aplicarse cuando **la DF quedó en una sola partición**, **sí puede usarse** pero **no es tan llevadero como hacer una validación simple**. Conviene usarlo solo en estos casos que **no es tan obvio si se perdió o no la DF**. De esta forma:

+ Si luego de seguir el algoritmo para detectar pérdida de dependencias, **se logra incluir en res** todos los atributos de la DF que suspechaba haber perdido, entonces **no perdí la DF** y **puedo continuar** con el análisis de BCNF.
+ **Caso contrario, sí perdí la DF** y **no puedo continuar** con el análisis de BCNF.

Aplicado al ejemplo:

```
res = d

i=1
res = d ∪ ((d ∩ {a,b})+ ∩ {a,b}) = d

i=2
res = d ∪ ((d ∩ {b,c})+ ∩ {b,c}) = d

i=3
res = d ∪ ((d ∩ {c,d})+ ∩ {c,d})
res = d ∪ ((d)+ ∩ {c,d})
res = d ∪ ({a,b,c,d} ∩ {c,d})
res = d ∪ {c,d} = **{c,d}**
```

Itero de nuevo porque res cambió

```
i=1
res = {c,d} ∪ (({c,d} ∩ {a,b})+ ∩ {a,b}) = {c,d}

i=2
res = {c,d} ∪ (({c,d} ∩ {b,c})+ ∩ {b,c})
res = {c,d} ∪ ((c)+ ∩ {b,c})
res = {c,d} ∪ ({a,b,c,d} ∩ {b,c})
res = {c,d} ∪ {b,c} = {c,d,b}

i=3
res = {c,d,b} ∪ (({c,d,b} ∩ {b,c})+ ∩ {b,c}) = **{c,d,b}**
```

Itero de nuevo porque res cambió

```
i=1
res = {c,d,b} ∪ (({c,d,b} ∩ {a,b})+ ∩ {a,b}) = **{c,d,b,a}**
```

Notar que logré llegar a **a** a partir de **d**, por lo que no pierdo la DF. Ejemplo de cuando pierdo la DF:

**LIBROS**(titulo,teatro,ciudad), vale F={teatro->ciudad, (titulo, ciudad)->teatro}
y cc1={titulo,ciudad}, cc2={teatro,titulo}

¿Cumple **LIBROS** con la definición de **BCNF**? No, dado que existe la DF1 tal que {teatro} **no es superclave** del esquema. Particiono en:

L1(teatro,ciudad)
L2(teatro,titulo)

Ahora bien, **¿Perdí información?** No, ya que L1 ∩ L2 es clave del esquema L1{teatro}. **¿Perdí DFs?** En L1 vale DF1, **pero andá a saber si DF2 también vale**...

Aplico entonces el **algoritmo** para saber si la perdí efectivamente:

```
res = {titulo,ciudad}

i=1
res = {titulo,ciudad} ∪ (({titulo,ciudad} ∩ {titulo,ciudad})+ ∩ {titulo,ciudad})
res = {titulo,ciudad} ∪ ((ciudad)+ ∩ {titulo,ciudad})
res = {titulo,ciudad} ∪ ({∅} ∩ {titulo,ciudad})
res = {titulo,ciudad} ∪ {∅} = {titulo,ciudad}

i=2
res = {titulo,ciudad} ∪ (({titulo,ciudad} ∩ {teatro,titulo})+ ∩ {teatro,titulo})
res = {titulo,ciudad} ∪ ((titulo)+ ∩ {teatro,titulo})
res = {titulo,ciudad} ∪ ({∅} ∩ {{teatro,titulo}})
res = {titulo,ciudad} ∪ {∅} = {titulo,ciudad}
```

Esas eran las dos particiones de LIBROS y res no cambió, por lo que la DF2 efectivamente se perdió durante el particionamiento. En estos casos (más específicamente, **cuando no puedo llevar a BCNF**) se lleva el esquema a 3FN.

## Tercera Forma Normal (3FN)

Se dice que un esquema está en 3FN si para toda DF de la forma X->A, se cumple que:
+ X->A es **trivial**.
+ X es **superclave**.
+ A es **primo**, siendo primo un atributo que forma parte de alguna CC.

Así, el esquema 3FN asegura que:

+ No se pierde **información**.
+ No se pierden **DFs**.
+ Quedan algunas **anomalías**.

### ¿Cómo llevar a 3FN?

Habiendo determinado que no se puede llevar el esquema a BCNF:

+ Construir una **tabla** por **cada** DF.
+ Si la **clave** de la **tabla original** no está incluída en ninguna de las tablas del punto anterior, **construir una tabla** con esa **clave**.

Volviendo al ejemplo de LIBROS, intenté llevarlo a BCNF y no se pudo. Para intentar llevar a 3FN, siendo que F={teatro->ciudad, (titulo, ciudad)->teatro}, construir una tabla por cada DF:

```
L1.1 (teatro, ciudad)
L1.2 (teatro, titulo, ciudad)
```

Ver que en este caso la clave quedó en una de las particiones y por eso no se agrega una nueva partición con ella.

## 3FN vs. BCNF

Habiendo analizado cómo llevar a 3FN y cómo llevar a BCNF, el hecho es que **llevar a 3FN es mucho más fácil que BCNF**. Sin embargo, recordar que **BCNF es mucho más restrictivo y evita más anomalías que 3FN**. Es por eso que es conveniente llevar a 3FN **solo** en el caso en que no pueda llevar a BCNF.

## Otras FN: 1FN y 2FN

+ 1FN: los atributos de la relación son simples y atómicos.
+ 2FN: para toda DF de la forma X->A se cumple que A depende de manera total de la clave. Ejemplo:

	Teniendo **EMPLEADOS**(#empleado, #area, nombre, nombre_area, años), siendo CC (#empleado, #area) y F = {#empleado->nombre, #area->nombre_area, (#empleado, #area)->años} se puede decir que **EMPLEADOS** no está en 2FN porque existen atributos que no dependen funcionalmente de toda la clave de empleados (en la DF1, _nombre_ no depende funcionalmente de toda la clave).