- Índice (las he ordenado como he podido no me juzgues)

# {···} Funciones Autorizadas en Minishell

# Funciones para trabajar con directorios y archivos

### `**getcwd**`

`char *getcwd(char *buf, size_t size);` 

Recibe dos argumentos: un puntero al buffer de caracteres donde se almacenará la ruta absoluta y el tamaño `size_t` del buffer proporcionado. Devuelve un string de caracteres con la ruta absoluta y NULL en caso de que el buffer sea demasiado pequeño o haya algún otro error. 

### `chdir`

`int chdir(const char *path);`

Recibe una cadena con la ruta del directorio a cambiar. Devuelve 0 o, en caso de error, -1.

### `opendir`

`DIR *opendir(const char *pathname);`

Se utiliza para abrir un directorio cuya ruta recibe por argumento, y obtener un puntero al flujo de directorio. Si hay algún error, devolverá NULL.

### **`readdir`**

`struct dirent *readdir(DIR *dirp);`

Se utiliza para leer la siguiente entrada dentro de un directorio abierto utilizando el puntero al flujo de directorio obtenido mediante `opendir`.

Si tiene éxito, la función devuelve un puntero a una estructura **`struct dirent`** que contiene información sobre la entrada del directorio. Si hay algún error o se alcanza el final del directorio, devolverá `NULL`.

### `closedir`

`int closedir(DIR *dirp);`

Se utiliza para cerrar el flujo de directorio abierto previamente con **`opendir`**. Devuelve 0 ó -1.

### **`write`**

`ssize_t write(int fd, const void *buf, size_t count);`

Se utiliza para escribir datos en un archivo. 

Recibe el `fd` del archivo, un puntero `*buf` a los datos a escribir y el número `count` de bytes que se desea escribir.

Devuelve el número de bytes escrito si la operación tiene éxito, y -1 en caso de error.

### `access`

`int access(const char *path, int mode);`

Se utiliza para verificar si un proceso tiene permisos de acceso a un archivo o directorio.

Recibe un puntero `*path` a una cadena de caracteres con la ruta al archivo o directorio a verificar, y un entero `mode`, que representa un conjunto de constantes que indican qué tipo de operación desea verificarse. Por ejemplo, `R_OK` verifica la lectura, `W_OK`, la escritura y `X_OK`, la ejecución.

Devuelve 0 si tiene éxito y el proceso tiene los permisos adecuados, y -1 en caso contrario.

### `open`

`int open(const char *pathname, int flags, mode_t mode);`

Se utiliza para abrir archivos.

Recibe el puntero `*pathname` a la ruta del archivo, un entero que actúa como banderas sobre cómo abrir el archivo, y un tercer argumento opcional para indicar los permisos en caso de que el archivo vaya a crearse. 

Devuelve un fd al archivo abierto si tiene éxito, y -1 en caso de error.

### `read`

`ssize_t read(int fd, void *buf, size_t count);`

Read leerá de un `fd` de archivo abierto, una cantidad `count` de bytes, guardándola en su segundo parámetro `*buf`. Devuelve el número de bytes leídos o -1 en caso de error.

### `close`

`int close(int fd);`

Cierra el descriptor de archivo `fd` previamente abierto. Devuelve 0 en caso de éxito y -1 en caso de error.

### `stat`

`int stat(const char *pathname, struct stat *buf);`

Se utiliza para obtener información sobre un archivo o directorio. 

Recibe la ruta del archivo y un puntero a una estructura `stat` donde se almacenarán los atributos y detalles del archivo. 

Devuelve 0 o, en caso de error, -1.

- `struct stat`
    - **`st_mode`**: Los permisos y tipo de archivo.
    - **`st_size`**: El tamaño del archivo en bytes.
    - **`st_uid`**: El ID del usuario propietario.
    - **`st_gid`**: El ID del grupo propietario.
    - **`st_atime`**: La última vez que se accedió al archivo.
    - **`st_mtime`**: La última vez que se modificó el archivo.
    - **`st_ctime`**: La última vez que se cambió el estado del archivo (como los permisos).

### `lstat`

`int lstat(const char *pathname, struct stat *buf);`

