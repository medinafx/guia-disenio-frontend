# Guía diseño Frontend de servicios web RESTful.

<br>

- [2 Consideraciones](#2-consideraciones)  
      * [2.1 Responsabilidades del Servicio](#21-responsabilidades-del-servicio)  
      * [2.2 Restricciones de la arquitectura REST](#22-restricciones-de-la-arquitectura-rest)  
- [3 Diseño de URIs RESTful](#3)  
      * [3.1 Estructura básica (plantilla)](#user-content--31)  
      * [3.2 Plantilla para recursos relacionados](#32-plantilla-para-recursos-relacionados)  
      * [3.3 Convenciones en el diseño de URIs](#33-convenciones-en-el-diseño-de-uris)  
      * [3.4 Ejemplo de URIs válidas](#user-content--34)  
      * [3.5 URIs estables](#35-uris-estables)  
- [4 Representaciones](#4-representaciones)  
      * [4.1 Header Content-Type](#41-header-content-type)  
      * [4.2 Negociación de contenido](#user-content--42)  
- [5 Parámetros](#user-content--5)  
      * [5.1 Creación y actualización de recursos](#user-content--51)  
      * [5.2 Recuperación de recursos](#user-content--52)  
      * [5.2.1 Filtros](#521-filtros)  
      * [5.2.2 Ordenamiento](#522-ordenamiento)  
      * [5.2.3 Localizadores](#523-localizadores)  
      * [5.2.4 Selección de atributos en la instancia de un recurso](#user-content--524)  
      * [5.2.5 Selección de atributos en una colección de recursos](#user-content--525)  
      * [5.2.6 Metadata](#526-metadata)  
- [6 Uso de métodos HTTP](#user-content--6)  
      * [6.1 Idempotencia y métodos seguros](#user-content--61)  
- [7 Seguridad](#7-seguridad)  
      * [7.1 Mecanismos de seguridad](#71-mecanismos-de-seguridad)
      * [7.1.1 HTTP Basic](#711-http-basic)
      * [7.1.2 TSL](#712-tsl)
      * [7.1.3 HTTPS](#713-https)
      * [7.2 HTTP status](#72-http-status)  
- [8 Rendimiento](#8-rendimiento)  
      * [8.1 Mecanismos de almacenamiento en caché HTTP](#81-mecanismos-de-almacenamiento-en-cache-HTTP)
      * [8.1.1 ETag](#811-etag)
      * [8.1.2 HEADERS Last-Modified/If-Modified-Since](#812-headers-last-modified/if-modified-since)  

## 2 Consideraciones.
----------------------
El presente documento provee una serie de directrices y ejemplos para el diseño frontend de servicios web RESTful, los cuales están basados en las restricciones de estilo arquitectónico REST, el protocolo HTTP - protocolo usado por REST, y las mejores prácticas y convenciones recopiladas en su mayor parte de las siguientes fuentes:

<br>

- RESTful API Design - Matthias Biehl.  
* [White House Web API Standards](https://github.com/WhiteHouse/api-standards) - The White House, Washington, D.C.  
* [Learn REST: A RESTful Tutorial](http://www.restapitutorial.com/) - Todd Fredrich.

<br>

Es oportuno señalar que no existe un estándar para la interpretación y aplicación de las restricciones, y claro está, de las &quot; buenas prácticas&quot; en el diseño y construcción de servicios RESTful. Depende de quien diseña y construye el API implementar las características que de acuerdo a su experiencia son las más indicadas para el objetivo que desea alcanzar.  

### 2.1 Responsabilidades del Servicio.

<br>

Las APIs generalmente no implementan la lógica del negocio. La lógica del negocio y
almacenamiento de los datos son llevadas a cabo por otros sistemas en el backend. Dicho lo
anterior, existen cuatro responsabilidades principales que deben ser satisfechas por nuestro servicio:

<br>

- **Reunir los datos:** Las APIs deben reunir los datos de varias fuentes; bases de datos,
legacy systems, otros servicios. Cada API deberá decidir qué backend debe usar para reunir
la información solicitada, de acuerdo al contenido o contexto de la petición.  

<br>

- **Estructurar y dar formato a los datos:** Cuando los datos son expuestos, éstos deben
tener una estructura y formato simple  que sea fácil de consumir por el cliente. Es deber
del API abstraer la complejidad de los sistemas en el backend y proveer una interfaz
apropiada para el uso de los clientes.  

<br>

- **Entregar los datos:** En general, las APIs deberán hacer accesibles los datos de manera
segura al tiempo que proveen el mejor rendimiento posible.  

<br>

- **Proveer seguridad:** Las APIs exponen los sistemas en el backend hacia el exterior,
incrementando el riesgo de que la información sea robada o que dichos sistemas sean
comprometidos. Por lo anterior, las APIs deben garantizar que quienes consumen la
información estén debidamente autenticados y autorizados para consumir la información que
expone la API. Por otra parte, el API debe garantizar la disponibilidad de ella misma y
de los sistemas en el backend con los que los clientes interactúan a través del API.  

### 2.2 Restricciones de la arquitectura REST.

<br>

La arquitectura REST define seis restricciones  que comprenden la base de la construcción de servicios RESTful. A continuación las enumeramos con una breve descripción.

<br>

-  Interfaz uniforme  
      * Basado en recursos.  
      * Manipulación de recursos a través de sus representaciones.  
      * Mensajes descriptivos.  
      * Hypermedia as the engine of the application state (HATEOAS)  
- Sin estado.  
- Cacheable.  
- Cliente-servidor.  
- Sistema por capas.  
- Código por demanda.  

<h2 href="3">3 Diseño d  e URIs RESTful.</h2>

<h3 href="31">3.1 Estructura básica (plantilla)</h3>
---------------------------------------

```
 /{version}/{namespace}/{recurso}/{recurso-id}
```
<br>

Una URI típica está compuesta de las siguientes partes:  

<br>

- La versión del API. Generalmente solo los números de versiones mayores serán usados como path parameter, con el prefijo **v**. Ejemplo: **/v1**.
-  Un namespace usado como un medio para agrupar los recursos de un modo más lógico y jerárquico.
- Un sustantivo plural que corresponde al nombre del recurso que se accede en el namespace; generalmente una colección de recursos.
- El id del recurso seleccionado.

### 3.2 Plantilla para recursos relacionados.

<br>

```
 .../{sub-recurso}/{sub-recurso-id}
```
<br>

- Multiples niveles de **sub-recursos** y sus respectivos id.

<br>

<h2 href="33"> 3.3 Convenciones en el diseño de URIs.</h2>

<br>

-  La URI está compuesta exclusivamente por caracteres en minúsculas. No usar lowerCamelCase, UpperCamelCase o cualquier otro uso de mayúsculas.
-  Las URI no tienen pleca (&quot;/&quot;) como último carácter.
-  Usar guión medio (&quot;-&quot;) para separar palabras y así mejorar su lectura. No usar guión bajo (&quot;\_&quot;)
-  Las URI no tienen la extensión de un archivo.  

<h3 href="34"> 3.4 Ejemplo de URIs válidas.</h3>

<br>

**Colección de libros**

```
http://domain.com/api/v1/libros
```

**Un único libro**

```
http://domain.com/api/v1/libros/123
```

**Todos los capítulos de un único libro**

```
http://domain.com/api/v1/libros/123/capitulos
```

**Un único capítulo de un libro.**

```
http://domain.com/api/v1/libros/123/capitulos/1
```

### 3.5 URIs estables.  

<br>

La URI de una API publicada no debe ser cambiada ya que rompería la funcionalidad de quien
la consume. Si dicha URI debe ser cambiada por una razón legítima, la respuesta deberá
retornar el status code 301 - Moved Permanently, y el nuevo link al recurso en el HEADER
location.  

## 4 Representaciones.  

<br>

Una representación es una serialización de un recurso - los recursos son datos crudos -. Cuando transferimos un recurso entre el API y el cliente éstos viajan como texto, por lo que necesitan ser serializados en el destino. Para serializar un recurso en una representación y deserializarlo de regreso a un recurso, usaremos las reglas definidas en los MIME-TYPES de HTTP que podremos encontrar en la especificación IETF RFC 2045.  

### 4.1 Header Content-Type.

<br>

El header Content-Type se usará para indicar el media-type del HTTP body enviado o recibido. Por ejemplo:  

```
Content-Type: text/html
```
<br>

<h3 href="42"> 4.2 Negociación de contenido.</h3>

<br>

La negociación de contenido es un mecanismo implementado en la API, en el que se selecciona la representación del recurso solicitado en base a las preferencias del cliente y las posibilidades del API.   

<br>

El cliente podrá enviar una lista de los formatos deseados a través del HEADER Accept. Por ejemplo:  

```
  Accept: application/json, application/xml
```
<br>

Por su parte, el header Content-Type será usado por el API para indicar la regla de serialización a usar en la representación, la cual podremos encontrar en el HTTP body. Para una representación JSON, el header Content-Type tendrá el valor:  

```
  Content-Type: application/json
```
<br>

Si el mecanismo de negociación de contenido no puede encontrar una representación que pueda ser tanto aceptado por el cliente como retornado por el API, se deberá retornar el status code **406 Not Acceptable**.  


<h2 href="5"> 5 Parámetros.</h2>
---------------------------------------

Las API raramente retornan información estática, por el contrario, retornan información dinámica que varía de acuerdo a la información suministrada por la aplicación que consume la API, el usuario final o el cliente (Ej. navegador web)  

Dicho lo anterior, el API podrá recibir información a través de los siguientes cuatro tipos de parámetros:

<br>

- Path parameters.  
- Query parameters.  
- Form parameters.  
- Header parameters.

<br>

A continuación abordamos los escenarios en los que se usará cada tipo de parámetro:  


<h3 href="51"> 5.1 Creación y actualización de recursos:</h3>

<br>

Cuando los valores de un recurso a crear o actualizar son recolectados de un HTML form, existe la posibilidad de enviar dichos valores al API como **form-parameters.**  

<br>

Ejemplo de creación de un nuevo recurso con form-parameters.  

```
Host: ucem.edu.ni
Accept: application/json,
Content-Type: application/x-www-form-urlencoded

titulo=APIs+RESTful&amp;anio=2017
```
<br>

Los form-parameters consisten en pares de clave valor, y  son encontrados en el cuerpo del mensaje de una petición HTTP POST o PUT. El **Content-Type** del mensaje es el media-type **application/x-www-urlencoded**.  

<br>

Dicho lo anterior, el mecanismo por defecto para enviar los datos al servidor será un JSON serializado, donde el **Content-Type** del mensaje es application/json.  

<br>

Ejemplo de creación de un nuevo recurso:  

```
POST /libro HTTP/1.1
Host: ucem.edu.ni
Accept: application/json
Content-Type: application/json

{
  "titulo": "APIs RESTful",
  "anio": "2017"
}
```

<h3 href="52"> 5.2 Recuperación de recursos.</h3>

<br>

Quien consume el API podrá usar el método HTTP GET y una URI para recuperar una colección de recursos.  

<br>

Ahora bien, para colecciones que no pueden ser retornadas en una única respuesta, o bien para clientes que necesitan únicamente un subconjunto de dicha colección, el API implementará la  siguiente funcionalidad:  


#### 5.2.1 Filtros.

<br>

Quien consume el API podrá filtrar la colección a retornar haciendo uso de un query parameter por cada atributo en el que desee buscar un término.

<br>

Ejemplo: Recuperamos una colección de libros donde el autor es George Martin y el año de publicación es 2004.  

```
http://domain.com/api/v1/libros?autor=George%20Martin&amp;anio=2004
```

Por otra parte, si lo que se desea es recuperar los recursos que tengan el valor **&quot;George Martin&quot;** en cualquiera de sus atributos, usaremos el siguiente URI:

```
http://domain.com/api/v1/libros?search=George%20Martin
```
#### 5.2.2 Ordenamiento.  

<br>

Para ordenar una colección de recursos usaremos los query parameter **sort** y **sortOrder**.  

<br>

Ejemplo:  Ordenamos una colección de recursos libros por el atributo autor en orden descendente.

```
http://domain.com/api/v1/libros?sort=titulo&amp;sortOrder=desc
```

#### 5.2.3 Localizadores.

<br>

Los localizadores se expresan en la URI en forma de **path-parameters** y se usan exclusivamente para localizar un único recurso, como es el caso del **identificador del recurso** (recurso-id)  

<br>

Dicho lo anterior, será posible usar múltiples path-parameters en una URI, pero siempre para expresar una organización jerárquica o lógica, nunca para representar una colección de recursos.  

<br>

Ejemplo de una URI válida.

```
http://domain.com/api/v1/libros/en/245
```

<br>

En el anterior URI existen dos path-parameters &quot;en&quot; y &quot;245&quot;; el primero corresponde a un identificador de los libros en idioma inglés, y el segundo parámetro corresponde el id único de un recurso.

<br>

Ejemplo de una URI inválida.

```
http://domain.com/api/v1/autores/george
```

<br>

La anterior URI retorna una colección de libros cuyo autor coincide con el valor del path parameter george. Ya que pueden existir muchos autores con el mismo nombre el path-parameter es inválido, y en consecuencia el URI también.

<br>

En el anterior escenario el API deberá retornar el status code **404 Not Found**.

<h4 href="524"> 5.2.4  Selección de atributos en la instancia de un recurso.</h4>

<br>

El API permitirá seleccionar los atributos a retornar de la  instancia de un recurso a través de query parameter fields.

<br>

Ejemplo: Recuperamos únicamente el título del libro con ID 123.

```
http://domain.com/api/v1/libros/123?fields=titulo
```

<br>

Para seleccionar más de un atributo, usaremos el query parameters fields mas lista de atributos a recuperar separados por coma:

<br>

```
http://domain.com/api/v1/libros/123?fields=autor,anio
```

<h4 href="525"> 5.2.5  Selección de atributos en una colección de recursos.</h4>

<br>

5.8 El API permitirá seleccionar los atributos a retornar de una lista de recursos a través de query parameter
fields.

<br>

Ejemplo: Recuperamos el título de todos los libros en una colección de recursos.

```
http://domain.com/api/v1/libros?fields=titulo
```
<br>

Para seleccionar más de un atributo, usaremos el query parameters fields mas lista de atributos a recuperar separados por coma:

<br>

```
http://domain.com/api/v1/libros?fields=autor,anio
```

#### 5.2.6 Metadata.

<br>

La metadata nos brinda información de cómo manejar los mensajes de petición y respuesta, y se expresa normalmente como **header-parameters.**  

<br>

Por ejemplo, los HEADERS que tenemos a nuestra disposición para describir el contenido de un mensaje son: Content-Type, Content-Language, Content-Encoding.  

<h2 href="6"> 6 Uso de métodos HTTP.</h2>

Los recursos serán únicamente manipulados usando peticiones HTTP, donde los métodos más usados en las operaciones CRUD y sus valores de retorno detallamos a continuación:

<br>

| **HTTP Verb** | **CRUD** | **Colección (ej. /libros)** | **Instancia de una colección (ej. /libros/{recurso-id})** |
| ------------- | -------- | ----------------------------| -------------------------------------------- |
| POST          | Crear    | 201 (Created), &#39;Location&#39; header con un link hacia el recurso creado /libros/{id}. | 404 (Not Found), 409 (Conflict) si el recurso ya existe. |
| GET | Leer/Recuperar | 200 (OK), colección de libros. Implementar paginación, ordenamiento, filtros por atributo y selección de atributos. | 200 (OK), un único libro. 404 (Not Found), si el ID no existe o es inválido. |
| PUT | Actualizar | 404 (Not Found) No se permite actualizar la colección completa (opcional) | 200 (OK) or 204 (No Content) 404 (Not Found) si el ID no existe o es inválido. |
| DELETE | Delete | 404 (Not Found) No se permite eliminar una colección completa. | 200 (OK) 404 (Not Found), si el ID no existe o es inválido.  |

<h3 href="61"> 6.1 Idempotencia y métodos seguros</h3>

<br>

| Método | Seguro | Idempotentes |
| ------ | ------ | ------------ |
| GET    | SI     | SI           |
| POST   | NO     | NO           |
| PUT    | NO     | SI           |
| DELETE | NO     | SI           |

<br>

- **Métodos seguros:** Los métodos seguros son los que nuncan modifican la representación del recurso; Es posible que cambien algo del mismo o algo en el servidor, pero nunca reflejan una representación diferente.  Los métodos de lectura son los únicos seguros.

<br>

- **Métodos idempotentes:** Son aquellos que pueden ser llamados muchas veces sin producir un resultado diferente.

<br>

Si bien los métodos HTTP se clasifican en seguros e idempotentes, ésto por si mismo no garantiza que se comporten como deberían. Es deber de los servicios programar dicho comportamiento en concordancia con la especificación del protocolo HTTP.  

<br>

Dicho lo anterior, el estado de un recurso solo puede ser modificado mediante los verbos PUT, POST, DELETE. Bajo ninguna circunstancia se debe usar el método GET u otro método HTTP de lectura para realizar cambios de estado.  

<br>

Por ejemplo:

<br>

- GET /usuarios/1?activar
- GET /usuarios/activar

## 7 Seguridad.
----------------------

Las APIs exponen los sistemas en el backend hacia el exterior, incrementando el riesgo de que la información sea robada o que dichos sistemas sean comprometidos. Por lo anterior, las APIs deben garantizar que quienes consumen la información estén debidamente autenticados y autorizados.

<br>

A continuación abordamos dos de los aspectos de la seguridad antes mencionados:

<br>

**Autenticación**  

<br>

La autenticación determina si un usuario es quien dice ser. Usualmente quien consume el API, el usuario final, o ambos, deberán estar autenticados por el proveedor del API para poder usar el servicio. Lo anterior puede suponer verificar un usuario y contraseña, o verificar si un token es válido y vigente.

<br>

Es importante señalar que la autenticación no significa que el cliente puede acceder a un recurso en particular.

<br>

**Autorización**

<br>

La autorización comprende verificar si un usuario está autorizado para acceder o modificar un recurso, generalmente a través de la definición de roles y la asociación de éstos con dichos usuarios.

Por ejemplo, una regla de autorización puede indicar que un usuario con el rol de cliente solo tiene permitido hacer uso de los EndPoint - recurso más método HTTP - de lectura (GET), mientras que los usuarios con el rol de administrador pueden hacer uso de todos los EndPoint.

<br>

### 7.1 Mecanismos de seguridad.

#### 7.1.1 HTTP Basic.

<br>

HTTP basic es un mecanismo simple para la autenticación y autorización de las peticiones hechas al API. Dicho mecanismo se encuentra estandarizado en la especificación **IETF RFC 7235**, por lo que es soportado por la mayoría de librerías HTTP y servidores.  

<br>

En el mecanismo HTTP Basic, quien consume la API hace peticiones HTTP incluyendo el nombre de usuario y contraseña en el HEADER **HTTP Authorization**, por otra parte, HTTP Basic también puede ser usado con un mecanismo de desafío-respuesta:  

<br>

1. El cliente realiza una petición HTTP solicitando un recurso protegido.  
2. El servidor responde con el status code **401 Unauthorized**, junto con el **HEADER WWW-Authenticate: Basic**.  
3. El cliente agrega las credenciales en el **HEADER Authorization** y envía la petición nuevamente.  

<br>

Dicho lo anterior, el cliente por lo general enviará las credenciales de forma directa - paso 3 -, requiriendo para ello que el mensaje de petición tenga el siguiente encabezado.  

<br>

```
Authorization: Basic {username}:{password}
```
<br>

En el anterior encabezado, la combinación de usuario y contraseña deberá estar codificada con el esquema de codificación base64.

<br>

Es importante señalar que la codificación Base64 puede ser fácilmente revertida, y debido a que en el protocolo HTTP los mensajes de petición y respuesta viajan como texto plano en la red, es más que recomendable que HTTP basic se use junto con TSL.  

#### 7.1.2 TSL.

<br>

Transport Layer Security (en español: seguridad de la capa de transporte) y su predecessor, Secure Sockets Layer (SSL), ambos referidos frecuentemente como “SSL", son protocolos de encriptación para enviar mensajes de forma segura en una red de computadoras.  

<br>

HTTP Basic depende de TSL, y por lo tanto la seguridad de la mayoría de peticiones a la API. TSL garantiza la confidencialidad e integridad de la información en el mensaje de petición y respuesta.

<br>

#### 7.1.3 HTTPS.

<br>

Secure HTTP (HTTPS) - también llamado HTTP sobre TSL/SSL- es una combinación del protocolo HTTP con el protocolo TLS/SSL que es usado para establecer comunicaciones cifradas.

HTTPS usa el esquema https en la URL en vez del esquema http. Por otra parte, el puerto por defecto para HTTPS es 443 en vez de 80.

<br>

### 7.2 HTTP status.

<br>

| Código | Descripción            |
| ------ | ---------------------- |
| 401  Unauthorized | El cliente intentó realizar una acción sin estar debidamente autenticado. |
| 403  Forbidden    | De acuerdo a las políticas/reglas de control de acceso, el usuario no tiene la autorización de realizar la acción solicitada |
| 405 Method not Allowed | El método no es soportado - no ha sido implementado -. Si el usuario no está autenticado retornar 401.

<br>

## 8 Rendimiento  
----------------------  

Cuando abordamos la optimización de rendimiento en aplicaciones WEB RESTful, el
almacenamiento en caché  es una de las primeras técnicas que se usan para dicho
propósito, las cuales pueden ser implementadas en el cliente (ej. Navegador web),
el API y/o el backend.  

<br>  

Dicho lo anterior, en esta sección nos enfocaremos en el almacenamiento en caché
del protocolo HTTP 1.1, ya que este mecanismo se encuentra estandarizado e
implementado por la gran mayoría de la infraestructura basada en HTTP, como es
el caso de servidores HTTP y proxies.

### 8.1 Mecanismos de almacenamiento en caché HTTP
#### 8.1.1 ETag  

El almacenamiento de caché al lado del cliente es implementado en HTTP mediante
el uso del HEADER **Etag:{etag-value}**. El API siempre incluirá un ETag en la
respuesta para enviarla junto con el recurso solicitado. ETags (Entity Tags) son
valores hash en la respuesta HTTP que son calculados por el API. Los valores hash
son específicos de una URL, y cada vez que el contenido de la respuesta cambia,
también lo hacen dichos valores.  

<br>

A continuación describimos el flujo del almacenamiento en caché por ETag:  

<br>

1 El cliente realiza una petición HTTP solicitando un recurso.  


```
GET /libros HTTP/1.1
Host: localhost:8080
Accept: application/json
```

2 El API calcula un hash a partir del contenido del mensaje de respuesta, y lo
adjunto a dicha respuesta en el HEADER Etag.  


```
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
ETag: "062c5d97b48d9df07fab132bc76c5da02"
Accept: application/json
Content-Length: 93
Date: Accept: 2017-02-17T23:28:56.782Z

{
  "titulo": "APIs RESTful",
  "anio": "2017"
}
```  

3 El cliente almacena en caché la URL del API, el ETag y la respuesta. En la
siguiente petición al API, el cliente incluirá el HADER **If-None-Match**:{etag-value},
con el valor del ETag almacenado en caché.  

```
GET /libros HTTP/1.1
Host: localhost:8080
Accept: application/json
If-None-Match: "062c5d97b48d9df07fab132bc76c5da0222"  
```

4  El API verifica el valor del ETag, y si éste no ha cambiado, el servidor
retorna el status code **304 Not Modified**, indicando al cliente que use la
respuesta almacenada en caché.  

```
HTTP/1.1 304 Not Modified
Server: Apache-Coyote/1.1
ETag: "062c5d97b48d9df07fab132bc76c5da02"
Date: Accept: 2017-02-17T23:28:56.782Z
```  
<br>

#### 8.1.2 HEADERS Last-Modified/If-Modified-Since

<br>

Los encabezados **Last-Modified** y **If-Modified-Since** ofrecen un mecanismo similar
a los ETags, en el sentido que permiten a los clientes validar el estado de las
respuestas almacenadas en caché. En vez de generar un hash a partir del contenido
del mensaje de respuesta, un timestamp es usado y comparado para determinar
si las respuestas en caché son válidas.

<br>

A continuación describimos el flujo del almacenamiento en caché por los
HEADERS Last-Modified/If-Modified-Since:

<br>

1 El cliente realiza una petición HTTP solicitando un recurso.

```
GET /libros HTTP/1.1
Host: localhost:8080
Accept: application/json
```

2 El API adjunta a la respuesta el HEADER Last-Modified con la fecha de la
última modificación del recurso solicitado.

```
HTTP/1.1 200 OK
Server: Apache-Coyote/1.1
Last-Modified: 2017-01-17T23:28:56.782Z
Accept: application/json
Content-Length: 93
Date: Accept: 2017-02-17T23:28:56.782Z

{
  "titulo": "APIs RESTful",
  "anio": "2017"
}
```  

3 El cliente almacena en caché la URL del API, la fecha Last-Modified y la respuesta. En la
siguiente petición al API, el cliente incluirá el HADER **If-Modified-Since**:{last-modified-value},
con la fecha almacenada en caché.  

```
GET /libros HTTP/1.1
Host: localhost:8080
Accept: application/json
If-Modified-Since: 2017-01-17T23:28:56.782Z
```

4  El API compara la fecha del HEADER **If-Modified-Since** con la fecha
de modificación en el API; si son iguales el servidor retorna el status code
 **304 Not Modified**, indicando al cliente que use la respuesta almacenada
 en caché.  

```
HTTP/1.1 304 Not Modified
Server: Apache-Coyote/1.1
Date: Accept: 2017-02-17T23:28:56.782Z
Last-Modified: 2017-01-17T23:28:56.782Z
```  
<br>
