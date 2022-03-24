## API FIRMAFY 

La API de FIRMAFY se ha planteado como una herramienta multiplataforma que permitirá a nuestros clientes la integración de nuestro servicio con sus sistemas, de manera que puedan enviar documentación a sus clientes, comprobar el estado de las firmas, y demás operaciones mediante una interfaz rápida, segura, y fácil de integrar.


Para realizar las pruebas de integración puedes apoyarte en nuestro proyecto de POSTMAN.

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

   
**Importante** - Para otros idiomas diferentes a español , no traducir el nombre de los parámetros.


    
# 1. OBTENER TOKEN

En primer lugar para trabajar con la API es necesario obtener el token de acceso. Recomendamos antes de iniciar el proceso, ponerse en contacto con el equipo de soporte técnico de Firmafy a través del chat, para confirmar que se disponen de permisos de llamada.
##### Método:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`

##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | login |
| usuario  | string | (Usuario Firmafy) |
| password | string | (Clave Firmafy) |



##### Respuesta

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| data     | string| token |

- Ejemplo de respuesta con **Login correcto**

```json
{
    "error": false,
    "data": "xxxxxxxxxxxxxxxxTOKENxxxxxxxxxxxxxxxx"
}
```

# 2. OBTENER PUBLIC KEY

El siguiente paso es obtener el public_key (id_show) para enviar solicitudes desde la API.
Para usuarios registrados en Firmafy podrás ubicarlo en el menú "Configuración" de tu Panel Cliente. 

# 3. SOLICITUD DE FIRMA
Con el **Token** y el **id_show** ya se puede realizar la solicitud de firma. 

Los parámetros **obligatorios** para realiar una solicitud de firma son los siguientes:

##### Método:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro | 
| -----------------| -------------- | --------------- |  
| action   | string | request | 
| token  | string | su token |
| id_show |  string | id_show | 
| signer<sup>1</sup> | string |  array de firmantes en jSON | 
| pdf <sup>2</sup> |   CURLFile | documento original a firmar |


#### <sup>1</sup> PARÁMETRO `signer` ( Array de Firmantes )

El parámetro signer contiene la estructura del o de los firmantes para los que se realiza la solcitiud de firma. Ha de contener aquellos datos necesarios para que Firmafy pueda garantizar la integridad del firmante. A continuación se muestra un array **signer** de dos firmantes, uno como **Persona Física** y otro como **Persona Jurídica**.

```json
[
  {
    "nombre": "Wence Criado",
    "nif": "12345678A",
    "cargo": "Trabajador",
    "email": "soporte@firmafy.com",
    "telefono": 600000000, 
    "type_notifications": "email"
  },
  {
    "nombre": "Fran Cortes",
    "nif": "98765432B",
    "cargo": "Responsable",
    "email": "hola@firmafy.com",
    "telefono": 777777777,
    "empresa": "FIRMAFY",
    "cif": "B11111111",
    "type_notifications": "email,sms"
  } 
]
```
Al igual que en la aplicación web, para realizar la solicitud de firma son obligatorios los datos personales: 

| Nombre Parámetro | Valor Parámetro |
| -----------------| -------------- | 
| nombre     | Nombre Completo| 
| nif     | DNI / NIE| 
| cargo    | Etiqueta identificativa del firmante  | 
| email     | Email para notificar al firmante| 
| telefono     | Móvil del firmante donde recibe el OTP| 
| empresa    | Nombre de empresa ( Si es P. JURÍDICA )  | 
| cif     | Cif Empresa ( Si es P. JURÍDICA) | 
| type_notifications     | *Medio por el que recibe el aviso de firma (email y/o sms)| 


*Hay que prestar **mucha atención** al parámetro ``type_notifications`` ya que la vía de notififación puede ser diferente para cada firmante, solo en caso de que se use ``"type_notifications" : "sms"`` el parámetro ``email`` puede ir vacío. Esta notificación es para avisar de la solicitud de firma y de la copia del documento firmado. **El OTP siempre se envíará por SMS**

 
#### <sup>2</sup> PARÁMETRO ``pdf``

