# Práctica 3.1 Login, registro de usuarios y conexión a Aiven

Diseña una aplicación Java Swing que permita crear una cuenta, iniciar sesión y cerrar sesión. Aplicarás los criterios de usabilidad de la **UT3.1** y la conexión mediante JDBC de la **UT3.2**, utilizando **MySQL en Aiven**.

Continuarás con lo trabajado previamente: separación entre interfaz y lógica, Maven, FlatLaf, pruebas con **JUnit 4** y **AssertJ Swing**, documentación y uso de ramas.

## Preparación del proyecto

Crea un proyecto Maven de Apache NetBeans llamado `practica3-1` dentro de `SOL` del repositorio. Utiliza la versión de *Java 21* LTS en tu proyecto.

 Organiza las clases en los dos paquetes utilizados anteriormente:

```text
Modelo
VistaControlador
```

Mantendremos esta separación básica; no se exige dividir la vista y el controlador en paquetes independientes.

Utiliza las ramas `parte-1`, `parte-2` y `parte-3`. Realiza commits descriptivos e integra cada parte comprobada en `main`. Registra en Issues los errores que encuentres durante las pruebas y referencia su corrección en los commits.

Añade mediante Maven las dependencias de FlatLaf, MySQL Connector/J y, con ámbito `test`, JUnit 4 y AssertJ Swing compatibles con lo utilizado en la práctica 2.4. No mezcles las anotaciones de JUnit 4 con las de JUnit Jupiter.


## Parte 1 Login con datos de prueba

Crea mediante el editor visual de NetBeans una ventana **login** sencilla que solicite el nombre de un usuario y su contraseña:

- Usuario: `JTextField` y Contraseña: `JPasswordField`.
- Casilla **Mostrar contraseña** o que use la propiedad `showRevealButton` desmarcados inicialmente.
- Botón **Iniciar sesión**.
- Enlace a **Crear cuenta**, cuya funcionalidad completarás en la parte 2.
- Una zona para mensajes de estado o validación.

