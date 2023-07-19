# MiniLibX

MiniLibX es una librería diseñada para facilitar la programación de aplicaciones gráficas simples en entornos Unix.

### *¿Cómo usar MiniLibX?*

En la página del proyecto en la intranet se nos da acceso, además de al subject, a diferentes carpetas comprimidas con diferentes versiones de la librería. Una de ellas, para línux. Cada una tiene su makefile, así que deberemos usar el comando make para compilarlas antes de usarlas.

Para usarla en mac, al compilar nuestros archivos deberemos usar las siguientes flags: 

`-Lmlx -lmlx -framework OpenGL -framework AppKit`

Para usarla en linux, previamente deberemos haber instalado las siguientes dependencias: `xorg`, `libxext-dev` y`zlib1g-dev`.

`sudo apt-get update && sudo apt-get install xorg libxext-dev zlib1g-dev libbsd-dev`

Después, habrá que ejecutar el script `configure` para configurar MLX. 

Las flags a utilizar, en este caso serán: 

`-Lmlx_linux -lmlx_Linux -L/usr/lib -Imlx_linux -lXext -lX11 -lm -lz` 

Para instalarla en Windows es algo más cpmplejo. Más info [aquí](https://harm-smits.github.io/42docs/libs/minilibx/getting_started.html#getting-a-screen-on-windows-10-wsl2)

## Funciones MLX

### **mlx_init**

**`mlx_init();`**

Inicializa una conexión con el servidor gráfico y crea una estructura de datos necesaria para el uso de miniLibX. 

Será nuestra primera función a utilizar. Se usa sobre un `void *` y devuelve la dirección donde la librería va a estar almacenando las cosas de nuestro mlx.

### mlx_new_window

`void *mlx_new_window(void *mlx_ptr, int size_x, int size_y, char *title);`

Crea una nueva ventana en la pantalla con un tamaño y posición específicos.

Recibe: el puntero `mlx_ptr` de conexión con el servidor gráfico, que es el que hemos creado al llamar a init, los tamaños de los ejes x e y, y el título que tendrá la ventana. Devuelve un puntero a la dirección donde se encuentra la ventana a crear.

### Otras (aún no las he necesitado)

1. **mlx_pixel_put**: Dibuja un píxel en una ventana en una ubicación específica con un color determinado.
2. **mlx_loop**: Inicia el bucle principal de eventos, que permite que la ventana sea interactiva y responda a las acciones del usuario.

## *Funciones de manejo de imágenes*

### mlx_xpm_file_to_image

`void *mlx_xpm_file_to_image(void *mlx_ptr, char *filename, int *width, int *height);`

Permite cargar una imagen en formato XPM desde un archivo y crear una estructura de imagen en memoria. (XPM es un formato de imagen en texto plano que se utiliza comúnmente en aplicaciones gráficas.)

Recibe el puntero `mlx_ptr`, una cadena de caracteres `filename` con la ruta y el nombre del archivo .xpm a cargar, y dos punteros a int donde se almacenarán las dimensiones de la imagen cargada.

Una vez ha cargado la imagen en nuestra memoria, devuelve un puntero al área de memoria donde la ha cargado.

### mlx_put_image_to_window

`int mlx_put_image_to_window(void *mlx_ptr, void *win_ptr, void *img_ptr, int x, int y);`

Una vez hemos pasado el *sprite* que teníamos en .xpm a imagen, la función anterior no carga la imagen en la ventana. Para hacerlo, debemos llamar a mlx_put_image_to_window.

Esta función recibe, además del puntero `mlx_ptr`, y el puntero a la ventana `win_ptr`, el puntero a la imagen `img_ptr` que resulta de usar la función anterior, y dos enteros: x e y con las coordenadas en las que colocar la imagen.

Hay que tener en cuenta el orden en que colocamos las imágenes en la ventana, considerando que van a mostrarse de abajo a arriba en orden de procesador.


### Otras

- **mlx_new_image**: Crea una nueva imagen en memoria con un tamaño específico.
- **mlx_get_data_addr**: Obtiene un puntero a los datos de la imagen. Permite acceder y modificar los píxeles directamente en la memoria.
- **mlx_destroy_image**: Libera la memoria utilizada por una imagen creada con **`mlx_new_image`**.

## *Funciones de manejo de eventos de teclado y ratón*

### mlx_loop

`int mlx_loop(void *mlx_ptr);`

Se utiliza para iniciar el bucle principal de eventos. Esta función es responsable de procesar y gestionar los eventos de la ventana, como interacciones del usuario, teclas pulsadas, eventos de ratón, redibujado de la ventana, entre otros.

Generalmente se llama después de haber configurado la ventana, los eventos y otros elementos necesarios para la aplicación gráfica. Una vez llamada, el bucle de eventos se inicia y se ejecuta de forma continua hasta que se produzca una acción que detenga la ejecución del programa, como cerrar la ventana.

### mlx_key_hook

`int	mlx_key_hook (void *win_ptr, int (*funct_ptr)(), void *param);`

Permite asignar una función de control de eventos a una acción específica del teclado.

Es decir, es la función con la que indicaremos qué hay que hacer cuando, mientras nuestro programa esté en ejecución, y por tanto la ventana esté abierta, se pulse una tecla.

Recibe como parámetro el `win_ptr`, la función de control de eventos `funct_ptr` que se tiene que ejecutar, y un puntero `param`a un parámetro adicional y opcional. 

`int funct_ptr(int keycode, void *param);`

La función de control de eventos, a su vez, recibe un entero con el  el KEY CODE de la tecla presionada, y un puntero al parámetro adicional `param`. 

Respecto a `param`, Enrique lo que ha hecho es pasarle un puntero a la estructura en la que ha ido almacenando la información del videojuego.

- Las Keycodes de MacOS
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/22ecc914-b41e-4bca-91e6-629936832f19/Untitled.png)
    