Similar a `stat` pero diseñada específicamente para obtener información sobre enlaces simbólicos. Mientras que `stat` sigue el enlace simbólico y devuelve información sobre el archivo al que aputa, `lstat`aporta información sobre el enlace en sí mismo, sin seguirlo.

### `fstat`

`int fstat(int fd, struct stat *buf);`

Similar a las anteriores, pero en lugar de aceptar la ruta, acepta el descriptor de archivo, de forma que puede utilizarse para obtener información sobre un archivo abierto o un fd específico. 

### `unlink`

`int unlink(const char *pathname);`

Se utiliza para eliminar un archivo cuya ruta recibe por argumento. Elimina el enlace entre el nombre del archivo y sus datos. Devuelve 0 o -1 en caso de error.

# Funciones para trabajar con procesos

### `fork`

`pid_t fork(void);`

Crea un proceso hijo del proceso en que es llamada. 

En el proceso padre devuelve el ID del hijo.

En el proceso hijo devuelve 0 en caso de éxito o -1 en caso de error.

### `wait`

`pid_t wait(int *status);`

Se utilizará para esperar a que los procesos hijos terminen su ejecución.

Recibe un puntero a un entero que almacenará el estado de salida del proceso hilo. Es nullable.

Devuelve el ID del proceso hijo o -1 en caso de error.

### `waitpid`

`pid_t waitpid(pid_t pid, int *_Nullable status, int options);`

Permite especificar a qué proceso hijo se debe esperar.

Devuelve el ID del proceso hijo o -1 en caso de error.

- Sobre el tercer parámetro `options`
    
    Se utiliza para especificar ciertas opciones que afectan al comportamiento de la llamada. Por ejemplo: 
    
    - **`WNOHANG`** (No bloqueante): Si se establece esta opción, **`waitpid`** no bloqueará la ejecución del proceso padre mientras espera a que el proceso hijo termine. Si el proceso hijo no ha terminado, **`waitpid`** retornará inmediatamente con el valor 0, lo que indica que no hay proceso hijo disponible para esperar. Esto es útil si deseas realizar otras tareas mientras esperas a un proceso hijo.
    - **`WUNTRACED`**: Si un proceso hijo está detenido (suspendido, por ejemplo, debido a una señal de detención como Ctrl-Z), **`waitpid`** también regresará información sobre el estado de este proceso hijo. Esto es útil para monitorear procesos que están en un estado de detención temporal.
    - **`WCONTINUED`**: Similar a **`WUNTRACED`**, pero se utiliza para monitorear procesos que han sido reanudados después de estar en estado de detención. Esto puede ocurrir, por ejemplo, cuando un proceso detenido se reanuda con la señal de continuación **`SIGCONT`**.
    - **`WEXITED`**: Esta opción especifica que **`waitpid`** debe esperar solo por procesos que hayan terminado. Si no se establece esta opción, **`waitpid`** puede regresar cuando un proceso hijo cambia su estado, como cuando se suspende o se reanuda.
    - **`WSTOPPED`**: Similar a **`WUNTRACED`**, esta opción especifica que **`waitpid`** debe esperar solo por procesos detenidos. Es útil si solo estás interesado en procesos que han sido detenidos.
    - **`WCONTINUED`**: Esta opción se utiliza para esperar por procesos que han sido reanudados después de estar detenidos. Se utiliza en conjunción con señales como **`SIGCONT`** que se envían para reanudar procesos detenidos.

### `wait3`

`pid_t wait3(int *status, int options, struct rusage *rusage);`

Más avanzada que `wait`, proporciona informacióna dicional sobre los recursos utilizados por el hijo, la cual consta en la estructura `rusage` que recibe como tercer parámetro.

### `wait4`

`pid_t wait4(pid_t pid, int *status, int options, struct rusage *rusage);`

Similar a `wait3` pero permite indicarle el `pid` del hilo a esperar.

# Funciones para trabajar con señales

### `signal`

`void (*signal(int signum, void (*handler)(int)))(int);`

Establece cómo se debe manejar una señal específica. Toma dos argumentos: el número de la señal a manejar, y un puntero a una función que actuará como el manejador de la señal.

### `sigaction`

`int sigaction(int signum, const struct sigaction *act, struct sigaction *oldact);`