Configura *FlatLaf* antes de crear las ventanas y añade un título e **icono** propio a la aplicación, así como en las ventanas que creas conveniente: 
- El repositorio proporciona una carpeta *Icons* con imágenes que puedes utilizar en la aplicación.
- Copia las imágenes que quieras utilizar dentro de los recursos del proyecto Maven, en la ruta: `src\main\resources\icons\`
- Para iconos *SVG* puedes utilizar FlatSVGIcon, añadiendo la dependencia de FlatLaf Extras.  

Para esta primera parte se utilizarán **cuentas y credenciales ficticias** almacenadas en memoria dentro del modelo.

Si las credenciales son correctas, se abrirá una **ventana principal** que muestre el nombre del usuario y un botón **Cerrar sesión**. Evita dejar ventanas de login duplicadas. Al cerrar sesión, elimina la sesión y vuelve al login con la contraseña vacía y oculta.

### Usabilidad básica

- Asocia etiquetas claras a los campos y establece un orden de tabulación lógico.
- Permite iniciar sesión con el botón y con la tecla **Intro**.
- Añade ayudas breves cuando aporten información; las etiquetas deben seguir siendo comprensibles sin depender del tooltip.
- No comuniques los errores únicamente mediante el color: añade texto.
- Comprueba legibilidad, distribución y navegación con teclado.
- De forma opcional, puedes hacer que la ventana vibre brevemente cuando las credenciales sean incorrectas.

![](media/05a6f0e7b87c4893f589def93ec7388d.png) ![](media/97e6f7691fc01c201777beb206893ea7.png)

## Parte 2 Registro y persistencia en MySQL mediante Aiven

Amplía el ejercicio anterior agregando un acceso a una **Base de Datos relacional** sencilla que conectes mediante JDBC, y que contenga el nombre de los usuarios y sus contraseñas para realizar dicha verificación:

### Preparar la conexión en Aiven

Utiliza un servicio **MySQL de Aiven** del entorno de prácticas. Sigue la preparación guiada de la UT3.2 para:

1. Disponer del servicio y de una base de datos de prácticas llamada `usuarios`.
2. Obten el **host y puerto reales** del servicio. 
3. Incorpora *MySQL Connector/J* al proyecto mediante Maven.
4. Se recomienda crear una clase de pruebas sencilla para probar la conexión y una consulta sencilla antes de integrarlo en la aplicación.

En esta primera práctica utilizaremos la **conexión JDBC básica**, sin configurar opciones TLS ni verificar expresamente la identidad del servidor.

### Modelo de datos

Se recomienda utilizar una base de datos con dos tablas:

- **usuarios**: almacenará las credenciales necesarias para identificar al usuario.
  - Identificador
  - Nombre de usuario
  - Contraseña

- **info**: almacenará información adicional asociada al usuario.
  - Identificador del usuario
  - Nombre
  - Apellidos
  - Fecha
  - Correo electrónico

Cada registro de **info** estará asociado a un usuario existente mediante su identificador. La información adicional será opcional, por lo que puede existir un usuario que todavía no tenga datos asociados en esta tabla.

### Organización de la lógica

Una vez implementada la conexión JDBC, deberás sustituir la comprobación de las credenciales ficticias por consultas a la base de datos creada anteriormente.

Debes de utilizar la distribución *Modelo-VistaControlador*, y utilizar esta distribución orientativa dentro de los paquetes existentes:

| Clases | Responsabilidad |
|---|---|
| `Usuario` | Representar los datos del usuario (identificador, nombre de usuario y demás atributos) |
| `ConexionBD` | Centralizar la URL, el usuario y la contraseña en variables de clase y abrir conexiones. |
| `GestionUsuariosBD` | Consultar usuarios, comprobar credenciales e insertar nuevos registros mediante JDBC. |
| `ValidadorUsuario` | Normalizar y validar los datos recibidos, sin utilizar Swing ni acceder a la base de datos. |

Las ventanas en *VistaControlador* recogerán datos, responderán a eventos y presentarán resultados. El *Modelo* no mostrará `JOptionPane` ni accederá a componentes gráficos.

Utiliza `PreparedStatement` para las consultas con datos del formulario y `try-with-resources` para cerrar conexiones, sentencias y resultados.

### Ventana crear cuenta

Desde **Crear cuenta**, se debe abrir una ventana para el registro de nuevos usuarios. 

En dicha ventana deberá requerirse la siguiente información:
-  **Nombre de usuario** y **contraseña**. Campos obligatorios y que **deberán validarse**.
-  Otro campo de **repetición de contraseña** que deberá de validar si coincide con la primera.
-  Información **opcional**: *nombre*, *apellidos*, *fecha de nacimiento* y *correo electrónico*. Estos campos por el momento **no deberán validarse**.

Respecto el campo fecha:
- Utiliza un `JFormattedTextField` para introducir la fecha. Desde NetBeans has de configurar el formato de entrada usando *formatterFactory* (dd/MM/YY). 
- Para guardarla puedes convertirla a `java.sql.Date`. Si usas una cadena intermedia, emplea yyyy-MM-dd.

Para los campos que deben validarse:
- El usuario tendrá entre 3 y 30 caracteres y admitirá letras ASCII, números y guion bajo. Guarda el identificador sin espacios exteriores y en minúsculas, para que `Alumno1` y `alumno1` sean la misma cuenta.
- La contraseña tendrá al menos 8 caracteres y deberá coincidir exactamente con su repetición.
- No se podrá registrar un nombre de usuario ya existente.  

No te olvides también de aplicar aquí **criterios de usabilidad**: campos claros, mensajes de ayuda y *Tooltips*, validaciones visuales (por ejemplo, colores o mensajes en línea de los campos).

![](media/702a2963751b73f63199fb0a32c401ee.png)

### Empaquetado

Añade maven-assembly-plugin dentro de <build><plugins> en el archivo pom.xml. Si estas secciones ya existen, reutilízalas. 

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.7.1</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass>com.ejemplo.Main</mainClass>
            </manifest>
        </archive>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

No te olvides de sustituir *com.ejemplo.Main* por el nombre completo de la clase principal de tu aplicación, incluyendo su paquete.

Desde NetBeans utiliza **Clean and Build**, comprueba `BUILD SUCCESS` y ejecuta el JAR desde una terminal. 

## Parte 3. Pruebas y documentación

### Pruebas unitarias con JUnit 4

Añade las dependecias necesarias a tu fichero `pom.xml`.

Crea las clases de prueba en `Test Packages`, correspondientes a `src/test/java`. Estas pruebas se ejecutarán sin abrir ventanas ni conectarse a Internet.

Implementa, como mínimo, cuatro casos independientes sobre la lógica de validación:

| Caso | Comprobación |
|---|---|
| Usuario vacío o formado solo por espacios | Se devuelve un error de validación. |
| Usuario con espacios exteriores y mayúsculas | Se obtiene el identificador normalizado. |
| Contraseña de menos de ocho caracteres | Se rechaza al registrar una cuenta. |
| Contraseña y repetición diferentes | Se devuelve un error de validación. |

Incluye entradas válidas e inválidas. Utiliza aserciones; imprimir un resultado en consola no comprueba automáticamente el comportamiento. Si los métodos devuelven un mensaje cuando existe un error y null cuando el dato es válido, puedes utilizar `assertNotNull()` y `assertNull()`.

En JUnit 4 utiliza `org.junit.Test`, `org.junit.Before` y `org.junit.After` cuando proceda, y métodos de prueba `public void`. Mantén las dependencias de pruebas con ámbito `test`.

### Prueba con AssertJ Swing

La prueba deberá:

1. Abrir la ventana de login.
2. Dejar vacíos usuario y contraseña.
3. Pulsar Iniciar sesión.
4. Comprobar el mensaje de campo obligatorio.
5. Cerrar el mensaje y comprobar que el foco vuelve al campo usuario.
6. Comprobar que el login continúa visible y no se ha abierto la ventana principal.

Si presentas el error en una etiqueta en lugar de un diálogo, comprueba el texto de esa etiqueta mediante su fixture.

Este caso debe funcionar **sin conexión** a Aiven, porque los datos se validan antes de consultar la base de datos. El constructor del formulario tampoco debe abrir conexiones.

### Comprobaciones de integración y pruebas manuales

Las pruebas anteriores no demuestran que JDBC funcione. Comprueba también el recorrido real contra Aiven: registrar una cuenta ficticia, cerrar la aplicación, volver a abrirla e iniciar sesión con esa cuenta.

Crea `TEST/casos-prueba.md` y registra los resultados de la siguiente tabla.

Los casos **U** y **G** corresponden a las pruebas automatizadas anteriores. Los casos **I** y **M** pueden ejecutarse manualmente.

| ID | Caso de prueba | Entrada / Acción | Resultado esperado | Resultado |
|---|---|---|---|---|
| **Pruebas unitarias (JUnit 4)** | | | | |
| U01 | Usuario obligatorio | Usuario vacío o solo espacios | Devuelve un error de validación | Pendiente |
| U02 | Normalización | Introducir `  ALUMNO1  ` | Devuelve `alumno1` | Pendiente |
| U03 | Longitud de contraseña | Probar 7 y 8 caracteres | Rechaza 7 y acepta 8 | Pendiente |
| U04 | Confirmación | Introducir contraseñas distintas | Devuelve un error de validación | Pendiente |
| **Prueba gráfica (AssertJ Swing)** | | | | |
| G01 | Login vacío | Iniciar sesión sin introducir datos | Mensaje de error, foco en usuario y login visible | Pendiente |
| **Pruebas de integración (JDBC y Aiven)** | | | | |
| I01 | Conexión JDBC | Realizar una consulta sencilla | La conexión y la consulta funcionan | Pendiente |
| I02 | Registro y persistencia | Registrar un usuario, reiniciar e iniciar sesión | El usuario permanece almacenado y puede acceder | Pendiente |
| I03 | Credenciales incorrectas | Introducir usuario inexistente o contraseña incorrecta | Informa del error y permite reintentar | Pendiente |
| I04 | Usuario duplicado | Registrar dos veces el mismo nombre, variando mayúsculas | Impide crear la segunda cuenta | Pendiente |
| I05 | Datos opcionales | Registrar un usuario con información adicional | Se almacena asociada al usuario correcto | Pendiente |
| I06 | Error de conexión | Simular un fallo de conexión a Aiven | Muestra un mensaje comprensible sin cerrar la aplicación | Pendiente |
| **Pruebas manuales de interfaz** | | | | |
| M01 | Campos obligatorios del login | Dejar la contraseña vacía | Muestra un mensaje de campo obligatorio | Pendiente |
| M02 | Validaciones del registro | Introducir usuario inválido, contraseña corta o confirmación distinta | Identifica los errores e impide registrar | Pendiente |
| M03 | Mostrar contraseña | Activar y desactivar la opción | Permite mostrarla y ocultarla correctamente | Pendiente |
| M04 | Campos opcionales vacíos | Registrar sin introducir información adicional | Permite completar el registro | Pendiente |
| M05 | Fecha de nacimiento | Introducir una fecha válida y otra incorrecta | Guarda la válida y rechaza la incorrecta | Pendiente |
| M06 | Navegación | Utilizar Tab, Intro y volver del registro al login | Navegación correcta y sin ventanas duplicadas | Pendiente |
| M07 | Cierre de sesión | Iniciar sesión y pulsar Cerrar sesión | Regresa al login con la contraseña vacía y oculta | Pendiente |
| M08 | Empaquetado | Ejecutar el JAR fuera de NetBeans | La interfaz, los iconos y las funcionalidades siguen funcionando | Pendiente |

Sustituye `Pendiente` por:

- **OK:** se obtiene el resultado esperado.
- **No cumple:** existe un fallo.
- **No ejecutado:** no se ha realizado la prueba.
- **No aplica:** únicamente para la ampliación opcional no implementada.

### Documentación y entrega

En `docs/index.md` incluye descripción, funcionalidades, capturas, responsabilidades de los paquetes, preparación de Aiven, valores del url accedido —sin sus valores secretos—, ejecución del JAR y ejecución de pruebas. Documenta con Javadoc las clases principales del modelo y al menos dos de sus métodos.

Añade al README un enlace a la documentación y al informe de pruebas. Si mantienes GitHub Pages de las prácticas previas, comprueba su publicación.

Antes de entregar:

- [ ] Maven, FlatLaf y los paquetes siguen la organización trabajada.
- [ ] El código respeta la separación Modelo–VistaControlador.
- [ ] Login, registro y cierre de sesión funcionan con Aiven.
- [ ] Las reglas de validación están en `ValidadorUsuario`.
- [ ] La conexión utiliza el host y puerto reales de Aiven y las credenciales configuradas.
- [ ] Los datos de conexión están centralizados en las variables de clase de `ConexionBD`.
- [ ] Los campos opcionales pueden quedar vacíos y los informados se guardan.
- [ ] La versión publicada del código, las capturas y la documentación utilizan valores de ejemplo para las credenciales.
- [ ] Se validan los obligatorios, se admiten opcionales vacíos y se evitan duplicados.
- [ ] Las conexiones, sentencias y resultados JDBC se cierran correctamente.
- [ ] Pasan los cuatro casos unitarios y al menos una prueba con AssertJ Swing.
- [ ] Se han ejecutado y documentado las comprobaciones reales con Aiven.
- [ ] Se ha realizado la revisión de usabilidad según los requerimientos y registrado sus conclusiones.
- [ ] El JAR se ejecuta fuera de NetBeans y la documentación permite reproducirlo.
- [ ] Las ramas están integradas en `main` y las incidencias quedan identificadas.








