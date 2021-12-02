## Firmafy para desarrolladores

La api de firmafy se ha planteado como una herramienta multiplataforma que permitirá a nuestros clientes la integración de nuestro servicio con sus sistemas, de manera que puedan enviar documentación a sus clientes, comprobar el estado de las firmas, y demás operaciones mediante una interfaz rápida, segura, y fácil de integrar.

*Importante - Para otros idiomas diferentes a español , no traducir el nombre de los parámetros.


### Empezamos la integración: Login

##### Descripción: 
Lo primero que debemos obtener para comenzar a operar con la API de Firmafy es el token que nos autenticará en el sistema. 
La validez del token será de 4 horas.

Un ejemplo de ello puede verse a continuación:

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`
##### Método:
`POST`
##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | login |
| usuario  | string | (su usuario) |
| password | string | (su clave) |

##### Respuesta

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| data     | string| token |

```json
{
    "error": false,
    "data": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

### Obtener id usuario (id_show)

##### Descripción: 
Además de obtener el token que nos permitirá autenticarnos en futuras peticiones, necesitaremos saber cual es nuestro 
id de usuario (id_show).


##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`
##### Método:
`POST`
##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | Consultar_Cliente_Nif |
| token  | string | (su token) |
| cif | string | (su cif/dni) |

##### Respuesta

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| message     | string| (mensaje que aporta información adicional) |
| data     | Object| (información del usuario entre la que está su id_show) |

```json
    "data": {
        ...
        "id_show": "25d122ba412fda5eed63730c9f8c25f3"
    }'
```


### Solicitar firma

##### Descripción: 
Una vez estemos autenticados y hayamos obtenido nuestro token, podremos comenzar a solicitar las firmas de nuestros 
clientes al documento que elijamos. Para ello, haremos uso de la plantilla previamente creada en
[app.firmafy.com](https://app.firmafy.com), en la cual ya habremos indicado la posición de las firmas para el documento
que nos interese enviar.

Un ejemplo de ello puede verse a continuación:

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`
##### Método:
`POST`
##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | Solicitar_Firma |
| token  | string | (su token) |
| signer | array | (array con los firmantes en json) |
| pdf |  *CURLFile | (documento original a firmar) |
| template_name |  string | (nombre plantilla) |
| send_form |  bool | true/false) | 
| id_show |  string | (id_usuario) |
| type_notifications |  string | email,sms |
| mail_notification |  bool | true/false |
| fecha_vencimiento | datetime | Y-m-d H:i:s |

###### Ejemplo signer:

*Hay varias opciones para enviar el PDF además de hacerlo por CURL
- En base64:

`pdf_base64` (string) PDF codificado en base64 y añadir

`pdf_name` (string) Nombre del archivo

- A través de una url pública:

`pdf_url` (string) PDF codificado en base64 y añadir

`pdf_name` (string) Nombre del archivo

Valores posibles de "role": `PERSONA FISICA` , `PERSONA JURIDICA`

*Importante - send_form: Indica que se trata de una plantilla de FORMULARIO, por lo que el firmante recibirá un formulario a rellenar, de lo contrario false.

*Importante - template_name: Indica el nombre de la plantilla creada previamente ( si no se indica, las firmas van el lateral izquierdo de todas las páginas )

*Importante -  type_notifications: para poder notificar por sms , debe de tener SMS disponibles, de lo contrario se enviará por email.

*Importante - mail_notification: se omite enviar el enlace al cliente.

*Importante - fecha_vencimiento: No incluir como parámetro si no se quiere fecha de vencimiento.

Valores posibles de "cargo": `Administrador` , `Interesado`, `Empresario` etc...  (cualquier dato identificativo para el firmante).

A continuación se muestra un ejemplo con dos firmantes:
```json
[
  {
    "role": "PERSONA FISICA",
    "nombre": "Jhon Smith",
    "nif": "12345678A",
    "cargo": "Gerente",
    "email": "prueba@gmail.com",
    "telefono": 666666666,
    "empresa": "",
    "cif": "",
    "type_notifications": "email"
  },
  {
    "role": "PERSONA FISICA",
    "nombre": "Jhon Smith 2",
    "nif": "98765432B",
    "cargo": "Responsable",
    "email": "prueba2@gmail.com",
    "telefono": 777777777,
    "empresa": "",
    "cif": "",
    "type_notifications": "email,sms"
  } 
]
```

##### Respuesta

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| message     | string| (mensaje que aporta información adicional) |
| data     | string| (csv del documento) |

```json
{
    "error": false,
    "message": "Documento Enviado",
    "data": "xxxxxxx"
}
```

##### Excepciones
Si se produjese alguna excepción, obtendríamos en la respuesta el valor de `error` a `true` y el valor de `message` 
variará en función de la excepción producida, aportando información sobre la misma, para que podamos depurar dicho error.

Ejemplo de excepciones controladas:

- Si el número de páginas enviadas supera al de la plantilla creada, se obtendrá el mensaje 
`Número de páginas distinto al de la plantilla`
- Si la plantilla no existe o el número de firmantes es superior al de la plantilla, el mensaje será `Plantilla no encontrada` 

### Solicitar firma con asunto del mensaje y cuerpo del mensaje determinado (SIN PLANTILLA)

Mismo procedimiento que el ejemplo anterior, salvo teniendo en cuenta que cambia el nombre del action y se añaden dos parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | request |
| subject  | string | (Asunto del Email) |
| message | string | (Cuerpo del Email) |

*Siempre que se envíe el parámetro 'template_name':null  se ubicarán las firmas en el lateral izquierdo.
En caso de enviar el nombre de una plantilla: si existen los campos subject y message, van a tomar prioridad éstos sobre la el asunto y mensaje de la plantilla (el resto de valores no los sobreescribiremos).
Si queréis seguir haciendo uso del asunto y mensaje de vuestra plantilla, simplemente no enviar los parámetros subject y message, o dejarlos vacíos.


### Consultar estado envío
*CONSULTAR CON SOPORTE

Agregar Respuesta Automática (Webhook)

Descripción:
Devuelve automáticamente la respuesta al final de proceso de firma. Indicando el CSV del documento, rutas de descarga de documento firmado, ruta de descarga de auditoría e información de los firmantes.
##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`
##### Método:
`POST`
##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | addWebhook |
| id_show   | string | identificador del usuario |
| token  | string | token de inicio de sesion |
| type | int | Tipo de evento al que se desea suscribir |
| url_webhook  | string | url donde enviaremos la respuesta |

## Tipos de Evento:
- 1 : Documento Firmado
- 2 : Aviso de Firma Completada

Un ejemplo de la estructura que se devuelve es la siguiente:

```json
{
 "type":"Firmar Documento",
 "daterequest":"2021-02-09 16:02:34",
 "datesign":"2021-02-09 16:07:25",
 "status":"FIRMADO",
 "subject":"Firmafy | Solicitud de Firma",
 "csv":"xxxxxxxxxxxxxx",
 "size":"35687",
 "sender":"SYSTEM TEST",
 "docsigned":"https://drive.google.com/uc?id=xxxxxxxxxxxxxxxxxxx&export=download",
 "docaudit":"https://drive.google.com/uc?id=xxxxxxxxxxxxxxxxxxxx&export=download",
 "signer":"[array]"
}
```
Por cada firmante recibirás algo así:
```json
[
 {
  "name":"xxxxxx",
  "phone":"xxxxx",
  "nif":"xxxxxxx",
  "email":"xxxxxx@gmail.com",
  "status":"ACTIVO",
  "compliance":"true/false",
  "datesign":"2021-02-09 16:06:32"
 }
]
```
## PROYECTO EN POSTMAN PARA CONSULTAR 

<div class="postman-run-button"
data-postman-action="collection/fork"
data-postman-var-1="7469087-c91f148d-d283-4798-ba1c-36ed95fffff7"
data-postman-collection-url="entityId=7469087-c91f148d-d283-4798-ba1c-36ed95fffff7&entityType=collection&workspaceId=cbf8dde0-f772-422a-a1ce-4d460e55043f"></div>
<script type="text/javascript">
  (function (p,o,s,t,m,a,n) {
    !p[s] && (p[s] = function () { (p[t] || (p[t] = [])).push(arguments); });
    !o.getElementById(s+t) && o.getElementsByTagName("head")[0].appendChild((
      (n = o.createElement("script")),
      (n.id = s+t), (n.async = 1), (n.src = m), n
    ));
  }(window, document, "_pm", "PostmanRunObject", "https://run.pstmn.io/button.js"));
</script>

*Clonar Proyecto de Postman
 
 
### Invalidar Solicitud de Firma

Función para invalidar solicitud de firma y desactivar links de los firmantes

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | invalidar_documento |
| token  | string | (su token) |
| csv | string | csv del documento |
 
 Ejemplo:
 
```json
{
  "action":"invalidar_documento",
  "token":"",
  "csv":"xXXXXXXXXXX"
}
```
