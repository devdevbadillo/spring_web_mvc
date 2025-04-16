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

Dentro de Spring Web MVC se cuenta con diferentes anotaciones que permiten el mapeo de solicitudes entrantes debido a su coincidencia por URL, método HTTP, etc. Hacía métodos definidos dentro de los controladores.

Anotaciones para el mapeo de solicitudes: 
- @GetMapping
- @PostMapping
- @PutMapping
- @DeleteMapping
- @PatchMapping

```
@RestController
@RequestMapping("/java")
class AppController {

	@GetMapping("/hello-world")
	public Person getMessage() {
		// ...
	}
}
```