Existen otras opciones además de cURL para enviar el documento **PDF**, para ello habría que omitir el parámetro ``pdf`` anterior y utilizar los siguientes en función del que se elija:

-  Enviar en base64:
 
| Nombre Parámetro | Tipo Parámetro | Valor Parámetro | 
| -----------------| -------------- | --------------- |  
| pdf_name  | string | Nombre del archivo |
| pdf_base64   | string | PDF codificado en base64 | 



- Enviar a través de una url pública:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro | 
| -----------------| -------------- | --------------- |  
| pdf_name  | string | Nombre del archivo |
| pdf_url   | string | Url del documento PDF  | 


## 3.1 PARÁMETROS OPCIONALES A LA SOLICITUD DE FIRMA



Con los parámetros anteriores ya tendríamos los elementos suficientes para hacer una solicitud de firma, aunque existen otros parámetros opcionales para personalizar la solicitud.

A continuación se muestran todos los parámetros disponibles y en qué consiste cada uno.

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro | 
| -----------------| -------------- | --------------- | 
| template_name |  string | Nombre Plantilla |

Podemos utilizar una Plantilla creada en plataforma para que vía API podamos utilizar el Asunto y el Mensaje del email que queremos que reciban nuestros firmantes, además de utilizar la ubicación de los elementos de firma en el documento.

**MUY IMPORTANTE**, siempre que se utilice una PLANTILLA, el número de hojas del documento a enviar tiene que coincidir con el número de hojas de la PLANTILLA creada.

**Si no se indica** ``template_name``, se utilizarán el Asunto y Mensaje predeterminados de Firmafy , además de ubicar la firmas en el lateral izquierdo de todas las páginas.

**Existe la posibilidad de no utilizar Plantilla y de indicar manualmente la ubicación de las firmas**, además del asunto y el mensaje de la solicitud.

**Existe la posibilidad de indicar Plantilla para utilizar las coordenadas de los elementos** y además, pasar como parámetros el asunto y el mensaje (sobreescriben a los de la plantilla).

Para ello utilizar los siguientes parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro | 
| -----------------| -------------- | --------------- | 
|  <sup>3</sup> coordenadas | string  | JSON con ubicación de firmas  | 
| subject  | string | Asunto del Email | 
| message | string | Mensaje del Email | 
| cco | string | Indica los emails donde requiere enviar copia del PDF firmado ( separados por ; )| 
| signer_priority | bool | Activa la firma en orden | 
| mail_notification |  bool | true/false (Omite enviar notificación de solicitud de firma) | 
| fecha_vencimiento | datetime | Y-m-d H:i:s (Pasada la fecha, no se puede firmar) | 

#### <sup>3</sup> PARÁMETRO ``coordenadas``

Si se quiere indicar las coordenadas vía API de la ubicación de las firmas, hay que tener en cuenta las siguientes reglas:

- Usamos tamaño A4, la escala debe ser medida en milímetros, siendo el máximo de medidas 210x297mm (x,y)
- El punto que se toma como referencia corresponde a la esquina superior izquierda, desde allí comenzamos a dibujar.
- El tamaño recomendado para el cuadro de firma es de 40x15mm (ancho x alto)
- Se debe indicar el número del firmante siguiendo el mismo orden que en el array de firmantes.
- La propiedad ``side`` puede ser 1 o 0 (ver ejemplo) e indica un giro de 90º en el cuadro de firma para la firma lateral.
- Una página puede tener tantos cuadros de firma como se necesiten, incluso repetir el mismo firmante, siempre que se indique la posición correcta. 
- En una página no es necesario incluir todos los firmantes si no es necesario.
- Por cada página se debe construir un array que contenga los cuadros de firma de la misma. 
- Se debe pasar un array para cada página.

