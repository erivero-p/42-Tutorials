# Operadores de bits para pencas 🌵
Deberás saber cómo trabajar con bits para proyectos como [Minitalk](https://github.com/erivero-p/Minitalk-w-Tutorial-), o el [examen de rango 02](https://github.com/erivero-p/exam_rank02)

### Sobre los bits

Un bit (binary digit) es la unidad de medida de información más pequeña, es un dígito en sistema de numeración binario, representado con dos valores: 0 y 1. Se utilizará para contraponer dos valores, por ejemplo, apagado y encendido. Será la forma en que se maneje toda la información en un sistema informático.

### Sobre el sistema binario

En cualquier sistema numérico posicional, cada dígito en una posición determinada de un número representa una potencia de la base elevada a esa posición. Por ejemplo, en el sistema decimal, el número 123 se puede descomponer en 1x10^2 + 2x10^1 + 3x10^0.

En el caso del sistema binario, cada número será una acumulación de las multiplicaciones de 0 y 1 por 2 elevado a la potencia de la posición que sea. Por ejemplo, 2^7 (128).

### Sobre los bytes

Cada byte, está compuesto por un octeto de bits, es decir, 8 bits contiguos, de los cuales, el de la izquierda será el de mayor peso (MSB: Most Significant Bit), y el de la derecha del todo, el menor (LSB: Less Significant Bit). Teniendo en consideración cómo funciona el sistema binario, el valor de la posición del MSB será 2^7, es decir, 128, el siguiente 62 (2^6), etc. Un 11000000, será el resultado de 1*2^7 + 1*2^6 + 0*2^5…, es decir, 128 + 62 = 190.

| 7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB) | position |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | valor |
| 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 2 |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 42 |
| 1 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 190 |

### Manipulación de bits en C

Para trabajar con bits en c, no existe un tipo de dato específico. Generalmente vamos a trabajar con variables de tipo `char` , hacemos esto porque, al ocupar solamente un byte, nos facilita el trabajo. Podremos trabajar, tanto con `char` como con `unsigned char`, dependiendo del rango de valores con el que queramos trabajar. Al ser una variable que no contempla los negativos, los `unsigned char` van desde el 0 hasta el 255. Por el contrario el `char`, aunque también tenga un byte de memoria, el rango numérico es del -128 al 127. 

## Operadores de bits

Los operadores de bits son herramientas utilizadas en programación para manipular los bits individuales de un número. De igual forma que cuando trabajamos en C y usamos una variable char, en realidad no es un caracter sino un entero en código ascii, cuando usamos los operadores de bits no estaremos trabajando con un número en base decimal, sino en base binaria.

### Operadores de desplazamiento

********************`operador <<`********************

Es el operador de desplazamiento a la izquierda. Mueve cada bit a la izquierda y coloca en la posición del bit menos significativo un cero, sustituyendo el que hubiese previamente.

Por ejemplo, si desplazásemos una vez a la izquierda el número 42, obtendríamos 84.

| 7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB) | position |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | valor |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 42 |
| 0 | 1 | 0 | 1 | 0 | 1 | 0 | 0 | 84 |

********************`operador >>`********************

Es el operador de desplazamiento a la derecha. Mueve cada bit a la derecha, descarta lo que hubiese en el bit menos significativo, y coloca en la posición del bit más significativo un cero.

Por ejemplo, si desplazásemos una vez a la izquierda el número 42, obtendríamos 21.

| 7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB) | position |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | valor |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 42 |
| 0 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 21 |

### Operadores de comparación

**********************`operador AND &`**********************

Compara dos números bit a bit, de forma que si ambos son 1, el resultado también será 1.

Por ejemplo, el resultado de comparar 42 & 119 es 34

| 7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB) | position |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | valor |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 42 |
| 0 | 1 | 1 | 1 | 0 | 1 | 1 | 1 | 119 |
| 0 | 0 | 1 | 0 | 0 | 0 | 1 | 0 | 34 |

**********************`operador OR |`**********************

Compara dos números bit a bit, de forma que si uno de ellos es 1 y el otro no, o si ambos son 1, el resultado también será 1.

Por ejemplo, el resultado de comparar 42 | 119 es 127

| 7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB) | position |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | valor |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 42 |
| 0 | 1 | 1 | 1 | 0 | 1 | 1 | 1 | 119 |
| 0 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 127 |

**********************`operador XOR ^`**********************

