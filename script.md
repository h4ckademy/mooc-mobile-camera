# Módulo 2. Cámara
La cámara es algo propio de los dispositivos móviles que no tenemos en desktop. Tener esta característica en nuestro dispositivo móvil nos permite desarrollar aplicaciones que conectan con el mundo que nos rodea.

Para ejemplificar el uso de la cámara vamos a desarrollar una aplicación que toma una fotografía. Sobre la imagen capturada, podemos aplicar una serie de filtros: blanco y negro, negativo, sepia. Además, podemos importar fotos de nuestra galería para aplicar los filtros.

## Plantilla
Para empezar, partimos de la plantilla que venimos usando que tiene preparado un esqueleto de la aplicación en HTML / CSS, y las librerías básicas cargadas: cordova y Fastclick.

Tenemos un HTML básico con una cabecera y un pie de página. Y cargamos el CSS de nuestra app y las librerías JavaScript necesarias. En el CSS aplicamos un estilo para el body con márgenes, padding y tipografía. Lo mismo para la cabecera y el pie de página.

Lanzamos el servudor de PhoneGap con ```phonegap serve``` desde la consola. Y hacemos la conexión con el dispositivo usando la PhoneGap Developer App.

## Tomar una foto

El plugin *camera* de PhoneGap nos proporciona un método *getPicture* que nos permite acceder a la cámara para hacer una fotografía. Tenemos que pasarle 3 parámetros:
- Una función que se ejecuta si tomamos la fotografía con éxito
- Una función que se ejecuta si hay un error o el usuario cancela la acción de tomar fotografía
- Una estructura de datos de diccionario con opciones sobre la captura

Creamos un objeto *img* en nuestra página y le aplicamos estilos con CSS para que ocupe tamaño en la pantalla. Y añadimos un botón para tomar una fotografía.

Creamos una función *tomarFoto* donde vamos a invocar el método *getPicture* de la cámara.

A la función de éxito *fotoTomada* se le pasa la fotografía y, para poder pintarla en pantalla, enlaza la URL generada al objeto imagen.

A la función de error *errorAlTomarFoto* se le pasa un diccionario con información con el error que ha sucedido. Simplemente lo imprimimos en la consola.

El diccionario de opciones nos ayuda a describir algunas características sobre la captura de la foto. Las más relevantes para nosotros son:
- Indicamos la calidad de la foto 0 - 100: más calidad, mayor tamaño y procesamiento de imagen
- Podemos definir dimensiones (ancho y alto) de la foto, ya que al tamaño original es demasiado grande para manejarla en nuestro programa
- El destino de la foto sirve para indicar si queremos que a nuestra función de éxito se le pasen los datos en bruto de la foto, o un enlace a la foto en el disco
- Hay un parámetro booleano que nos ayuda a corregir la orientación de la foto porque depende de cómo esté montado el hardware en nuestro dispositivo

Finalmente enlazamos la acción de click sobre el botón (sabemos que el móvil es tap) para que se ejecute la función de tomarFoto.

Vamos a ver cómo funciona en el móvil.

## Usando el canvas
Las imágenes de una pantalla (de un móvil, computador, televisión) se dibujan usando pequeños puntos llamados píxeles. Un computador o terminal móvil maneja la información de la imagen como la información de cada píxel de la pantalla. Una forma habitual es manejar la información de color de cada píxel usando la escala RGB (del inglés *Red-Green-Blue*), representada con un valor de 0 a 255 (puede parece un valor un poco raro, pero es muy normal a sabiendas de cómo manejan información los computadores). Con una combinación de valores de rojo, verde y azul podemos representar cualquier color con mucha precisión. Además de estos 3 valores, para cada píxel indicamos un cuarto valor que indica la transparencia (normalmente definido como *alpha*), de forma que 0 es totalmente transparente y 1 es el color plano, sin ninguna transparencia.

Para procesar la imagen que hemos tomado con la cámara, vamos a usar un elemento especial de HTML: el canvas. Es un lienzo donde podemos pintar cualquier cosa, y se utiliza por ejemplo para desarrollar videojuegos y hacer animaciones. En nuestro caso, lo vamos a usar para pintar la imagen que hemos tomado con la cámara, y poder acceder a la información de cada píxel de la imagen.

En nuestro HTML vamos a sustituir la imagen por un elemento canvas.

Vamos a modificar nuestra función *fotoTomada* para crear un nuevo objeto imagen y aplicarle la URL donde se almacenó la fotografía. Pero este no es un proceso instantáneo sino que sucede de forma asíncrona. Por tanto, debemos registrar un escuchador (callback) para que cuando la imagen esté cargada invoque una nueva función, *pintarFoto*, que pinte la imagen en el canvas.

En la función para *pintarFoto* accedemos al contexto 2D de nuestro canvas y pintamos la imagen con el método *drawImage*.

Vamos a ver cómo en el móvil sigue funcionando igual.

## Filtros
En nuestra app y tenemos un canvas donde pintamos la foto. De esta forma, podemos acceder a la información de color y transparencia de cada píxel. Y con esta información podemos ejecutar funciones con filtros que modifican el color. ¡Vamos a ello!

Vamos a añadir botones en el HTML/CSS para aplciar los filtros de color sobre la imagen. Contamos con una librería JS nueva, *effects.js*, que nos proporciona filtros para convertir la imagen en:
- Blanco y negro (escala de grises)
- Negativo
- Sepia (es un filtro con pérdidas, es decir, que no podemos recuperar la información original de la foto)

Añadimos escuchadores a cada botón, de forma que invocamos una función *aplicarFiltro* pasando como parámetro el nombre del filtro a aplicar como está identificado en la librería *effects*.

En *aplicaFiltro* recuperamos la información de cada píxel de la imagen usando sobre el contexto 2D del canvas el método *getImageData*.

A continuación aplicamos el filtro conveniente usando la librería *effects*, donde le pasamos como parámetro el atributo *data* que contiene la información de píxeles de la imagen pintada en el canvas. La librería modifica directamente el el objeto data pasado, de forma que en *imageData* tenemos ya la imagen modificada.

Finalmente, volveremos a poner la imagen modificada sobre el canvas con *putImageData*.

Vamos a ver cómo funciona en el móvil. Aplicamos un filtro y si aplicamos otro será sobre el anterior. Vemos como el negativo queda igual si lo aplicamos dos veces.

## Cargar foto de la galería
Vamos a añadir una última funcionalidad a nuestra app: la posibilidad de aplciar filtros a una imagen de la galería.

Modificamos nuestra maqueta (HTML, CSS) para añadir un botón que permita cargar una foto de la galería.

Añadimos un escuchador al botón para que cargue una foto de la galería llamando a la función *cargarFoto*.

Volvemos a llamar a *getPicture* pero con la opción *sourceType* describiendo que sacamos la foto de la galería (PHOTOLIBRARY) en lugar de la cámara (CAMERA).

Los filtros siguen funcionando sobre imágenes de la galería :)

### Enlaces
- [Documentación del plugin de localización de Apache Cordova](http://cordova.apache.org/docs/en/6.x/reference/cordova-plugin-camera/index.html)
- [Manipulación de nivel de píxel con canvas](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial/Pixel_manipulation_with_canvas)
