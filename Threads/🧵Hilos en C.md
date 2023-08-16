Un hilo o subproceso es una unidad básica de ejecución dentro de un proceso o programa en ejecución. El proceso tiene su espacio de memoria y recursos asignados, mientras que los hilos comparten el mismo espacio de memoria y recursos. Aunque comparten recursos, pueden realizar tareas de forma independiente. Los hilos permiten que un programa realice varias tareas de forma concurrente.

| Proceso | Hilo |
| --- | --- |
| Un proceso implica un programa completo en ejecución | Un hilo implica que un segmento o parte del programa en ejecución |
| Un proceso aislado puede contener varios hilos. | Múltiples hilos pueden formar parte de un mismo programa, compartiendo el mismo espacio de memoria.  |
| Los proceso consumen más recursos | Un hilo consumirá menos recursos que un proceso |
| Distintos procesos serán tratados por separado por el sistema operativo | Los hilos son tratados como una única tarea por el sistema operativo, con un PID único |
| Los procesos no comparten datos entre sí | Hilos de un mismo proceso sí comparten datos. |

# Creación de hilos

## Funciones implicadas

### `pthread_create`

`int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);`

Se utiliza para crear hilos. Recibe:

- `pthread_t *thread`: Es un puntero a una variable de tipo `pthread_t` que se utilizará para almacenar el identificador del hilo creado.
- `const pthread_attr_t *attr`: Es un puntero a una estructura de atributos de hilo (`pthread_attr_t`) que se puede utilizar para configurar ciertas características del hilo, como su tamaño de pila o su política de planificación. Si no es necesario especificar atributos personalizados, se le puede pasar `NULL`  y se utilizarán los atributos predeterminados.
- `void *(*start_routine) (void *)`: Es un puntero a una función que será el punto de entrada del hilo recién creado. Esta función se ejecutará en el nuevo hilo. El tipo de retorno de la función es `void *`, y toma un argumento de tipo `void *`.
- `void *arg`: Es un puntero que se pasará como argumento a la función `start_routine`. Puede usarse para pasarle datos al hilo que se está creando.

Devuelve 0 si el hilo se ha creado correctamente y otro valor en caso de error.

### `pthread_join`

`int pthread_join(pthread_t thread, void **retval);`

Se utiliza para esperar a que un hilo finalice su ejecución antes de continuar con el hilo principal o cualquier otro hilo. Bloqueará la ejecución del hilo que la llama hasta que el hilo especificado termine su función.

Recibe el identificador del hilo a esperar, y un puntero a un puntero void utilizado para almacenar el valor devuelto por la función del hilo que esté finalizando. Se le puede pasar NULL.

Si, no necesitamos esperar a que un hilo termine o no necesitamos recopilar su estado después de que termine, podremos usar `detach`, de lo contrario, será necesario usar `join.` Ambas funciones no pueden utilizarse sobre un mismo hilo. También debe tenerse en cuenta que cada hilo puede ser esperado con esta función una única vez.

## Proceso de creación

El proceso básico para crear un hilo será:

- Crear la función `routine` que vaya a ejecutar el nuevo hilo
- Declarar la variable en la que guardaremos el identificador del hilo `tid`
- Crear el hilo con `pthread_create`
- Llamar a 	`pthread_join` para esperar a que el hilo termine

```c
#include <stdio.h>
#include <pthread.h>

void	*routine(void * arg)
{
	printf("just to test the thread\n");
	return (NULL);
}

int	main(void)
{
	pthread_t	tid;

	pthread_create(&t1, NULL, &routine, NULL);
	pthread_join(t1, NULL);
	return (0);
}
```

Si quisiésemos crear dos hilos, tendríamos que llamar dos veces a cada función, de la siguiente forma:

```c
int	main(void)
{
	pthread_t	t1;
	pthread_t	t2;

	pthread_create(&t1, NULL, &routine, NULL);
	pthread_create(&t2, NULL, &routine, NULL);
	pthread_join(t1, NULL);
	pthread_join(t2, NULL);
	return (0);
}
```

Para comprobar que se está ejecutando en paralelo, podemos hacer que la función de rutina imprima dos mensajes con una espera en medio, de forma que se viese diferente si se imprimiesen en serie o en paralelo. Si lo hemos hecho correctamente, los hilos deberán funcionar en paralelo.

```c
**Impresión en serie:         Impresión en paralelo:**
**Mensaje 1                   Mensaje 1
Mensaje 2                   Mensaje 1
Mensaje 1                   Mensaje 2
Mensaje 2                   Mensaje 2
```

```c
void	*routine(void *arg)
{
	printf("Mensaje 1\n");
	sleep(3);
	printf("Mensaje 2\n");
	return (NULL)
}
```

Para asegurarnos de que nuestro ordenador tiene los recursos necesarios para la creación del hilo y que no ha habido ningún problema, deberemos considerar que la salida de `pthread_create` y `pthread_join` sea 0. De lo contrario deberemos parar el programa. Una forma muy básica de hacerlo será:

```c
int	main(void)
{
	pthread_t	t1;

	if (pthread_create(&t1, NULL, &routine, NULL) != 0)
		return (-1);
	if (pthread_join(t1, NULL) != 0)
		return (-1);
	return (0);
}
```