Se utiliza para establecer el manejo (handler) de una función específica, con el propósito de controlar el comportamiento de un programa cuando se recibe una señal. Recibe el número *signum* de la señal, un puntero *act* a una estructura de tipo `struct sigaction` con información sobre el manejo de la señal, y un puntero *oldact* a otra estructura con información previa sobre el manejo de la señal. Si no se necesita esa información, puede pasarse `NULL`. Devuelve 0 en caso de éxito y -1 en caso de error. 

Sobre la estructura `struct sigaction`

```c
struct sigaction {
    void     (*sa_handler)(int);
    void     (*sa_sigaction)(int, siginfo_t *, void *);
    sigset_t   sa_mask;
    int        sa_flags;
    void     (*sa_restorer)(void);
};
```

Los campos principales de la estructura **`struct sigaction`** son los siguientes:

1. **`sa_handler`**: Puntero a la función de manejo (handler) que se ejecutará cuando se reciba la señal. Especifica el comportamiento del programa al recibir la señal. Esta función debe aceptar un argumento entero que representa el número de la señal (**`int signum`**).
2. **`sa_sigaction`**: Puntero a una función de manejo (handler) alternativa para señales que proporciona más información sobre la señal recibida. Esta función debe aceptar tres argumentos: el número de la señal (**`int signum`**), una estructura **`siginfo_t`** que contiene información adicional sobre la señal y un puntero a **`void`** que puede ser utilizado para datos adicionales.
3. **`sa_mask`**: Conjunto de señales que se bloquearán temporalmente durante la ejecución del manejador de señal. Especifica las señales adicionales que se deben bloquear cuando el manejador de señal se está ejecutando para evitar interrupciones no deseadas.
4. **`sa_flags`**: Opciones y modificadores para el comportamiento del manejo de señales. Puede incluir flags como **`SA_RESTART`** para reiniciar automáticamente llamadas de sistema interrumpidas por la señal.
5. **`sa_restorer`**: Puntero a una función que se utiliza para restaurar el estado del programa después de que el manejador de señal se haya ejecutado. Este campo está obsoleto y generalmente no se utiliza.

### `kill`

`int kill(pid_t *pid*, int *sig);*`

Envía una señal a un proceso determinado. Si falla devolverá -1, si funciona correctamente, devuelve 0.

# Funciones para trabajar con pipes

### **`execve`**

`int execve(const char *pathname, char *const argv[], char *const envp[]);`

Se utiliza para ejecutar un nuevo programa desde un proceso existente. La función reemplaza la imagen del proceso actual, con la imagen del programa especificado en sus argumentos.

Recibe la ruta del archivo ejecutable del programa, un puntero a un array bidimensional con los argumentos del programa, cuyo último elemento debe ser `NULL`, y otro array bidimensional con las variables de entorno del nuevo programa.

Devuelve 0 en caso de éxito y -1 si hay algún error.

### `dup`

`int dup(int oldfd);`

Dup duplica un descriptor de archivo existente, recibiendo el `fd` del archivo a duplicar y devolviendo el `fd` del nuevo, o -1 en caso de error.

### `dup2`

`int dup2(int oldfd, int newfd);`

Duplica un descriptor de antiguo en uno nuevo específico, que recibe como parámetro. Devuelve el nuevo `fd` o -1 en caso de error.

### `pipe`

`int pipe(int pipefd[2]);`

Recibe un array con dos enteros, siendo `pipefd[0]` el extremo de lectura de la tubería, y `pipefd[1]` el extremo de escritura.  La función une ambos extremos, creando una tubería de comunicación unidireccional. Devuelve 0 si hay éxito y -1 si no.

# Funciones de manejo de errores

### **`strerror`**

`char *strerror(int errnum);`

Toma un número de error, generalmente almacenado en la variable global `errno` y devuelve una cadena describiendo el error correspondiente.

### `perror`

`void perror(const char *s);`

Imprime en consola un mensaje de error, seguido en una descripción basada en el número de error almacenado en `errno`.

# Funciones para trabajar con terminales

## Funciones de la librería <readline.h>

### `readline`

```c
#include <readline/readline.h>
char *readline(const char *prompt);
```

