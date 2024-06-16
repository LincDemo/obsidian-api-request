# 👨🏻‍💻 Bloque de código

El `bloque de código` es un bloque versátil que se puede usar para escribir código en diferentes lenguajes. En este caso, lo usaremos para realizar solicitudes.

## 🏳️ Banderas

Las banderas son la forma de especificar los parámetros de nuestra solicitud y también el formato en el que queremos nuestra respuesta.

| Bandera | Valor predeterminado |
| ------------| ---------|
| [url](#url) |          |
| [method](#method) | GET   |
| [body](#body) |         |
| [headers](#headers) |   |
| [show](#show) | ALL     |
| [format](#format) | `{}` |
| [req-id](#req-id) | req-general |
| [disabled](#disabled) | |
| [req-repeat](#req-repeat) | 1t@1s |
| [notify-if](#notify-if) | |
| [save-to](#save-to) |   |
| [properties](#properties) | |

### url

Es la única bandera **obligatoria**. Especifica la URL de la solicitud. Se pueden utilizar variables definidas en el `frontmatter`.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/users/{{this.id}}
```
~~~

!!! note "Donde `{{this.id}}` es una variable (`id`) definida en el frontmatter."


### method

Especifica el método de solicitud. El valor predeterminado es `GET` y los valores disponibles son:

- GET
- POST
- PUT
- DELETE

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/posts
method: post
```
~~~

### body

Especifica el cuerpo de la solicitud. El valor predeterminado es un objeto vacío. Los datos deben estar en formato JSON con comillas dobles separando las claves y valores con dos puntos y espacio. Se pueden utilizar variables definidas en el `frontmatter`.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/posts
method: post
body: {"title": {{this.title}}, "body": "bar", "userId": 1}
```
~~~

!!! note "Donde `{{this.title}}` es una variable (`title`) definida en el frontmatter."

### headers

Especifica los encabezados de la solicitud. El valor predeterminado es un objeto vacío. Los datos deben estar en formato JSON con comillas dobles separando las claves y valores con dos puntos y espacio. Se pueden utilizar variables definidas en el `frontmatter`.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/posts
method: post
headers: {"Content-type": "application/json; charset=UTF-8"}
```
~~~

### show

Especifica los datos de respuesta que se van a mostrar. Para acceder a objetos anidados, se utiliza una flecha derecha `->`. El valor predeterminado es `ALL`.

~~~markdown
```req
url: https://api.chess.com/pub/player/hikaru/stats
show: chess_daily -> last -> rating
```
~~~

Se pueden mostrar múltiples salidas separándolas con coma.

~~~markdown
```req
url: https://api.chess.com/pub/player/hikaru/stats
show: chess_daily -> last -> rating, chess_daily -> best -> rating
format: <p>Último juego: {}</p> <strong>Mejor juego: {}</strong>
```
~~~

También es posible iterar sobre un arreglo usando `{..}`. El siguiente ejemplo muestra la ciudad de todos los usuarios.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/users
show: {..} -> address -> city
```
~~~

También es posible iterar sobre un número especificado de elementos del arreglo usando `{n..n}`.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/users
show: {0..2} -> address -> city
```
~~~

También es posible iterar sobre un rango especificado de índices del arreglo usando `{n-n-n}`.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/users
show: {0-2-1} -> address -> city
```
~~~

### format

Especifica el formato en el que se debe mostrar la respuesta. El valor predeterminado es `{}`. Puede ser cualquier cadena (incluyendo `markdown` y `html`). Si se especifican más de una salida, se deben especificar más formatos, de lo contrario, se aplicará el mismo formato para todas las salidas.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/posts/1
show: title, body
format: <h1>{}</h1> <p>{}</p>
```
~~~

!!! note "En este ejemplo, primero `{}` será reemplazado por el título, y segundo `{}` será reemplazado por el cuerpo."

### req-id

Especifica el ID con la que se almacenará la solicitud. El valor predeterminado es `req-general`. Esto es útil cuando queremos almacenar la respuesta en `localStorage` y usarla en otros bloques o notas.


~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/users/1
show: name
req-id: name
```
~~~

Las respuestas almacenadas se pueden ver usando el `req-id` con la bandera `disabled` (que no activará una nueva solicitud).

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/users/1
req-id: name
disabled
```
~~~

Las respuestas también se pueden ver usando [dataview](https://blacksmithgu.github.io/dataview/).

~~~markdown
```dataview
dv.paragraph(localStorage.getItem("req-name"))
```
~~~

!!! info "Es obligatorio usar `req-` antes de lo que sea que hayas definido en la bandera `req-id`."

Para eliminar respuestas de localStorage, ejecuta:

~~~markdown
```dataview
localStorage.removeItem("req-name")
```
~~~

Para eliminar todas las respuestas, ve a configuraciones y haz clic en el botón `Clear`.

### disabled

Deshabilita la solicitud. Si se especifica un `req-id`, APIR buscará la respuesta en `localStorage`. Si no se encuentra, realizará una nueva solicitud y la almacenará. Después de eso se usará la respuesta recién almacenada.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/users/1
show: name
req-id: name
disabled
```
~~~

### req-repeat

!!! warning "Esto solo funciona con respuestas de tipo JSON"

Especifica la cantidad de veces que se debe repetir la solicitud y el intervalo entre cada repetición. El valor predeterminado es `1@1` (leído como `X veces cada X segundo(s)`).


~~~markdown
```req 
url: api.coincap.io/v2/rates/bitcoin
req-repeat: 5@5
```
~~~

### notify-if

!!! warning "Esto solo funciona con respuestas de tipo JSON"

Especifica la condición para activar una notificación. Puede usarse para monitorear un valor específico. La sintaxis de ruta utilizada para acceder a objetos anidados varía respecto a la bandera `show`, aquí se usan puntos en lugar de flechas y no se permiten espacios en la ruta.

~~~markdown
```req 
url: api.coincap.io/v2/rates/bitcoin
req-repeat: 5@5
notify-if: data.rateUsd < 69889
```
~~~

!!! note "En el ejemplo anterior, se activará una notificación cada vez que el valor de `data.rateUsd` sea menor que `69889`."

### save-to

Especifica la ruta para guardar la respuesta. Guardará toda la respuesta. Se requiere una extensión de archivo. No creará directorios.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/posts/1
save-to: posts/1.json
```
~~~

### properties

!!! warning "Para usar esta bandera necesitas una respuesta de tipo JSON y la bandera `show`"

Especifica las propiedades del frontmatter que se actualizarán con la respuesta. Los datos deben ser cadenas separadas por comas.

~~~markdown
```req 
url: https://jsonplaceholder.typicode.com/posts/1
show: id, title
properties: id, title
```
~~~