## Creación de hilos en bucle

Lo conveniente será hacer dos bucles while, uno para crear todos los hilos, y otro para llamar a pthread_join. De lo contrario, estaríamos trabajando en serie, ya que crearemos un hilo y esperaremos a que acabe en cada iteración.

```c
int	main(void)
{
	pthread_t	th[4];
	int			i;

	pthread_mutex_init(&mutex, NULL);
	i = 0;
	while (i < 4)
	{
		if (pthread_create(th + i, NULL, &routine, NULL) != 0)
			return (-1);
		i++;
	}
	i = 0;
	while (i < 4)
	{
		if (pthread_join(th[i], NULL) != 0)
			return (-1);
		i++;
	}
	pthread_mutex_destroy(&mutex);
	return (0);
}
```

### Detachable threads (`**pthread_detach` )**

`int pthread_detach(pthread_t thread);`

Se trata de hilos separados del hilo principal, cuyos estado y recursos se liberan automáticamente una vez finalizada su ejecución. 

No necesitan ser rastreados ni explícitamente esperados por el hilo principal (con  `pthread_join`). Esto e útil en situaciones en las que no se necesitan los resultados de un hilo o no es importante si el hilo termina o no de ejecutarse por completo, por ejemplo en hilos que realizan tareas secundarias de bajo nivel que no afecten directamente al flujo principal del programa. Esto ahorraría recursos, al no tener la necesidad de rastrear y esperar hilos adicionales, siendo una forma de optimización de recursos.

Para marcar un hilo como desprendido o detachable se utilizaría la función `pthread_detach` que recibe el identificador del hilo a marcar como detachable, y devuelve 0 en caso de éxito o -1 en caso de error.

Un hilo desprendido no puede volver a unirse al principal. En este caso, el hilo limpiará su propia memoria. Normalmente se utilizará en procesos de larga ejecución dentro del proceso principal, si no queremos que los demás hilos sigan funcionando. 

# Sincronización de hilos

Para evitar problemas de sincronización, como race conditions,  y proteger los recursos, podremos utilizar herramientas como los mutex.

## Race conditions

Son un tipo de problema de sincronización que ocurre cuando múltiples hilos o procesos acceden y manipulan simultáneamente un recurso compartido sin ningún mecanismo de coordinación adecuado. Pueden conducir a resultados inesperados y errores en el programa.

Si dos o más hilos intentan modificar el mismo recurso al mismo tiempo, y el resultado final depende del orden en que se ejecuten las operaciones, el resultado podrá variar en cada ejecución del programa, volviéndolo impredecible.

Por ejemplo, si tuviésemos una variable `contador` que ir aumentando desde 0, y dos hilos intentasen incrementar el contador al mismo tiempo, podría suceder que ambos leyesen 0 y aumentasen a 1, de forma que el resultado del programa, debiendo ser 2, será 1.

## Mutex

Un mutex (mutual exclusion) es una herramienta de sincronización utilizada para garantizar  el acceso exclusivo a recursos compartidos entre hilos, evitando problemas de sincronización. 

Se trataría de un tipo especial de variable. Es en esencia, una estructura de datos que actúa como un *candado* que los hilos pueden adquirir y liberar para acceder a un recurso compartido de manera exclusiva. Si un hilo intenta adquirir un mutex que ya está en uso por otro hilo, se bloqueará y deberá esperar a que esté disponible.

## Funciones para trabajar con mutex

### `pthread_mutex_init`

`int pthread_mutex_init(pthread_mutex_t *mutex, const pthread_mutexattr_t *attr);`

Se utiliza para inicializar un objeto de tipo `pthread_mutex_t`, que es una estructura que representa un mutex. 

Recibe: Un puntero a la estructura que se utilizará para almacenar el mutex iniciado, y un puntero a una estructura de atributos mutex para configurar ciertas características, aunque puede pasársele NULL. 

Devuelve 0 en caso de éxito y un código de error en caso contrario.

### `pthread_mutex_destroy`

`int pthread_mutex_destroy(pthread_mutex_t *mutex);`

Se utiliza para destruir un mutex. Recibe el puntero al mismo y devuelve 0 en caso de éxito o el código de error en caso contrario.

Antes de destruir un mutex habrá que asegurarse de que no esté siendo utilizado por ningún hilo. Para ello, se puede utilizar `pthread_join`.

### `pthread_mutex_lock`

`int pthread_mutex_lock(pthread_mutex_t *mutex);`

Se utiliza para que un hilo que necesite acceder al recurso compartido protegido por el mutex, pueda adquirirlo.

Si el mutex está disponible cuando el hilo llama a la función, lo adquirirá y podrá continuar con el acceso al recurso compartido protegido por el mutex. Si ya está bloqueado por otro hilo, la función bloqueará la ejecución del hilo actual hasta que el mutex esté disponible.

Esta función llevaría a cabo tres acciones: comprobar si el mutex está disponible, esperar a que lo esté, y bloquearlo.

### `pthread_mutex_unlock`

`int pthread_mutex_unlock(pthread_mutex_t *mutex);`

