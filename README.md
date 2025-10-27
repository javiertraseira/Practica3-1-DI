# Práctica 3.1 Pantalla de login

El objetivo de esta práctica es diseñar y desarrollar una interfaz gráfica funcional de inicio de sesión que integre aspectos fundamentales de la arquitectura Modelo–Vista–Controlador (MVC) y la conexión a una base de datos mediante JDBC. Además se trabajarán aspectos fundamentales de la usabilidad de una interfaz mediante la disposición y validación de sus campos.

## Parte 1

Crea una pantalla de **login** sencilla que solicite el nombre de un usuario y su contraseña; propón por el momento un listado de usuarios y contraseñas dentro del código (recuerda usar el `modelo` y `vistacontrolador` adecuadamente.)
- Si el usuario es correcto, deberá crear una nueva ventana mostrando que te has logueado mostrando el nombre del usuario y un botón que permita cerrar sesión y volver a la pantalla de logueo inicial.
- Agrégale un icono a tu aplicación.
- En caso contrario deberá mostrar un mensaje indicando que el logueo no ha sido correcto y volver a solicitar las credenciales. 
- Ten en cuenta criterios de **usabilidad** en todo momento. Utiliza *Tooptips* para los campos.
- *Opcional*: Hacer que la ventana vibre brevemente en caso de que las credenciales sean incorrectas.

Agrega y utiliza de forma correcta las clases en los paquetes *VistaControlador* y *Modelo* para organizar las funciones de las clases de tu aplicación.

![](media/05a6f0e7b87c4893f589def93ec7388d.png) ![](media/97e6f7691fc01c201777beb206893ea7.png)

## Parte 2

Amplía el ejercicio anterior agregando un acceso a una **Base de Datos relacional** sencilla que conectes mediante **JDBC**, y que contenga el nombre de los usuarios y sus contraseñas para realizar dicha verificación:
- Puedes utilizar como motor de la Base de Datos *Mysql*, *MariaDB* o *Derby*.
- Recuerda agregar la dependencia JDBC correspondiente en el fichero `pom.xml`.
- Implementa una clase auxiliar de conexión (por ejemplo, `metodosbd`) dentro del paquete modelo, encargada de gestionar las operaciones básicas con la base de datos:
	- Conectar y desconectar la BD.
	- Consultar la existencia de usuarios y contraseñas.
	- Insertar nuevos registros.
	- Controlar y mostrar los errores de conexión o SQL de forma comprensible para el usuario.


- Añade en la pantalla de login un botón o enlace para *crear una cuenta nueva* que abra una segunda ventana destinada al registro de nuevos usuarios. En dicha ventana deberá requerirse la siguiente información:
	-  **Nombre de usuario** y **contraseña**, aplicando **criterios de usabilidad**: campos claros, mensajes de ayuda y *Tooltips*, validaciones visuales (por ejemplo, colores o mensajes en línea de los campos).
	-  Información *opcional*: nombre, apellidos, fecha de nacimiento y correo electrónico. Por el momento no deberán validarse.

- Asegúrate de que el fichero `jar` resultante tiene incluídas todas las dependencias necesarias y pueda ejecutarse desde la línea de comandos. 
	- Edita para ello el fichero `pom.xml` para utilizar el plugin *maven-assembly-plugin* para incorporar las dependencias.

![](media/702a2963751b73f63199fb0a32c401ee.png)


## Pruebas (testing)

### Parte 1 y 2

| ID Caso Prueba | Descripción Caso de Prueba                     | Entrada                                 | Salida Esperada                                                           | Resultado   |
|----------------|-----------------------------------------------|-----------------------------------------|---------------------------------------------------------------------------|-------------|
| 01             | Validaciones del botón "Loguear"               | Escribir texto en los campos Usuario y Password     | Si falta alguno de los campos no podrá continuar                  | OK/No cumple|
| 02             | Comprobación del checkbox 'Mostrar'           | Marcar/desmarcar checkbox     | Se muestra o no la contraseña con asteriscos                      | OK/No cumple|
| 03             | Intento de login con credenciales incorrectas  | Introducir usuario o contraseña no válidos                        | Se muestra un mensaje de error claro y comprensible, y se permite volver a intentar  | OK/No cumple |
| 04             | Conexión con la BD de datos                          | Iniciar una consulta desde la ventana   | Se conecta correctamente a la BD elegida | OK/No cumple|
| 05             | Gestión básica de la conexión con la BD de datos                          | Iniciar una consulta desde la ventana   | Se muestran y gestionan errores de conexión con la BD | OK/No cumple|
| 06             | Validación del campo usuario y password    | Escribir texto en los campos Usuario y Password     | Se valida en la BD que el usuario y contraseña existen | OK/No cumple|
| 07             | Ventana principal                     | Se abre la ventana principal al validarse el usuario/contraseña | Se muestra el nombre del usuario logueado en la ventana | OK/No cumple|
| 08             | Ventana crear nuevos usuarios                        | Hacer clic en el botón/enlace crear cuenta nueva   | Se abre una ventana de nuevos usuarios que tiene todos los campos principales y opcionales requeridos | OK/No cumple|
| 09             | Validación del campos usuario y password ventana nuevos usuarios   | Escribir texto en los campos Usuario y Password     | Se validan los campos introducidos y la contraseña duplicada | OK/No cumple|
| 10             | Validación visual en el formulario de registro | Introducir valores incorrectos o vacíos en los campos de registro | Se muestran mensajes o colores indicando errores o confirmaciones visuales en campos (usabilidad)           | OK/No cumple |
| 11             | Estructura del proyecto                        | N/D   | Se utiliza la división por paquetes del MVC; VistaControlador y Modelo para organizar las clases usando el modelo de objetos de forma apropiada | OK/No cumple|
| 12             | Comprobación fichero `jar`                        | Proyecto a empaquetar   | Se genera y prueba el fichero `jar` empaquetado con todas sus dependencias y que funciona correctamente | OK/No cumple|
| 14             | Comprobación del icono de la aplicación        | Ejecutar la aplicación                                            | La aplicación muestra un icono personalizado en la barra de título o barra de tareas | OK/No cumple |
| 15             | Cierre de sesión desde la ventana principal    | Pulsar el botón "Cerrar sesión" en la ventana principal           | Se cierra la ventana principal y se regresa a la pantalla de login                   | OK/No cumple |
| 16             | Verificación de Tooltips y mensajes de ayuda   | Colocar el puntero sobre los campos de texto y botones            | Se muestran Tooltips o mensajes explicativos en los campos correspondientes (usabilidad)         | OK/No cumple |
| 17             | Usabilidad general: diseño, tamaño y legibilidad textos | Observar disposición interfaz y textos           | El tamaño de la fuente, contraste y la disposición de los elementos son adecuados para una lectura clara y accesible | OK/No cumple |










