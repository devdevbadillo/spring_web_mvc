# Spring Web MVC

## Tabla de Contenido

- [¿Qué es Spring Web MVC?](#qué-es-spring-web-mvc)
- [Mapeo de solicitudes](#mapeo-de-solicitudes)

## ¿Qué es Spring Web MVC?

Spring Web MVC es un framework dentro del ecosistema de Spring Framework para el desarrollo de aplicaciones web. Esta construcción se realiza siguiendo el patrón de diseño **Modelo-Vista-Controlador (MVC)**, lo cual permite una segregación de responsabilidades al dividir nuestra aplicación en tres capas interconectadas:

- Model: Representa las entidades de la aplicación y la lógica de negocio. Esta capa es la responsable de manejar el estado de la aplicación.
- View: Es la capa responsable de renderizar la UI al usuario con los datos obtenidos recuperados por el controlador.
- Controller: Es la capa encargada de manejar y validar las interacciones del usuario con la aplicación. El proceso que realiza es la interacción con el modelo para recuperar o actualizar datos y selecciona la vista adecuada para su presentación en la UI.

## Mapeo de solicitudes

La anotación @RequestMapping puede ser usada a nivel de clase para expresar asignaciones compartidas o también a nivel de métododo para el mapeo de solicitudes entrantes hacía métodos definidos dentro del controlador dependiendo de su coincidencia por URL, método HTTP, etc.

> Anotación @RequestMapping a nivel de método

```
@Controller
public class AppController {
	 @RequestMapping(value = "/java/hello-world", method = RequestMethod.GET)
	 public String getMessage() {
			// ...
	 }
}
```

Existe anotaciones que son variantes de la anotación @RequesMapping para mapear solicitudes entrantes de acuerdo a su método HTTP. Esto ayuda a tener una mejor legibilidad del código y agilizar la configuración del mapeo a nivel de método, pues, muchas veces solo se tratará de manejarlo por su método HTTP y su concidencia por URL.

> [!NOTE]
> La configuración del mapeo de solicitudes puede recibir otros atributos como los parámetros de la solicitud, los encabezados y los media types.

Anotaciones para el mapeo de solicitudes dependiendo su método HTTP: 
- @GetMapping
- @PostMapping
- @PutMapping
- @DeleteMapping
- @PatchMapping


> Anotación @RequestMapping a nivel de clase
```
@Controller
@RequestMapping("/java")
class AppController {

	@GetMapping("/hello-world")
	public String getMessage() {
		// ...
	}
}
```

> Anotación @PathVariable

La anotación @PathVariable nos permite extraer segmentos de la url a la cuál se le esta realizando la petición, esto nos ayuda a capturar ese valor y utilizarlo en el método del controlador. Es comunmente utilizado para la obtención de datos de acuerdo a su identificador.

```
@RequestMapping("/persons")
class PersonController {

	@GetMapping("/{id}")
	public Person getPerson(@PathVariable Long id) {
		// ...
	}
}
```

> Consumable Media Types

Cómo se menciono anteriormente, el mapeo de solicitudes admite como atributo los media types, comunmente usados para delimitar los formatos de los datos que un endpoint o controlador puede aceptar cuando recibe una solicitud HTTP. Esto se especifica mediante la cabecera Content-Type en la petición y permite a Spring determinar cómo debe interpretar los datos que recibe.

> [!NOTE]
> La clase MediaType proporciona constantes para definir estos tipos de medios, los más utilizados son: 
> - MediaType.APPLICATION_JSON_VALUE
> - MediaType.APPLICATION_XML_VALUE

```
@Controller
@RequestMapping("/java")
class AppController {

	@GetMapping("/hello-world", consumes = MediaType.APPLICATION_JSON_VALUE )
	public String getMessage() {
		// ...
	}
}
```

> [!NOTE]
> Se puede usar la negación para aceptar todo media type, excepto el indicado.
> - @GetMapping("/hello-world", consumes = !MediaType.APPLICATION_JSON_VALUE )
>   
> De esta manera, se realizará el mapeo hacía GET: /java/hello-world cuya solicitud del lado del cliente deberá de tener en su cabecera del Content-Type un tipo distinto a application/json para la ejecución del método.
> - También es aplicable con el atributo **produces**. 

> Producible Media Types 

El atributo **produces** en el mapeo de solicitudes es usado para especificar los tipos de contenido que un controlador o endpoint puede generar como respuesta. Esto se relaciona directamente con la cabecera HTTP Accept que envía el cliente y la cabecera Content-Type que se incluye en la respuesta.

```
@Controller
@RequestMapping("/java")
class AppController {

	@GetMapping("/hello-world", produces = MediaType.APPLICATION_JSON_VALUE )
	public String getMessage() {
		// ...
	}
}
```

> [!NOTE]
> Se puede definir más de un media type para un delimitación de formatos menos restrictiva o que se acomple a las necesidades del proyecto.
> - @GetMapping("/hello-world", produces = { MediaType.APPLICATION_JSON_VALUE, MediaType.APPLICATION_XML_VALUE } ). 
> - También es aplicable con el atributo **consumes**.


> [!IMPORTANT]
> Se puede declarar el atributo **consumes** o **produces** en el @RequesMapping a nivel de clase. Sin embargo, a diferencia de la mayoría de los demás atributos de mapeo de solicitudes, cuando se usa a nivel de clase, y también a nivel de método, entonces, se anula la declaración a nivel de clase en lugar de extenderla.


```
@Controller
@RequestMapping("/java", produces = MediaType.APPLICATION_XML_VALUE)
class AppController {

 	// Anula el atributo compartido asignado por el RequestMapping
	@GetMapping("/hello-world", produces = MediaType.APPLICATION_JSON_VALUE )
	public String getMessage() {
		// ...
	}
}
```