A continuación se muestra un ejemplo de un documento de **3 páginas**, la primera página tiene 2 cuadros de firma ubicados en el lateral y la tercera página sólo tiene un cuadro de firma del segundo firmante.

```json
[
    [
        {
            "signer": 1,
            "x": 5,
            "y": 120,
            "side": 1,
            "width": 40,
            "height": 10
        },
        {
            "signer": 2,
            "x": 5,
            "y": 170,
            "side": 1,
            "width": 45,
            "height": 15
        }
    ],
    [],    
    [ 
        {
            "signer": 2,
            "x": 80,
            "y": 210,
            "side": 0,
            "width": 45,
            "height": 15
        }
    ]
]

```

## 3.2 Ejemplo de respuesta de solicitud enviada correctamente


| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| message     | string| (mensaje que aporta información adicional) |
| data     | string| (CSV del documento) |

```json
{
    "error": false,
    "message": "Documento Enviado",
    "data": "xxxxxxx"
}
```
# 4. Obtener el estado del envío a través del Webhook

Para obtener los cambios de estado que se produzcan en la solicitudes de firma y así obtener el documento firmado y la auditoría, es necesario estar suscrito a los eventos de Webhook.

##### Método:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


##### Parámetros:

| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | addWebhook |
| id_show   | string | identificador del usuario |
| token  | string | token de inicio de sesion |
| type<sup>4</sup> | int |  Evento al que se desea suscribir |
| method<sup>5</sup>  | int | Método de envío de datos |
| url_webhook  | string | url donde Firmafy enviará la respuesta |



#### <sup>4</sup> PARÁMETRO ``type``


Hay 2 tipos de evento para suscribirse:

- 1 : Documento Firmado ( Cuando todos los firmantes han firmado el documento )
- 2 : Aviso de Firma Completada ( Cuando alguno de los firmantes ha firmado el documento )


#### <sup>5</sup> PARÁMETRO ``method``

- 1 : Array POST
- 2 : JSON 

**No es necesario** suscribirse al evento por cada solicitud, ya que si la URL a notificar es la misma en todas las solicitudes, bastaría con hacerlo solo 1 vez.

Un ejemplo del POST en HTTP que se devuelve es la siguiente:

```json
{
 "type":"Firmar Documento"(string),
 "daterequest":"2021-02-09 16:02:34", (datetime)
 "datesign":"2021-02-09 16:07:25", (datetime),
 "status":"FIRMADO", (string)
 "subject":"Firmafy | Solicitud de Firma", (string)
 "csv":"xxxxxxxxxxxxxx", (string)
 "size":"35687",(int)
 "sender":"SYSTEM TEST",(string)
 "filename":"Documento.pdf", (string)
 "docoriginal": pdf, (multipart form-data)
 "docsigned":  pdf, (multipart form-data)
 "docaudit": pdf, (multipart form-data)
 "signer":"[array]"
	 - Por cada firmante -
	[
	 {
	  "name":"xxxxxx",
	  "phone":"xxxxx",
	  "nif":"xxxxxxx",
	  "email":"soporte@firmafy.com",
	  "status":"ACTIVO",
	  "compliance":"true/false",
	  "datesign":"2021-02-09 16:06:32"
	 }
	]
}
```


# 5. Invalidar una Solicitud de Firma

Esta acción desactiva los enlaces de los firmantes que haya pendientes de firma


| Nombre Parámetro | Tipo Parámetro | Valor Parámetro |
| -----------------| -------------- | --------------- | 
| action   | string | invalidar_documento |
| token  | string | (su token) |
| csv | string | CSV del documento |
 
 Ejemplo:
 
```json
{
  "action":"invalidar_documento",
  "token":"",
  "csv":"xXXXXXXXXXX"
}
```


### Recomendamos que para la integración de la API, se pongan en contacto con nuestro servicio de soporte técnico  ``soporte@firmafy.com`` y así formar parte de la comunidad de Dev's en nuestro canal de SLACK.


