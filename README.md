# Spring Web MVC

## Tabla de Contenido

- [¿Que es Spring Web MVC?](#que-es-spring-web-mvc)
- [HTTP Message Conversion](#http-message-conversion)
- [@RestController vs @Controller](#restcontroller-vs-controller)
- [Mapeo de solicitudes](#mapeo-de-solicitudes)
- [Validación de las solicitudes](#validacion-de-las-solicitudes)
- [Manejo de excepciones](#manejo-de-excepciones)

## ¿Qué es Spring Web MVC?  {#que-es-spring-web-mvc}

Spring Web MVC es un framework dentro del ecosistema de Spring Framework para el desarrollo de aplicaciones web. Esta construcción se realiza siguiendo el patrón de diseño **Modelo-Vista-Controlador (MVC)**, lo cual permite una segregación de responsabilidades al dividir nuestra aplicación en tres capas interconectadas:

- Model: Representa las entidades de la aplicación y la lógica de negocio. Esta capa es la responsable de manejar el estado de la aplicación.
- View: Es la capa responsable de renderizar la UI al usuario con los datos obtenidos recuperados por el controlador.
- Controller: Es la capa encargada de manejar y validar las interacciones del usuario con la aplicación. El proceso que realiza es la interacción con el modelo para recuperar o actualizar datos y selecciona la vista adecuada para su presentación en la UI.

## HTTP Message Conversion

Se ha hablado que el controlador es la capa encargada de manejar las solicitudes entrantes realizadas por las interacciones del usuario con la aplicación y así mismo es el encargado de proporcionar una respuesta al usuario una vez finalizada su solicitud.

Entonces, ¿De qué manera el framework de Spring Web realiza la tarea de pasar el cuerpo de una petición HTTP hacía un objeto de Java y viceversa? 

> Respuesta: Spring Web, contiene la interfaz **HttpMessageConverter**, cuyo prósito es leer y escribir el cuerpo de las solicitudes y respuestas HTTP a través de **InputStream** y **OutputStream.**

Algunas de las implementaciones más importantes de la interfaz **HttpMessageConverter**:

| Implementación                           | Media Type        | Descripción                                          |
|------------------------------------------|-------------------|------------------------------------------------------|
| `MappingJackson2HttpMessageConverter`    | `application/json`| Usa Jackson para convertir entre JSON y objetos Java |                         |
| `GsonHttpMessageConverter`               | `application/json`| Usa Gson en lugar de Jackson                         |
| `MappingJackson2XmlHttpMessageConverter` | `application/xml` | Usa Jackson XMl la conversión entre XML y objetos Java |
| `StringHttpMessageConverter`             | `text/plain`      | Para textos planos                                   |

> [!NOTE]
> - @RequestBody: convierte el cuerpo de la petición en un objeto Java.
> - @ResponseBody: convierte un objeto Java en la respuesta HTTP.

## @RestController vs @Controller 

Ambas anotaciones tienen como principal función la creación de endpoints, pero, con propositos diferentes.

> @Controller.

Es una anotación que es usada para aplicaciones que siguen el patrón diseño MVC. Debido a, su funcionalidad el cual es manejar las solicitudes entrantes y retornar como respuesta el nombre de la vista a **renderizar**

```
@Controller
public class AppController {

    @GetMapping("/app")
    public String home() {
        return "home"; // Nombre de la vista
    }
}
```

> Anotación @ResponseBody

Esta anotación le indica a Spring que el valor devuelto por el método debe ser serializado directamente al cuerpo de la respuesta HTTP, en lugar de ser tratado como el nombre de alguna vista.

```
@Controller
public class AppController {

    @GetMapping("/app")
    @ResponseBody
    public String home() {
        return Map.of("message",  "home"); // Se seraliza la respuesta debido al @ResponseBody
    }
}
```

> @RestController

Esta anotación es una combinación de **@Controller + @ResponseBody**. Es usada principalmente para la creación de  APIs RESTful.

```
@RestController
public class AppController {

    @GetMapping("/app")
    public String home() {
        return Map.of("message",  "home"); // Se serializa automaticamente la respuesta
    }
}
```

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

> Anotación @RequestParam

Está anotación nos permite capturar los parametros de consulta (query params) de la solicitud que se está realizado. Es de gran utilidad para realizar consultas personalizadas o complejas como por ejemplo una paginación de datos especificos almacenados en la aplicación

```
@RequestMapping("/persons")
class PersonController {

	@GetMapping("/get-data")
	public Person getPerson(@RequestParam("min") Long min, @RequestParam("max") Long max) {
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
	@ResponseBody
	public Map<String, String> getMessage() {
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
	@ResponseBody
	public Map<String, String> getMessage() {
		// ...
	}
}
```
 
## Validación de las solicitudes {#validacion-de-las-solicitudes}

> Anotación @RequestBody

Esta anotación permite leer y convertir toda la información del cuerpo de la solicitud entrante hacía un objeto de Java y pasarlo como parámetro al método que esta manejando dicha solicitud.

De este modo, ya se puede leer y acceder a la información que el usuario nos proporcionó por medio de alguna interación, pero, ¿Cómo se puede validar que la información esta alineada a los requerimientos de la aplicación?


> La validación de beans

Es una especificación de Java SE y Jakarta EE que permite validar objetos (beans) através de sus propiedades de forma estandarizada por medio de anotaciones declarativas.

> [!NOTE]
> Spring Boot integra de forma automática la validación de solicitudes através de la implementación de Hibernate Validator.
> 
> Dependencia necesaria:
> ```
> <dependency>
> <groupId>org.springframework.boot</groupId>
> <artifactId>spring-boot-starter-validation</artifactId>
> </dependency>
> ```

Anotaciones de validación comunes

- @NotNull: El campo no puede ser nulo
- @NotEmpty: El campo no puede ser nulo ni vacío (para colecciones, arrays y strings)
- @NotBlank: El campo String no puede ser nulo ni contener solo espacios en blanco
- @Size(min=, max=): Limita la longitud de strings, arrays, colecciones
- @Min/@Max: Establece valores mínimos/máximos para números
- @Email: Valida que el campo tenga formato de email
- @Pattern: Valida contra una expresión regular
- @Past/@Future: Valida que la fecha sea anterior/posterior a la actual
- @Positive/@PositiveOrZero: Valida números positivos
- @Negative/@NegativeOrZero: Valida números negativos

A continuación, se muestra cómo se validan los atributos **password** y **email** del objeto RegisterCredentialRequest que estaría simulando el cuerpo de alguna solicitud entrante. 

```
import jakarta.validation.constraints.Size;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Email;

public class RegisterCredentialRequest {

    @NotBlank()
    @Size(min=8, max=30)
    private String password;

    @Email()
    @NotBlank()
    private String email;

}
```

> Anotación @Valid

Está anotación tiene cómo función el indicarle a Spring que debe realizar el proceso de validación sobre el objeto anotado. 

En el ejemplo siguiente, se muestra que para el endpoint **POST: /user**, se estára recibiendo como parametro un objeto de tipo RegisterCredentialRequest después de su deserialización (@RequestBody) y además se le indica que se validarán los atributos de esta instancia de acuerdo a los lineamientos definidos (@Valid).


```
@RestController
@RequestMapping("/user")
public class UsuarioController {

    @PostMapping
    public Map<String, String> registerUser(@RequestBody @Valid RegisterCredentialRequest request) {
        ...
    }
}
```

## Manejo de excepciones