Se utiliza para liberar un mutex que haya sido previamente adquirido por un hilo. Debe ser llamada por el mismo hilo que lo adquirió.

## Cómo crear y usar un mutex

Deberemos:

- Declarar una variable `pthread_mutex_t`
- Inicializar la variable con `pthread_mutex_init`
- Llamar a `pthread_mutex_lock` para bloquear el mutex en la routine function.
- Llamar a `pthread_mutex_unlock` para desbloquear el mutex en la routine function.
- Destruir el mutex con `pthread_mutex_destroy`

```c
#include <stdio.h>
#include <pthread.h>

pthread_mutex_t	mutex;

void	*routine(void *arg)
{
	pthread_mutex_lock(&mutex);
	printf("thread test\n");
	pthread_mutex_unlock(&mutex);
	return (NULL);
}

int	main(void)
{
	pthread_t		t1;

	pthread_mutex_init(&mutex, NULL);
	if (pthread_create(&t1, NULL, &routine, NULL) != 0)
		return (-1);
	if (pthread_join(t1, NULL) != 0)
		return (-1);
	pthread_mutex_destroy(&mutex);
	return (0);
}
```

Con esto, nos estamos asegurando de que las líneas de código entre la llamada a `pthread_mutex_lock` y a `pthread_mutex_unlock` no puedan ejecutarse por más de un hilo a la vez.

# Parámetros de entrada y retorno de un hilo

## Cómo tomar los valores de retorno de un hilo

[Get return value from a thread (pthread_join)](https://www.youtube.com/watch?v=ln3el6PR__Q&list=PLfqABt5AS4FmuQf70psXrsMLEDQXNkLq2&index=6)

Suponiendo el caso de que nuestra routine function genera valores de algún tipo, ¿cómo puedo conseguir tomar esos valores en mi main function? Utilizando el segundo parámetro de `pthread_join`, que es un puntero doble a void. 

`int pthread_join(pthread_t thread, void **retval);`

Deberemos declarar un puntero *void, con el objetivo de pasar su dirección como segundo argumento de `pthread_join`.  De ser un puntero a algún otro tipo de variable, en función del valor que queramos obtener del hilo, deberemos hacer un casteo.

De igual forma, deberemos hacer que el return value de la función de nuestro hilo, sea un void* a lo que queramos obtener, con el casteo en caso de ser necesario.

Otra cuestión a considerar será que, al crear una variable en la routine function, lo estaremos haciendo en la pila de variable local de la función, por lo que una vez termina la función, esa memoria ya no será válida. Retornar un puntero a esa memoria puede causar comportamientos indefinidos. La solución, sería asignar memoria dinámicamente utilizando malloc. 

```c
void	*routine(void *arg)
{
	int	*value;

	value = (int *)malloc(sizeof(int));
	if (!value)
		pthread_exit(NULL);
	*value = 42;
	return ((void *)value);
}

int	main(void)
{
	pthread_t	t1;
	int			*ret;

	if (pthread_create(&t1, NULL, &routine, NULL) != 0)
		return (-1);
	if (pthread_join(t1, (void **)&ret) != 0)
		return (-1);
	printf("Valor retornado: %i\n", *ret);
	return (0);
}
```

## Pasar parámetros a los hilos

Para pasar una variable como parámetro a un hilo desde la main function, tendremos que utilizar el cuarto argumento de `pthread_create`, que es un puntero a un void.

`int pthread_create(pthread_t *thread, const pthread_attr_t *attr, void *(*start_routine) (void *), void *arg);`

Por ejemplo, si queremos que cada hilo imprima por pantalla cada número contenido en un array de enteros. Lo que le pasaremos a la función del hilo será el índice del número a imprimir. 

En este caso, también será necesario alojar memoria para un puntero al parámetro a pasar, de intentar pasar el parámetro directamente, obtendríamos comportamientos indefinidos.

Si hemos hecho un bucle para crear los hilos, alojaremos memoria dentro del bucle, para, en cada iteración, alojar memoria para el valor a pasar. A la hora de liberar, no podremos liberar dentro del bucle de creación, porque, al no haber llamado aún a `pthread_join`, no podremos asegurarnos de que el hilo haya terminado. Por ello, habrá que liberar dentro de la rutina del hilo. 

```c
pthread_mutex_t	mutex;
int	primes[5] = {2, 3, 5, 7, 11};

void	*routine(void *arg)
{
	int	index;

	index = *(int *) arg;
	printf("%d ", primes[index]);
	free(arg);
	return (NULL);
}

int	main(void)
{
	pthread_t	th[5];
	int			i;
	int			*i_ptr;

	pthread_mutex_init(&mutex, NULL);
	i = 0;
	while (i < 5)
	{
		i_ptr = malloc(sizeof(int));
		*i_ptr = i;
		if (pthread_create(th + i, NULL, &routine, i_ptr) != 0)
			return (-1);
		i++;
	}
	i = 0;
	while (i < 5)
	{
		if (pthread_join(th[i], NULL) != 0)
			return (-1);
		i++;
	}
	pthread_mutex_destroy(&mutex);
	return (0);
}
```