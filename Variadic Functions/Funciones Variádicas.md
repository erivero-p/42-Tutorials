# Funciones Variádicas

Necesitarás aprender a trabajar con funciones variádicas para el proyecto [ft_printf](https://github.com/erivero-p/ft_printf)

Las funciones variádicas (variadic functions) son aquellas que pueden recibir un numero variable de argumentos. Toma como mínimo un argumento fijo y luego se puede pasar cualquier número de argumentos. La función variádica consiste en al menos una variable fija y luego una elipsis (…) como último parámetro.

```c
int ft_viariadic(int n, char c, ...);
```

En este caso, la función toma como argumentos fijos un entero n y un caracter c, y además un número variable y desconocido de argumentos. 

Para poder acceder a los valores de los argumentos y por tanto trabajar con funciones variádicas, usamos la siguiente librería: 

```c
#include <stdarg.h>
```

Esta librería incluye los siguientes métodos: 

- `va_start(va_list ap, argN);` que permite el acceso a los argumentos de la función variádica.
- `va_arg(va_list ap, tipo);` que accede al siguiente argumento.
- `va_copy(va_list ap, tipo);` que hace una copia de los argumentos.
- `va_end(va_list ap);` que finaliza el recorrido de los argumentos.

Aquí, **va_list** contiene la información que necesitan **va_start,** **va_arg, va_end**  y **va_copy**.

**argN** es el último argumento no variádico (fijo) que reciba mi función variádica. En mi ejemplo, sería char c.

A la hora de hacer una función variádica, primero deberemos definir una variable donde guardaremos los argumentos variádicos que reciba nuestra función en cada llamada. Para ello, tenemos la variable de tipo va_list. Que, una vez declarada, deberemos inicializar con `va_start`. Durante la ejecución de nuestra función, podrá acceder a los argumentos utilizando `va_arg` y no deberemos olvidar cerrar la lista con `va_end`.

*Ejemplo de una función variádica*

```c
#include <stdarg.h>
#include <stdio.h>

// Variadic function to add numbers
int AddNumbers(int n, ...)
{
    int Sum = 0;

    // Declaring pointer to the
    // argument list
    va_list ptr;

    // Initializing argument to the
    // list pointer
    va_start(ptr, n);

    for (int i = 0; i < n; i++)

        // Accessing current variable
        // and pointing to next one
        Sum += va_arg(ptr, int);

    // Ending argument list traversal
    va_end(ptr);

    return Sum;
}

// Driver Code
int main()
{
    printf("\n\n Variadic functions: \n");

    // Variable number of arguments
    printf("\n 1 + 2 = %d ",
           AddNumbers(2, 1, 2));

    printf("\n 3 + 4 + 5 = %d ",
           AddNumbers(3, 3, 4, 5));

    printf("\n 6 + 7 + 8 + 9 = %d ",
           AddNumbers(4, 6, 7, 8, 9));

    printf("\n");

    return 0;
}
```