### mlx_hook

`int	mlx_hook(void *win_ptr, int event, int event_mask, int (*funct)(), void *param);`

Es más general que la anterior, no sólo afecta a teclas. Por ejemplo, si queremos que el juego se cierre al pulsar el botón de cerrar la ventana, deberemos usar esta función.

Recibe: 

- El puntero a la ventana `win_ptr.`
- Un entero `event` con el evento al que va a ir asociada la función de control. En el caso de cerrar la ventana, el evento es el 17.
- Una máscara de eventos`event_mask` que filtrará los eventos que desencadenarán la ejecución de la función de control. Hay algunas constantes definidas en miniLibX, como `mlx_key_press_mask` (máscara para eventos de tecla presionada), `mlx_button_press_mask` (máscara para eventos de botón del ratón presionado), `mlx_expose_mask` (máscara para eventos de exposición de la ventana), etc.
- Un puntero a la función `funct` de control de eventos a ejecutar cuando ocurra el evento especificado.
- Y el puntero a parámetro adicional `param`.

### **mlx_loop_hook**

`int mlx_loop_hook(void *mlx_ptr, int (*funct_ptr)(), void *param);`

Permite ejecutar una función en cada iteración del bucle principal de eventos. Puede ser útil para controlar eventos de teclado continuos, como mantener presionada una tecla.

Tiene también otras funciones para manejo de eventos del teclado y el ratón, y el manejo de colores e imágenes en formatos específicos.

### **mlx_mouse_hook**

`int funct_ptr(int button, int x, int y, void *param);`

Asigna una función de control de eventos a una acción específica del ratón, como hacer clic o mover el cursor. Esta función se ejecutará cuando se produzca el evento correspondiente. Además de recibir un entero con el botón del ratón a clickar (`1` para el izquierdo, `2` para el central y `3` para el derecho) recibe las coordenadas donde se ha producido el click.