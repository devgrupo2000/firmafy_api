## Firmafy para desarrolladores

La api de firmafy se ha planteado como una herramienta multiplataforma que permitirá a nuestros clientes la integración de nuestro servicio con sus sistemas, de manera que puedan enviar documentación a sus clientes, comprobar el estado de las firmas, y demás operaciones mediante una interfaz rápida, segura, y fácil de integrar.



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
| template_session |  string | false  |
| template_name |  string | (nombre plantilla) |
| id_show |  string | (id_usuario) |
| type_notification |  string | email,sms |
| mail_notification |  bool | false |

###### Ejemplo signer:

*Se puede optar por enviar el PDF por base 64, en ese caso hay que sustuir el parámetro pdf por:

pdf_base64 (string) PDF codificado en base64 y añadir

pdf_name (string) Nombre del archivo

Valores posibles de "role": `PERSONA FISICA` , `PERSONA JURIDICA`

Importante - template_sessión: Indica que las plantillas pertenecen al id_show , no al usuario logueado en la clase Firmafy.php

Importante -  type_notification: para poder notificar por sms , debe de tener SMS disponibles, de lo contrario se enviará por email.

Importante - mail_notification: se omite enviar el enlace al cliente.

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
    "cif": ""
  },
  {
    "role": "PERSONA FISICA",
    "nombre": "Jhon Smith 2",
    "nif": "98765432B",
    "cargo": "Responsable",
    "email": "prueba2@gmail.com",
    "telefono": 777777777,
    "empresa": "",
    "cif": ""
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


### Consultar estado envío

Agregar Respuesta Automática (Webhook)

Descripción:
Devuelve automáticamente la respuesta al final de proceso de firma. Indicando el CSV del documento y el estado del mismo.
Por ejemplo https://dominio.com/webhook_firmafy ?csv=XXX&estado=FIRMADO

URL:
https://app.firmafy.com/ApplicationProgrammingInterface.php

Método:
POST

##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | add_edit_webhook |
| token  | string | (su token) |
| id_show | string | id_usuario |
| url_webhook | string | url donde enviaremos la respuesta |



### Registro y asociación de plan

##### Descripción: 
__Para realizar esta acción es necesario contar con los privilegios correspondientes.__ 

Cuando queramos registrar a usuarios, y seguidamente asociarle un plan deberemos hacerlo de la siguiente manera:


##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`
##### Método:
`POST`
##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | nc_nuevo_usuario_plan |
| token  | string | (su token) |
| data | string | (json<sup>1</sup>) |

##### Formato JSON<sup>1</sup>:
A continuación se muestran dos ejemplos de la estructura que debe tener el parametro `data` en `json`.

__Para registrar y asociarle el plan a una persona física :__
```
{
  "datos_usuario": {
    "email": "XXXXX"
  },
  "datos_contacto": {
    "dni": "XXXXX",
    "nombre": "XXXXX",
    "primer_apellido": "XXXXX",
    "segundo_apellido": "XXXXX",
    "cargo": 1,
    "direccion": "XXXXX",
    "ciudad": "XXXXX",
    "codigo_postal": "XXXXX",
    "movil": 000000000,
    "email": "XXXXX"
  },
  "producto": {
    "id_producto": "XXXXX"
  }
}
```
__Para registrar y asociarle el plan a una empresa:__
```
{
     "datos_usuario": {
       "email": "XXXXX"
     },
     "datos_facturacion": {
       "nif": "XXXXX"
     },
     "datos_contacto": {
       "empresa": "XXXXX",
       "dni": "XXXXX",
       "nombre": "XXXXX",
       "primer_apellido": "XXXXX",
       "segundo_apellido": "XXXXX",
       "cargo": 1,
       "direccion": "XXXXX",
       "ciudad": "XXXXX",
       "codigo_postal": "XXXXX",
       "movil": 000000000,
       "email": "XXXXX"
     },
     "producto": {
       "id_producto": "XXXXX"
     }
   }
```

##### Respuesta

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| message     | string | (mensaje que aporta información adicional) |
| data     | string | (información sobre el proceso) |


##### Excepciones
Si se produjese alguna excepción, obtendríamos en la respuesta el valor de `error` a `true` y el valor de `message` 
variará en función de la excepción producida, aportando información sobre la misma, para que podamos depurar dicho error.

Ejemplo de excepciones controladas:

- Si se intentase registrar al usuario en un plan gratuito que ya ha disfrutado, `message` contendría el mensaje `Acción no realizada a usuario existente: El usuario ya ha disfrutado del plan solicitado.` y `data` `El usuario ya ha disfrutado del plan solicitado. Puede ponerse en contacto con Firmafy`
- Cuando se indique un `id_producto` incorrecto, en `message` se obtendría `El nombre del plan indicado no existe`
- Para cuando alguno de los datos no sean correctos, podremos saberlo porque `message` contendrá un resumen de aquellos datos incorrectos: `Error procesando el JSON: JSON incorrecto. El DNI no es correcto` y `data` `datos_usuario: Los datos de usuario son correctos | datos_contacto: [datos_contacto] El dni no es correcto | datos_facturacion: Los datos de facturación de LA PERSONA FÍSICA son correctos | datos_producto: El producto indicado es correcto`
- Si la estructura JSON es incorrecta `El formato json no es correcto`
- Si el email indicado tiene asociado un cif/nif distinto, el mensaje que obtendríamos sería `Usuario no creado. El email indicado tiene asociado otro dni. Por favor, ponte en contacto con firmafy.`