Compara dos números bit a bit, de forma que si uno de ellos es uno y el otro no, el resultado será 1. Si ambos son 1, o ambos son 0, será 0. (la x es de exclusividad)

Por ejemplo, el resultado de comparar 42 ^ 119 es 127

| 7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB) | position |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | valor |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 42 |
| 0 | 1 | 1 | 1 | 0 | 1 | 1 | 1 | 119 |
| 0 | 1 | 0 | 1 | 1 | 1 | 0 | 1 | 93 |

`**operador NOT ~**` 

Este operador no compara dos elementos, sino que se aplica sólo a 1 e invierte los bits del mismo. 

Por ejemplo, la inversión de ~42 sería

| 7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB) | position |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 128 | 64 | 32 | 16 | 8 | 4 | 2 | 1 | valor |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | 42 |
| 1 | 1 | 0 | 1 | 0 | 1 | 0 | 1 | 213 |

# Ejercicios para ponerlo en práctica

### `print_bits`

Write a function that takes a byte, and prints it in binary WITHOUT A NEWLINE
AT THE END. 
Your function must be declared as follows:

`void	print_bits(unsigned char octet);`

Example, if you pass 2 to print_bits, it will print "00000010"%

Allowed funtions: write

```c
#include <unistd.h>

void	print_bits(unsigned char octet)
{
	unsigned char	i;

	i = 128; // 1 0 0 0 0 0 0 0
	while (i > 0)
	{
		if  (octet & i)
		{
			write(1, "1", 1);
		}
		else
		{
			write(1, "0", 1);
		}
		i >>= 1;
	}
}
```

Inicializamos i en 128, de forma que tengo un uno en el MSB, y el resto son ceros. 

En cada iteración, con el comparador and, vamos a ir comparando nuestro número con i. Compararemos los 8 bits del octeto, por eso hemos iniciado i con un valor que tiene un único 1. De esta forma, si el resultado de comparar mi número con i, existe `if (octet & i)`, es decir, si no son todos 0, será porque en la iteración en la que estoy, corresponde escribir un 1, y si no, escribiré un 0. Después, desplazaré mi número i a la derecha una posición `i >>= 1`, de forma que compararé mi octeto con 01000000.

Realmente estamos haciendo 8 iteraciones, en cada una de las cuales comparamos los 8 bits de octet, con i. No haremos más de 8 ya que, de desplazar i una vez más a la derecha, valdría 0.

- Desglose de cada iteración (que por algo es una explicación para pencas)
    
    Por ejemplo, para el caracter ‘*’ (42):
    
    ************************************Primera iteración************************************
    
    Comparamos: 42 & 128
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 128 | 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
    | result | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
    
    Como el resultado es 00000000, printearemos un 0.
    
    ************************************Segunda iteración************************************
    
    Comparamos: 42 & 64
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 64 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | 0 |
    | result | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
    
    Como el resultado es 00000000, printearemos un 0.
    
    ************************************Tercera iteración************************************
    
    Comparamos: 42 & 32
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 32 | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 |
    | result | 0 | 0 | 1 | 0 | 0 | 0 | 0 | 0 |
    
    Como el resultado NO es 00000000, printearemos un 1.
    
    ************************************Cuarta iteración************************************
    
    Comparamos: 42 & 16
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 16 | 0 | 0 | 0 | 1 | 0 | 0 | 0 | 0 |
    | result | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
    
    Como el resultado es 00000000, printearemos un 0.
    
    ************************************Quinta iteración************************************
    
    Comparamos: 42 & 8
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 8 | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 |
    | result | 0 | 0 | 0 | 0 | 1 | 0 | 0 | 0 |
    
    Como el resultado NO es 00000000, printearemos un 1.
    
    ************************************Sexta iteración************************************
    
    Comparamos: 42 & 4
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 4 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | 0 |
    | result | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
    
    Como el resultado es 00000000, printearemos un 0.
    
    ************************************Quinta iteración************************************
    
    Comparamos: 42 & 8
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 2 | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 |
    | result | 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 |
    
    Como el resultado NO es 00000000, printearemos un 1.
    
    ************************************Octava iteración************************************
    
    Comparamos: 42 & 4
    
    | 42 | 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 |
    | --- | --- | --- | --- | --- | --- | --- | --- | --- |
    | 4 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 1 |
    | result | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
    
    Como el resultado es 00000000, printearemos un 0.
    

### `swap_bits`

Write a function that takes a byte, swaps its halves (like the example) and
returns the result. 
Your function must be declared as follows:

`unsigned char	swap_bits(unsigned char octet);`

Non functions allowed

Example:

1 byte

0100 | 0001
\ /
/ \
0001 | 0100

- print_swap_bits
    
    He hecho una versión imprimiendo en pantalla porque no sé cómo hacerlo acumulándolo en un unsigned char (todavía), y funciona: 
    
    ```c
    void	swap_bits(unsigned char octet)
    {
    	unsigned char	u;
    	unsigned char	v;
    	unsigned int	i;
    
    	u = 128;
    	v = 8;
    	i = 0;
    	while (v > 0)
    	{
    		if (octet & v)
    			write(1, "1", 1);
    		else
    			write (1, "0", 1);
    		v >>= 1;
    	}
    	while (u > 8)
    	{
    		if (octet & u)
    			write(1, "1", 1);
    		else
    			write (1, "0", 1);
    		u >>= 1;
    	}
    }
    ```
    

```c
unsigned char	swap_bits(unsigned char octet)
{
	return ((octet >> 4) | (octet << 4));
}
```

Lo que hace el código es comparar con or nuestro octeto desplazado 4 veces a la derecha, con el octeto desplazado 4 veces a la izquierda, de esta forma:

| 7  | 6 | 5 | 4 | 3 | 2 | 1 | 0 | POSICIÓN |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | octet |
| 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | >> 4 |
| 1 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | << 4 |
| 1 | 0 | 1 | 0 | 0 | 0 | 1 | 0 | >> 4 | << 4 |

### `reverse_bits`

Write a function that takes a byte, reverses it, bit by bit (like the
example) and returns the result. 
Your function must be declared as follows:

`unsigned char	reverse_bits(unsigned char octet);`

Non functions allowed

Example:

1 byte

0100  0001
||
\/
1000  0010

En mi momento menos esquizofrénico, he visto por ahí que si comparo los desplazamientos de 4 en 4 (como en la de antes), de 2 en 2 y de 1 en 1, le doy la vuelta al número. Me he puesto a comprobarlo y bueno, funciona.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/259c6b00-3250-445a-903b-e0d281d22119/Untitled.png)

| 7  | 6 | 5 | 4 | 3 | 2 | 1 | 0 | POSICIÓN |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 0 | 0 | 1 | 0 | 1 | 0 | 1 | 0 | octet |
| 0 | 0 | 0 | 0 | 0 | 0 | 1 | 0 | >> 4 |
| 1 | 0 | 1 | 0 | 0 | 0 | 0 | 0 | << 4 |
| 1 | 0 | 1 | 0 | 0 | 0 | 1 | 0 |  4 |  4 |
|  |  | 1 | 0 | 1 | 0 | 0 | 0 | >> 2 |
| 1 | 0 | 0 | 0 | 1 | 0 |  |  | << 2 |
| 1 | 0 | 1 | 0 | 1 | 0 | 0 | 0 | 2 | 2 |
|  | 1 | 0 | 1 | 0 | 1 | 0 | 0 | >> 1 |
| 0 | 1 | 0 | 1 | 0 | 0 | 0 |  | << 1 |
| 0 | 1 | 0 | 1 | 0 | 1 | 0 | 0 | 1 | 1 |

```c
unsigned char	reverse_bits(unsigned char octet)
{
    unsigned char   r;

    r = octet;
    r = ((r >> 4) | (r << 4));
    r = ((r >> 2) | (r << 2));
    r = ((r >> 1) | (r << 1));
    return (r);
}
```

Otra versión

```c
unsigned char reverse_bits(unsigned char b)
{
	unsigned char	r = 0;
	unsigned		byte_len = 8;

	while (byte_len--)
	{
		r = (r << 1) | (b & 1);
		b >>= 1;
	}
	return (r);
}
```

Inicia la longitud en 8 para hacer 8 iteraciones.

Checkea si el char b tiene un bit en su LSB comparándolo con 1 (b & 1). Si es así, como r ahora mismo es 00000000, al compararlo con or b & 1 (que ahora mismo sería 00000001), estaremos “encendiendo” el LSB de r, es decir, ahora mismo r es 00000001. A partir de aquí, en cada iteración desplazaremos r a la izquierda, ya que estamos “escribiendo nuestro número” al revés, de derecha a izquierda, y también desplazaremos b uno a la derecha, para volver a comparar el LSB (antes en la posición 2) con 1 y ver si el bit está o no encendido.