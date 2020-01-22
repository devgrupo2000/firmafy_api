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
| pdf |  CURLFile | (documento original a firmar) |
| template_name |  string | (nombre plantilla) |
| id_show |  string | (id_usuario) |

###### Ejemplo signer:
Valores posibles de "role": `PERSONA FISICA` , `PERSONA JURIDICA`

Valores posibles de "cargo": `Administrador` , `Gerente`, `Responsable`

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

##### Descripción: 
Una vez solicitadas la/s firma/s para nuestro/s documento/s, nos interesará conocer el estado de las mismas, para, por ejemplo,
conocer cuantos firmantes han firmado, o quien/es falta/n por firmar.


##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`
##### Método:
`POST`
##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | estado_envio |
| token  | string | (su token) |
| csv | string | (csv del documento) |


##### Respuesta

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| message     | string | (mensaje que aporta información adicional) |
| data     | Object | (información sobre la solicitud de firma) |

```json
    "data": {
        "tipo": "Firmar Documento",
        "solicitud": "0000-00-00 00:00:00",
        "firmado": "0000-00-00 00:00:00",
        "estado": "TRAMITADO",
        "asunto": "Firmafy | Solicitud de Firma",
        "mensaje": "<p><br></p><p>Le enviamos el documento para que lo firme a través de nuestro sistema de firma avanzada <b>Firmafy</b>. Con este sistema podrás firmar tus documentos de una forma rápida, segura y legal. </p><p>Cualquier consulta, no dudes en contactar con nosotros.</p>",
        "csv": "xxxxxxxxxxxxx",
        "size": "0000000",
        "remitente": "xxxxxxxxxx",
        "url_documento_firmado": "https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "url_documento_auditoria": "https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "url_documento_origial": "https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "firmante": [
            {
                "nombre": "xxxxxxxxxxxxxxxxxxxxxxxx",
                "cargo": "xxxxx",
                "telefono": "666666666",
                "nif": "xxxxxxxxx",
                "email": "xxxxxxxxx@gmail.com",
                "link": "https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
                "link_usado": "1",
                "estado": "INACTIVE",
                "conformidad": "Aceptó y Firmó",
                "firmado": "0000-00-00 00:00:00"
            }
        ]
    }
```

##### Excepciones
Si se produjese alguna excepción, obtendríamos en la respuesta el valor de `error` a `true` y el valor de `message` 
variará en función de la excepción producida, aportando información sobre la misma, para que podamos depurar dicho error.

Ejemplo de excepciones controladas:

- Si el csv fuese incorrecto el mensaje recibido sería `CSV no encontrado.`