Se utiliza para leer una línea de entrada desde la terminal de manera interactiva. Recibe un puntero a una string que se muestra como el mensaje de inicio de la línea de entrada. Devuelve un puntero a un buffer que contiene la línea leída (excluyendo el newline). Si el usuario presiona  ‘enter’ sin ingresar ningún texto, la función devuelve `NULL`.

### `rl_clear_history`

`void rl_clear_history(void);`

Esta función se utiliza para borrar completamente el historial de comando almacenado en la memoria por la biblioteca Readline.

### `rl_on_new_line`

`void rl_on_new_line(void);`

Indica a la biblioteca Readline que el cursos se ha movido a una nueva línea, normalmente después de que se haya impreso algún resultado en la terminal. Al utilizarla, Readline sabrá que cualquier entrada de texto posterior a ese punto deberá ser tratada como una nueva línea.

### `rl_replace_line`

`void rl_replace_line(const char *text, int clear_undo);`

La función reemplaza la cadena actual de la línea de edición con una nueva cadena de caracteres que se le pasará como parámetro (`*text`). También, opcionalmente, puede borrar el historial de deshacer (undo history), en caso de que en su segundo parámetro reciba un entero distinto de 0. Si se le pasa 0, no lo borrará.

### `rl_redisplay`

`void rl_redisplay(void);`

Se utiliza en programas interactivos para redibujar la línea actual de edición en la terminal. Cualquier cambio que se haya realizado con funciones como `rl_replace_line` o `rl_insert_text` se reflejará visualmente.

Es útil cuando desea brindarse retroalimentación visual instantánea sobre los cambios realizados en la línea de edición.

### `add_history`

`void add_history(const char *line);`

Toma un comando o entrada `*line` y lo agrega al historial de comandos.

## Funciones de manejo de terminales

### **`isatty`**

`int isatty(int fd);`

Se utiliza para determinar si un descriptor de archivo se refiere a un dispositivo interactivo, como una terminal, o a un archivo regular.

Si recibe un `fd` de un dispositivo interactivo, devuelve un valor distinto de cero. Si se trata de un archivo no interactivo, devuelve 0.

### `ttyname`

`char *ttyname(int fd);`

Se utiliza para obtener el nombre de la terminal con la que está interactuando el proceso a través del descriptor de archivo dado.

Si `fd` no se refiere a un dispositivo de terminal, la función devuelve **`NULL`**.

### `ttyslot`

`int ttyslot(void);`

Se utiliza para obtener el número de entrada del archivo de terminal en la tabla de usuarios. Específicamente, esta función se utiliza en combinación con los archivos de información de terminal en `/etc/ttys` para determinar la posición de entrada del usuario en el archivo.

Si hay error, devuelve -1.

### `ioctl`

`int ioctl(int fd, unsigned long request, ...);`

Se utiliza para realizar operaciones de control, permitiendo a los programas interactuar con los dispositivos y recursos a través de llamadas al sistema.

Recibe el descriptor de archivo del dispositivo o recurso con el que se desea interactuar, un código `request` que representa la operación de control a realizar, y parámetros adicionales que dependerán de la operación que se esté realizando.

Devuelve 0 o -1 en caso de error. 

### `getenv`

`char *getenv(const char *name);`

Se utiliza para obtener el valor de una variable de entorno. Recibe el nombre de la variable y devuelve un puntero a la string con el valor de la misma. Su no existe, devolverá NULL.

### `tcsetattr`

```c
#include <termios.h>
int tcsetattr(int fd, int optional_actions, const struct termios *termios_p);
```

Se utiliza para configurar atributos de terminal, como la velocidad de transmisión, el control de flujo, el modo de operación y otros parámetros relacionados con la comunicación y la manipulación del terminal. 

Recibe:

- El fd del archivo del terminal a configurar.
- Un entero `optional_actions` que indica cuándo se deben aplicar los cambios.
    - `TCSANOW`: Los cambios se aplican inmediatamente.
    - `TCSADRAIN`: Los cambios se aplican después de que todos los datos pendientes hayan sido transmitidos.
    - `TCSAFLUSH`: Los cambios se aplican después de que todos los datos pendientes hayan sido transmitidos, y cualquier dato recibido y no leído se descarta.
