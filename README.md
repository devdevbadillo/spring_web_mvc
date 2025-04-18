# Spring Web MVC

## Tabla de Contenido

- [¿Qué es Spring Web MVC?](#que-es-spring-web-mvc)
  - [El patrón MVC](#el-patron-mvc)
- [HTTP Message Conversion](#http-message-conversion)
  - [Implementaciones de HttpMessageConverter](#implementaciones-de-httpmessageconverter)
  - [Anotaciones para Message Conversion](#anotaciones-para-message-conversion)
- [Controladores](#controladores)
  - [@RestController vs @Controller](#restcontroller-vs-controller)
  - [@Controller](#controller)
  - [@ResponseBody](#responsebody)
  - [@RestController](#restcontroller)
- [Mapeo de solicitudes](#mapeo-de-solicitudes)
  - [@RequestMapping a nivel de método](#requestmapping-a-nivel-de-metodo)
  - [Variantes de @RequestMapping](#variantes-de-requestmapping)
  - [@RequestMapping a nivel de clase](#requestmapping-a-nivel-de-clase)
  - [Captura de variables en URL](#captura-de-variables-en-url)
    - [@PathVariable](#pathvariable)
    - [@RequestParam](#requestparam)
  - [Consumible Media Types](#consumible-media-types)
  - [Producible Media Types](#producible-media-types)
- [Validación de las solicitudes](#validacion-de-las-solicitudes)
  - [@RequestBody](#requestbody)
  - [Validación de beans](#validacion-de-beans)  
  - [Anotaciones de validación comunes](#anotaciones-de-validacion-comunes)
  - [Mensajes de error personalizados](#mensajes-de-error-personalizados)
    - [Personalización explícita](#personalizacion-explicita)
    - [Personalización con archivos de propiedades](#personalizacion-con-archivos-de-propiedades)
  - [@Valid](#valid)
  - [@Validated](#validated)
    - [Validaciones individuales](#validaciones-individuales)
    - [Validación por grupo](#validacion-por-grupo)
  - [Las clases MethodArgumentNotValidException y ConstrainViolationException](#methodargumentnotvalidexception-constraintviolationexception)
    - [Clase MethodArgumentNotValidException](#methodargumentnotvalidexception)
    - [Clase ConstrainViolationException](#constraintviolationexception)
- [Manejo de excepciones](#manejo-de-excepciones)
  - [La interfaz HandlerExceptionResolver](#handler-exception-resolver)
  - [@ExceptionHandler](#exceptionhandler)
  - [@ControllerAdvice](#controlleradvice)
  - [@RestControllerAdvice](#restcontrolleradvice)

<a id="que-es-spring-web-mvc"></a>
## ¿Qué es Spring Web MVC? 

Spring Web MVC es un framework dentro del ecosistema de Spring Framework para el desarrollo de aplicaciones web. Esta construcción se realiza siguiendo el patrón de diseño **Modelo-Vista-Controlador (MVC)**, lo cual permite una segregación de responsabilidades al dividir nuestra aplicación en tres capas interconectadas.

<a id="el-patron-mvc"></a>
### El patrón MVC

- **Model**: Representa las entidades de la aplicación y la lógica de negocio. Esta capa es la responsable de manejar el estado de la aplicación.
- **View**: Es la capa responsable de renderizar la UI al usuario con los datos obtenidos recuperados por el controlador.
- **Controller**: Es la capa encargada de manejar y validar las interacciones del usuario con la aplicación. El proceso que realiza es la interacción con el modelo para recuperar o actualizar datos y selecciona la vista adecuada para su presentación en la UI.

<a id="http-message-conversion"></a>
## HTTP Message Conversion

Se ha hablado que el controlador es la capa encargada de manejar las solicitudes entrantes realizadas por las interacciones del usuario con la aplicación y así mismo es el encargado de proporcionar una respuesta al usuario una vez finalizada su solicitud.

Entonces, ¿De qué manera el framework de Spring Web realiza la tarea de pasar el cuerpo de una petición HTTP hacía un objeto de Java y viceversa? 

> Respuesta: Spring Web, contiene la interfaz **HttpMessageConverter**, cuyo prósito es leer y escribir el cuerpo de las solicitudes y respuestas HTTP a través de **InputStream** y **OutputStream.**

<a id="implementaciones-de-httpmessageconverter"></a>
### Implementaciones de HttpMessageConverter

Algunas de las implementaciones más importantes de la interfaz **HttpMessageConverter**:

| Implementación                           | Media Type        | Descripción                                          |
|------------------------------------------|-------------------|------------------------------------------------------|
| `MappingJackson2HttpMessageConverter`    | `application/json`| Usa Jackson para convertir entre JSON y objetos Java |                         
| `GsonHttpMessageConverter`               | `application/json`| Usa Gson en lugar de Jackson                         |
| `MappingJackson2XmlHttpMessageConverter` | `application/xml` | Usa Jackson XMl la conversión entre XML y objetos Java |
| `StringHttpMessageConverter`             | `text/plain`      | Para textos planos                                   |

<a id="anotaciones-para-message-conversion"></a>
### Anotaciones para Message Conversion

> [!NOTE]
> - **@RequestBody**: convierte el cuerpo de la petición en un objeto Java.
> - **@ResponseBody**: convierte un objeto Java en la respuesta HTTP.

<a id="controladores"></a>
## Controladores

<a id="restcontroller-vs-controller"></a>
### @RestController vs @Controller 

Ambas anotaciones tienen como principal función la creación de endpoints, pero, con propositos diferentes.

<a id="controller"></a>
### @Controller

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

<a id="responsebody"></a>
### @ResponseBody

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

<a id="restcontroller"></a>
### @RestController

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

<a id="mapeo-de-solicitudes"></a>
## Mapeo de solicitudes

La anotación @RequestMapping puede ser usada a nivel de clase para expresar asignaciones compartidas o también a nivel de métododo para el mapeo de solicitudes entrantes hacía métodos definidos dentro del controlador dependiendo de su coincidencia por URL, método HTTP, etc.

<a id="requestmapping-a-nivel-de-metodo"></a>
### @RequestMapping a nivel de método

```
@Controller
public class AppController {
	 @RequestMapping(value = "/java/hello-world", method = RequestMethod.GET)
	 public String getMessage() {
			// ...
	 }
}
```

<a id="variantes-de-requestmapping"></a>
### Variantes de @RequestMapping

Existe anotaciones que son variantes de la anotación @RequesMapping para mapear solicitudes entrantes de acuerdo a su método HTTP. Esto ayuda a tener una mejor legibilidad del código y agilizar la configuración del mapeo a nivel de método, pues, muchas veces solo se tratará de manejarlo por su método HTTP y su concidencia por URL.

> [!NOTE]
> La configuración del mapeo de solicitudes puede recibir otros atributos como los parámetros de la solicitud, los encabezados y los media types.

Anotaciones para el mapeo de solicitudes dependiendo su método HTTP: 
- **@GetMapping**
- **@PostMapping**
- **@PutMapping**
- **@DeleteMapping**
- **@PatchMapping**

<a id="requestmapping-a-nivel-de-clase"></a>
### @RequestMapping a nivel de clase

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

<a id="captura-de-variables-en-url"></a>
### Captura de variables en URL

<a id="pathvariable"></a>
#### @PathVariable

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

<a id="requestparam"></a>
#### @RequestParam

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

<a id="consumible-media-types"></a>
### Consumible Media Types

El atributo **consumes** en el mapeo de solicitudes es usado para delimitar los formatos de los datos que un endpoint o controlador puede aceptar. Esto se especifica mediante la cabecera Content-Type en la petición del cliente y permite a Spring determinar cómo debe interpretar los datos que recibe.

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

<a id="producible-media-types"></a>
### Producible Media Types 

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

<a id="validacion-de-las-solicitudes"></a>
## Validación de las solicitudes 

<a id="requestbody"></a>
### @RequestBody

Esta anotación permite leer y convertir toda la información del cuerpo de la solicitud entrante hacía un objeto de Java y pasarlo como parámetro al método que esta manejando dicha solicitud.

Una vez que ya se sabe cómo procesar la información que el usuario ha proporcionado por medio de alguna interación, surge la pregunta, ¿Cómo se puede validar que la información recibida esta alineada a los requerimientos de la aplicación?

<a id="validacion-de-beans"></a>
### La validación de beans

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

<a id="anotaciones-de-validacion-comunes"></a>
### Anotaciones de validación comunes

- **@NotNull**: Valida que el campo no sea nulo
- **@NotEmpty**: Valida que el campo no sea nulo ni vacío (para colecciones, arrays y strings)
- **@NotBlank**: Valida que el String proporcionado no puede sea nulo ni contener solo espacios en blanco
- **@Size(min=, max=)**: Permite establecer valores mínimos/máximos en la longitud de strings, arrays, colecciones
- **@Min/@Max**: Permite establecer valores mínimos/máximos para números
- **@Email**: Valida que el campo tenga formato de email
- **@Pattern**: Valida contra una expresión regular
- **@Past/@Future**: Valida que la fecha sea anterior/posterior a la actual
- **@Positive/@PositiveOrZero**: Valida números positivos
- **@Negative/@NegativeOrZero**: Valida números negativos


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

> [!NOTE]
> Cómo se especifico anteriormente, Spring Boot integra las validaciones por medio de la implementación de **Hibernate Validator**. Está implementación posee los mensajes de error en caso de no cumplir alguna validación.
>
> En el ejemplo anterior no se expecífica explicitamente un mensaje de error personalizado hacía alguna validación y por lo tanto se usarán los mensajes de error que vienen por defecto de **Hibernate Validator**.
> 
> Link del proyecto de **Hibernate Validator**: <a href="https://github.com/hibernate/hibernate-validator/tree/main/engine/src/main/resources/org/hibernate/validator">Ir</a>

<a id="mensajes-de-error-personalizados"></a>
### Mensajes de error personalizados

Se pueden generar mensajes de error personalizados para las validaciones en caso de no cumplirse. 

Existen dos alternativas: 
- Explicitamente
- Con archivos .properties/.yml

<a id="personalizacion-explicita"></a>
#### Personalización explícita

Todas las anotaciones declarativas de valicación tienen el atributo de **message**. Mediante esté atributo se le puede sumistrar directamente el texto que se quiere mostrar en caso de no cumplir la validación.

```
import jakarta.validation.constraints.Size;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Email;

public class RegisterCredentialRequest {

    @NotBlank(message = "No puede ser una cadena sin caractéres.") // Personalizando el mensaje explicitamente.
    @Size(min=8, max=30)
    private String password;

    @Email()
    @NotBlank()
    private String email;

}
```

<a id="personalizacion-con-archivos-de-propiedades"></a>
#### Personalización con archivos de propiedades

Para poder utilizar esté mecanismo primeramente se tienen que crear archivos con el nombre **ValidationMessages** y la extensión **.properties o .yml** en el directorio **resources/** de la aplicación.

```
# ValidationMessages.properties
password.notblank=No puede ser una cadena sin caractéres.
```

```
import jakarta.validation.constraints.Size;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Email;

public class RegisterCredentialRequest {

    @NotBlank(message = "{password.notblank}") // Busca la referencia.
    @Size(min=8, max=30)
    private String password;

    @Email()
    @NotBlank()
    private String email;

}
```

Lo cual genera una **extensión** sobre los archivos **ValidationMessages.properties** de la implementación. De modo que, sí no se cumple con la validación **@NotBlank**, entonces, consuma el archivo **ValidationMessages.properties** extendido y busque la referencia que se le ha pasado (**password.notblank**).

> [!NOTE]
> En caso de dar una referencia que no exista en el documento **ValidationMessages.properties**, entonces, se da cómo respuesta el mismo String proporcionado explicitamente.
>
> Por ejemplo, si no existiera la clave **password.notblank** dentro del archivo creado y además no se cumpla con la validación, entonces, el mensaje de error sería: **"{password.notblank}"**

<a id="valid"></a>
### @Valid

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

<a id="validated"></a>
### @Validated

Está anotación es propia de Spring framework la cual hace uso del validador **Hibernate Validator**. Esta anotación es una **extensión** a la validación por beans (Java Bean Validation) añandiendo características cómo la validación individual y la validación por grupos.

<a id="validaciones-individuales"></a>
#### Validaciones individuales

La anotación @Validated permite la validación de paramétros individuales. Es decir, permite la validación de @RequestParam o @PathVariable de las solicitudes entrantes.

> Para poder activar esté mecanismo es necesario utilizar la anotación **@Validated** ya sea a nivel de clase del controlador o a nivel de método. Si no es a nivel de clase, entonces, se obligaría a declarar la anotación **@Validated** en todos los campos que se quieran validar ya sea @RequestParam o @PathVariable de la solicitud.

**@Validated a nivel de clase**

```
@Validated
@RestController
@RequestMapping("/user")
public class UsuarioController {
    @GetMapping("{id}")
    public Map<String, String> getUser(
	@PathVariable @Min(1) Long id,
	@RequesParam  @NotBlank String name
   ) { 
        ...
    }
}
```

**@Validated a nivel de método**

```
@RestController
@RequestMapping("/user")
public class UsuarioController {
    @GetMapping("{id}")
    public Map<String, String> getUser(
	@PathVariable @Validated @Min(1) Long id,
	@RequesParam  @Validated @NotBlank String name
   ) { 
        ...
    }
}
```

<a id="validacion-por-grupo"></a>
#### Validación por grupo

Es una característica la cuál permite realizar **ciertas validaciones** a un mismo objeto bajo diferentes escenarios. Esto se realiza creando interfaces marcadoras (marker interfaces) que se utilizan para clasificar y agrupar restricciones de validación.

Como se puede observar en el siguiente código, se han creado dos interfaces: **OnRegister y OnSignIn**, las cuales se utilizan dentro de la clase **CredentialRequest**, que simula el cuerpo de la solicitud entrante. Dentro de la clase se implementa la validación mediante **Bean Validation**, con la particularidad de que se clasifica por grupos.

De esta forma, en el mapeo de la solicitud **POST /sign-in** definida dentro del controlador y mediante la anotación **@Validated(OnSignIn.class)**, se indica que deben aplicarse únicamente las validaciones correspondientes al grupo **OnSignIn** sobre el objeto **CredentialRequest**. Esto implica que solo se validará que los campos **email y password** no sean nulos, vacíos o contengan únicamente espacios en blanco.

```
public interface OnRegister{ }
public interface OnSignIn{ }

public class CredentialRequest{
	@NotBlank( groups = {OnRegister.class, OnSignIn.class} )
	@Email( groups = OnRegister.class )
	public String email;

	@NotBlank( groups = {OnRegister.class, OnSignIn.class} )
	@Size(min, 8, max=30, groups = OnRegister.class)
	public Strin password;
}

@RestController
public class CredentialController {
    
    @PostMapping("/register")
    public ResponseEntity<...> register(@Validated(OnRegister.class) @RequestBody CredentialRequest usuario) {
        return ResponseEntity.ok(...);
    }
    
    @PostMapping("/sign-in")
    public ResponseEntity<...> signIn(@Validated(OnSignIn.class) @RequestBody CredentialRequest usuario) {
        return ResponseEntity.ok(...);
    }
}
```

<a id="methodargumentnotvalidexception-constraintviolationexception"></a>
##Las clases MethodArgumentNotValidException y ConstrainViolationException

Hasta este punto, ya sabemos cómo extraer y validar los datos suministrados por el usuario hacía la aplicación, pero, ¿Qué resultado se generá en caso de no cumplirse alguna validación?

> En caso de no cumplirse alguna validación ya sea dentro del @RequestBody, @RequestParam o @PathVariable se producen excepciones de tipo **MethodArgumentNotValidException** y **ConstrainViolationException**

<a id="methodargumentnotvalidexception"></a>
#### Clase MethodArgumentNotValidException

Está es una excepción que forma parte del módulo de Spring Web MVC y es lanzada cuando falla la validación sobre un objeto que fue anotado con **@Valid** *(RequestBody)*. Es decir, es lanzada cuándo el usuario envía datos en el cuerpo de la petición los cuales no cumplen con los requisitos definidos en la aplicación.

> Método getBindingResult()
>
> Dentro de la clase MethodArgumentNotValidException existe el método **getBindingResult** el cuál devuelve un objeto de tipo **BindingResult** que a su vez contiene el método **getAllErrors()** que devuelve todos los errores que se generaron durante el proceso de binding.
> 
> ¿Y qué es el binding?
> El binding es el proceso donde se mapea los datos enviados en el cuerpo de una petición hacía un objeto de Java


<a id="constraintviolationexception"></a>
#### Clase ConstrainViolationException

Está es una excepción que forma parte del Bean Validation (JSR-380) de Jakarta EE. 

Dentro de una aplicación Spring, esta excepción se lanza cuando ocurre una **violación de restricción** (por ejemplo, fallas en el Bean Validation) sobre algún parámetro de un método definido en un controlador. Dicho parámetro debe estar anotado con **@Validated**, ya sea a nivel de clase o de método (por ejemplo, en parámetros como @RequestParam, @PathVariable o @RequestBody).


> Método getConstraintViolations()
>
> Dentro de la clase ConstrainViolationException existe el método **getConstraintViolations()** el cual devuelve un **Set<ConstraintViolation<?>>**, es decir, devuelve un conjuto de restricciones violadas. Donde cada elemento del conjunto posee distintos atributos, donde uno de estos atributos es el mensaje de error (por ejemplo, el mensaje de error personalizado o el mensaje de error por defecto).

<a id="manejo-de-excepciones"></a>
## Manejo de excepciones

En este punto, ya sabemos que, en caso de no cumplirse dichas validaciones, se producen excepciones. Entonces, ¿cómo podemos manejar esas excepciones dentro de una aplicación Spring?

<a id="handler-exception-resolver"></a>
### La interfaz HandlerExceptionResolver

Dentro del framework de Spring se tiene la interfaz HandlerExceptionResolver, la cual funciona como un punto intermedio entre las excepciones lanzadas por la aplicación y la respuesta HTTP que será devuelta al cliente.

El flujo del control de excepciones dentro de una aplicación de Spring inicia en el momento de su lanzamiento, en ese instante el **DispatcherServlet** interrumpe el flujo de la aplicación para delegarle la responsabilidad del manejo de la excepción a las implementaciones de la interfaz **HandlerExceptionResolver** hasta que alguna de ellas resuelva la excepción.

| Implementación                           	|  Descripción                                          	|
|------------------------------------------	|------------------------------------------------------		|
| `ResponseStatusExceptionResolver`         	| Maneja excepciones anotadas con @ResponseStatus o que implementan ResponseStatusException.		|                         
| `DefaultHandlerExceptionResolver`             | Maneja excepciones estándar de Spring MVC y las traduce a códigos de estado HTTP apropiados.          |
| `ExceptionHandlerExceptionResolver` 		| Utiliza los métodos registrados con la anotación @ExceptionHandler para el manejo de la excepción. 	|

> [!NOTE]
> La ejecución de cada una de las implementaciones de la interfaz **HandlerExceptionResolver** se hace de forma secuencial y por ende tiene un orden de ejecución:
>  1.  **ExceptionHandlerExceptionResolver**
>  2.  **ResponseStatusExceptionResolver**
>  3.  **DefaultHandlerExceptionResolver**

<a id="exceptionhandler"></a>
### @ExceptionHandler

Esta anotación permite manejar excepciones **específicas** que ocurren durante la ejecución de los métodos definidos dentro de un controlador.

El funcionamiento interno inicia cuando Spring registra los métodos anotados con **@ExceptionHandler** durante el inicio de la aplicación. De modo que, en el instante que se lance una excepción, entonces, se delega el manejo de la excepción al **ExceptionHandlerExceptionResolver** que a su vez busca un **Exception Handler** qué este registrado y sea apropiado para el manejo de la excepción lanzada.

```
    # Manejo de excepciones lanzadas por parametros anotados con @Valid.

    @ExceptionHandler(MethodArgumentNotValidException.class)
    private ResponseEntity<Map<String, String>> handleValidationErrors(
            MethodArgumentNotValidException ex
    ) {

        String error = ex.getBindingResult()
                .getFieldErrors()
                .stream().map(FieldError::getDefaultMessage)
                .findFirst().orElse(ex.getMessage());

        return new ResponseEntity<>(Map.of("error", error), HttpStatus.BAD_REQUEST);
    }
```

<a id="controlleradvice"></a>
### @ControllerAdvice

Al definir métodos anotados con @ExceptionHandler dentro de una clase marcada con @ControllerAdvice, permite la gestión de excepciones de forma global. Debido que, @ControllerAdvice también es un @Component y por lo tanto, Spring detecta automáticamente esta clase y aplica su lógica de manejo de excepciones a todos los controladores registrados.

> Existe dos escenarios:
> - Si la excepción ocurre antes de que el método del controlador se ejecute completamente (por ejemplo, en la validación de parámetros, autorización, etc.), Spring redirige automáticamente el flujo hacia el @ExceptionHandler correspondiente en el @ControllerAdvice.
>
> - Si el método se empieza a ejecutar y lanza una excepción, entonces en ese momento se interrumpe, se aborta la ejecución restante del método, y se llama al @ExceptionHandler correspondiente en el @ControllerAdvice.


```
@ControllerAdvice
public class GlobalExceptionHandler {

   # Manejo de excepciones lanzadas por parametros anotados con @Validated.
   @ExceptionHandler(ConstraintViolationException.class)
   public ResponseEntity<Object> handleConstraintViolation(ConstraintViolationException ex) {
     Map<String, String> errors = new HashMap<>();
    
     for (ConstraintViolation<?> violation : ex.getConstraintViolations()) {
        String propertyPath = violation.getPropertyPath().toString();
        String message = violation.getMessage();
        errors.put(propertyPath, message);
     }
    
     return new ResponseEntity<>(Map.of("errors", errors), HttpStatus.BAD_REQUEST);
   }
}
```

<a id="restcontrolleradvice"></a>
### @RestControllerAdvice

> [!NOTE]
> La anotación **@RestControllerAdvice** es una combinación de **@ControllerAdvice** y **@ResponseBody**
> 
> Por lo tanto, se tiene los beneficios de manejar excepciones de manera global y la serialización de respuestas de la petición de manera automática.