- Un puntero a una estructura `struct termios` con los nuevos valores de configuración del terminal.
    
    ```c
    struct termios {
        tcflag_t c_iflag;      /* Modos de entrada */
        tcflag_t c_oflag;      /* Modos de salida */
        tcflag_t c_cflag;      /* Modos de control */
        tcflag_t c_lflag;      /* Modos locales */
        cc_t c_line;           /* Carácter de línea */
        cc_t c_cc[NCCS];       /* Caracteres de control */
    };
    ```
    

Devuelve 0 o -1 si hay error.

### `tcgetattr`

`int tcgetattr(int fd, struct termios *termios_p);`

Permite recuperar información sobre los parámetros y características actuales del terminal, como la velocidad de transmisión (baud rate), los caracteres de control, el modo de operación y otros parámetros relacionados con la comunicación y la manipulación del terminal.

Recibe el fd del archivo del terminal a configurar, y un puntero a la estructura `termios`. Devuelve -1 en caso de error.

## Funciones de la librería <curses.h>

### `tgetent`

`int tgetent(char *bp, const char *name);`

Se utiliza, junto con otras funciones de la librería `curses.h` para interactuar con terminales y realizar operaciones como la limpieza de la pantalla, el posicionamiento del cursos y el cambio de colores.

Recibe un puntero `bp` a un buffer donde se almacenarán las capacidades del terminal y el nombre `name` del terminal del que se desean obtener las capacidades. Devuelve 0 ó -1.

Es importante tener en cuenta que `tgetent` carga las capacidades del terminal en el buffer proporcionado (`bp`) en un formato específico. Las capacidades se almacenan como cadenas de escape que se pueden utilizar para controlar el comportamiento del terminal. Una vez que las capacidades se han cargado con éxito, puedes usar otras funciones de `curses` para acceder a estas cadenas y realizar operaciones específicas del terminal

### `tgetflag`

`int tgetflag(const char *capname);`

Se utiliza para obtener el valor de una bandera específica asociada con las capacidades de un terminal desde una base de datos de descripciones de terminales (normalmente se usa la base de datos `terminfo`).

Recibe una cadena `capname` que especifica el nombre de la capacidad del terminal de la que se desea consultar si tiene una flag asociada. Si la flag está configurada devolverá 1 y 0 si no lo está.

### `tgetnum`

`int tgetnum(const char *capname);`

Se utiliza para obtener valores numéricos asociados con capacidades del terminal. Estos valores numéricos pueden representar cosas como el número de columnas en el terminal, el número de filas, el ancho de un carácter o cualquier otro valor numérico relevante para el terminal.

Recibe una cadena que especifica el nombre de la capacidad a consultar. Devolverá -1 si la capacidad no está configurada o no se encuentra en la base de datos.

### `tgetstr`

`char *tgetstr(const char *capname, char **area);`

Se utiliza para obtener cadenas de caracteres asociadas con capacidades del terminal.

Además de recibir `capname`, recibe un puntero a un área de almacenamiento donde se almacenará la cadena de caracteres obtenida. Devuelve un puntero a la cadena asociada con la capacidad especificada. En caso de que no esté configurada o no se encuentre, devolverá `NULL`. 

### `tgoto`

`char *tgoto(const char *capname, int col, int row);`

Se utiliza para generar secuencias de escape de control del cursor que muestran cómo mover el cursor a una posición específica en la pantalla del terminal. Esto es útil cuando deseas colocar el cursor en una ubicación específica, como una fila y una columna particulares en la pantalla del terminal.

Recibe el nombre de la capacidad que describe cómo se debe mover el cursos en el terminal, y la columna `col` y la fila `row`a la que se desea mover el curso.

Devuelve una cadena de caracteres que representa la secuencia de escape necesaria para mover el cursor a la posición especificada.

### `tputs`

`int tputs(const char *str, int affcnt, int (*putc)(int));`

Se utiliza para enviar secuencias de escape al terminal. Se puede usar para realizar diversas acciones, como cambiar los colores, borrar la pantalla, mover el cursor y más, al enviar las secuencias de escape adecuadas al terminal.

Recibe:

- `str`: La secuencia de escape que deseas enviar al terminal.
- `affcnt`: El número de veces que deseas que se aplique la secuencia de escape (generalmente 1 si es una operación única).
- `putc`: Una función que se utiliza para enviar caracteres a la

Devuelve 0 si hay éxito o un negativo en caso de error.