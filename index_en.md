## API FIRMAFY 

The FIRMAFY API has been designed as a multiplatform tool that will allow our clients to integrate our service with their systems, so that they can send documentation to their clients, check the status of signatures, and other operations through a fast, secure, and easy-to-integrate interface.

To carry out integration tests, you can rely on our POSTMAN project.

<div class="postman-run-button"
data-postman-action="collection/fork"
data-postman-visibility="public"
data-postman-var-1="7469087-8fbe653e-b510-4c8d-93b8-719f40e154ca"
data-postman-collection-url="entityId=7469087-8fbe653e-b510-4c8d-93b8-719f40e154ca&entityType=collection&workspaceId=268a76e5-4e22-4830-b2c9-86883b09dbaf"></div>
<script type="text/javascript">
  (function (p,o,s,t,m,a,n) {
    !p[s] && (p[s] = function () { (p[t] || (p[t] = [])).push(arguments); });
    !o.getElementById(s+t) && o.getElementsByTagName("head")[0].appendChild((
      (n = o.createElement("script")),
      (n.id = s+t), (n.async = 1), (n.src = m), n
    ));
  }(window, document, "_pm", "PostmanRunObject", "https://run.pstmn.io/button.js"));
</script>

   
**Important** - For languages ​​other than Spanish, do not translate the parameter names.


    
# 1. GET TOKEN

First of all, to work with the API you need to obtain the access token. We recommend that before starting the process, you contact the Firmafy technical support team via chat to confirm that you have access to the call permissions.
##### Method:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`

##### Parameters:

| Parameter Name | Parameter Type | Parameter Value |
| -----------------| -------------- | --------------- | 
| action   | string | login |
| usuario  | string | (User Firmafy) |
| password | string | (Password Firmafy) |



##### Respuesta

| Parameter Name | Parameter Type | Parameter Value |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| data     | string| token |

- Example of response with **Correct login**

```json
{
    "error": false,
    "data": "xxxxxxxxxxxxxxxxTOKENxxxxxxxxxxxxxxxx"
}
```

# 2. GET PUBLIC KEY

The next step is to get the public_key (id_show) to send requests from the API.
For registered users in Firmafy, you can find it in the "Settings" menu of your Client Panel.

# 3. SIGNATURE REQUEST
With the **Token** and the **public_key** you can now make the signature request.

The **mandatory** parameters to make a signature request are the following:

##### Method:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


##### Parameters:

| Parameter Name | Parameter Type | Parameter Value | 
| -----------------| -------------- | --------------- |  
| action   | string | request | 
| token  | string | the token |
| id_show |  string | public_key | 
| signer<sup>1</sup> | string |  array of signers in jSON | 
| pdf <sup>2</sup> |   CURLFile | original document to be signed |
| document_lang <sup>3</sup> |   string | Optional - notification language |


#### <sup>1</sup> PARAMETER `signer` ( array of signers )

The signer parameter contains the structure of the signer(s) for whom the signature request is being made. It must contain the data necessary for Firmafy to guarantee the integrity of the signer. Below is a **signer** array of two signers, one as a **Natural Person** and the other as a **Legal Entity**.

```json
[
  {
    "nombre": "Wence Criado",
    "nif": "12345678A",
    "cargo": "Worker",
    "email": "soporte@firmafy.com",
    "telefono": 600000000, 
    "type_notifications": "email"
  },
  {
    "nombre": "Fran Cortes",
    "nif": "98765432B",
    "cargo": "CEO",
    "email": "hola@firmafy.com",
    "telefono": 777777777,
    "empresa": "FIRMAFY",
    "cif": "B11111111",
    "type_notifications": "email,sms"
  } 
]
```
Al igual que en la aplicación web, para realizar la solicitud de firma son obligatorios los datos personales: 

| Parameter Name | Parameter Value |
| -----------------| -------------- | 
| nombre     | Full name | 
| nif     | DNI / NIE| 
| cargo    | Signer's identification tag  | 
| email | Email to notify the signer |
| telefono     | Signer's mobile phone where the OTP is received | 
| empresa    | Company name (If it is a LEGAL P.) |
| cif     | Company Cif (If it is a LEGAL P.) |
| type_notifications     | *Means by which you receive the signature notice (email and/or sms) |


*Pay **a lot of attention** to the ``type_notifications`` parameter since the notification path can be different for each signer. Only if ``"type_notifications" : "sms"`` is used, the ``email`` parameter can be empty. This notification is to notify of the signature request and the copy of the signed document. **The OTP will always be sent by SMS**

 
#### <sup>2</sup> PARAMETER ``pdf``

There are other options besides cURL to send the **PDF** document, for this you would have to omit the previous ``pdf`` parameter and use the following ones depending on the one you choose:

- Send in base64:
 
| Parameter Name | Parameter Type | Parameter Value | 
| -----------------| -------------- | --------------- |  
| pdf_name | string | File name |
| pdf_base64 | string | Base64 encoded PDF |



- Send via public url:

| Parameter Name | Parameter Type | Parameter Value | 
| -----------------| -------------- | --------------- |  
| pdf_name  | string | file name |
| pdf_url   | string | PDF document URL  | 

#### <sup>3</sup> PARAMETER ``document_lang``

If you want to notify in a language other than Spanish (by default, you do not have to indicate the PARAMETER in Spanish), indicate the desired available language as the value:

| Parameter Value | Description | 
| -----------------| -------------- |   
| EN   | English |
| IT    | Italian  | 
| CA   | Catalan |
| FR    | French  |
| IT    | Italian  | 


## 3.1 OPTIONAL PARAMETERS FOR THE SIGNATURE REQUEST



With the above Parameters we would already have enough elements to make a signature request, although there are other optional Parameters to customize the request.

Below are all the available Parameters and what each one consists of.

| Parameter Name | Parameter Type | Parameter Value | 
| -----------------| -------------- | --------------- | 
| template_name |  string | Template Name |

We can use a Template created on the platform so that via API we can use the Subject and Message of the email that we want our signers to receive, in addition to using the location of the signature elements in the document.

**VERY IMPORTANT**, whenever a TEMPLATE is used, the number of pages in the document to be sent must match the number of pages in the TEMPLATE created.

**If you do not indicate** ``template_name``, the default Subject and Message from Firmafy will be used, in addition to placing the signatures on the left side of all pages.

**There is the possibility of not using a Template and manually indicating the location of the signatures**, in addition to the subject and message of the request.

**There is the possibility of indicating a Template to use the coordinates of the elements** and also, passing the subject and message as Parameters (they overwrite those of the template).

To do this, use the following Parameters:

| Parameter Name | Parameter Type | Parameter Value | 
| -----------------| -------------- | --------------- | 
|  <sup>3</sup> coordinates | string  | JSON with signature location | 
| subject  | string | Email Subject | 
| message | string | Email message | 
| cco | string | Indicate the emails where you need to send a copy of the signed PDF (separated by ;)| 
| signer_priority | bool | Activate the signature in order | 
| mail_notification |  bool | 1/0 (Skip sending signature request notification, if true by default do not send PARAMETER) | 
| fecha_vencimiento | datetime | Y-m-d H:i:s (After the date, you cannot sign) | 
| reply-to | string | Default sender email | 

#### <sup>3</sup> PARAMETER ``coordinates``

If you want to indicate the coordinates via API of the location of the signatures, you must take into account the following rules:

- We use A4 size, the scale must be measured in millimeters, the maximum measurements being 210x297mm (x,y)
- The point that is taken as a reference corresponds to the upper left corner, from there we begin to draw.
- The recommended size for the signature box is 40x15mm (width x height)
- The number of the signer must be indicated following the same order as in the array of signers.
- The ``side`` property can be 1 or 0 (see example) and indicates a 90º turn in the signature box for the side signature.
- A page can have as many signature boxes as needed, even repeating the same signer, as long as the correct position is indicated.
- On a page it is not necessary to include all the signers if it is not necessary.
- For each page an array must be built that contains the signature boxes of the same. - An array must be passed for each page.

Below is an example of a **3-page** document, the first page has 2 signature boxes located on the side and the third page only has one signature box for the second signer.

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

## 3.2 Example of a successfully sent request response


| Parameter Name | Parameter Type | Parameter Value |
| -----------------| -------------- | --------------- | 
| error    | bool  | true/false |
| message     | string| (message that provides additional information) |
| data     | string| (Document CSV) |

```json
{
    "error": false,
    "message": "Documento Enviado", 
    "data": "xxxxxxx"
}
```

# 4. Certified Email Request

Requires the **Token** and **id_show** obtained previously.

The **mandatory** Parameters to send a certified email are:

##### Method:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


##### Parameters:

| Parameter Name | Parameter Type | Parameter Value | 
| -----------------| -------------- | --------------- |  
| action   | string | emailrequest | 
| token  | string | your token |
| id_show |  string | public_key | 
| subject | string | Notification email subject |
| body | string | Message body, may contain HTML code |
| to <sup>4.1</sup> | string | JSON Stringify recipient data |
| lang <sup>4.2</sup> | char | Optional - Language of the request, notifications and Audit. |
| attachment <sup>4.3</sup> | file/array | Optional - File attached to the request |
| reply-to | string | Default sender email |


## <sup>**4.1**</sup> Example of recipient data:

```json
{
  "name":"Jhon Smith",
  "email": "soporte@firmafy.com"
  
}
```
## <sup>**4.2**</sup> Notification Language:

ES - Default (no need to specify)

EN - English

IT - Italian

FR - French

CA - Catalan

## <sup>**4.3**</sup> Attachments:

- Option 1

| Parameter Type | Parameter Value | 
| -------------- | --------------- |  
| file   			| cUrl object | 

- Option 2

| Parameter Type | Parameter Value | 
| -------------- | --------------- |  
| array   			| ```[{"filename": "Doc1.pdf","url": "xxxxx"},{"filename": "Doc2.pdf","base64": "base64"}]```| 






# 5. Certified SMS Request

Requires the **Token** and **id_show** obtained previously.

The **mandatory** Parameters to send a certified SMS are:

##### Método:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


##### Parameters:

| Parameter Name | Parameter Type | Parameter Value | 
| -----------------| -------------- | --------------- |  
| action   | string | smsrequest | 
| token  | string | your token |
| id_show |  string | public_key | 
| body | string |  Message text, maximum 160 characters |
| to <sup>5.1</sup> | string | Recipient data JSON Stringify |
| lang <sup>5.2</sup> | char | Optional - Audit language |


## <sup>**5.1**</sup> Example of recipient data:

```json
{
  "name":"Jhon Smith",
  "country":34,
  "phone":612345678
}
```
## <sup>**5.2**</sup> Audit Language:

ES - Default (no need to specify)

EN - English

IT - Italian

FR - French

CA - Catalan



# 6. Event Subscription - Webhook

To get the status changes that occur in the requests, it is necessary to be subscribed to the Webhook events.

##### Method:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


##### Parameters:

| Parameter Name | Parameter Type | Parameter Value |
| -----------------| -------------- | --------------- | 
| action   | string | webhook |
| id_show   | string | public_key |
| token  | string | your token |
| type<sup>6.1</sup> | int | Event to subscribe to |
| method<sup>6.2</sup> | int | Data sending method |
| url_webhook | string | url where Firmafy will send the response |



## <sup>6.1</sup> PARAMETER ``type``


There are 2 types of events to subscribe to:

- 1 : Document Signed (When all signers have signed the document)
- 2 : Signature Completed Notice (When any of the signers have signed the document)
- 5 : Retry Delivery (Failed Webhook)<sup>6.1.1</sup>

**<sup>6.1.1</sup>** Firmafy listens to the HTTP response codes from its server to determine whether the webhook delivery is successful or not. All notifications that receive a 4xx or 5xx http status in response are recorded as failed. Firmafy will attempt to deliver the notification again up to 2 more times, every 30 minutes.
## <sup>6.2</sup> PARAMETER ``method``

- 1 : POST array
- 2 : JSON 

**It is not necessary** to subscribe to the event for each request, since if the URL to be notified is the same in all requests, it would be enough to do it only once.


An example of the HTTP POST that is returned is the following:

```json
{
"type":"Document Signed"(string),
 "daterequest":"2021-02-09 16:02:34", (datetime)
 "datesign":"2021-02-09 16:07:25", (datetime),
 "status":"SIGNED", (string)
 "subject":"Firmafy | Signature Request", (string)
 "csv":"xxxxxxxxxxxxxxx", (string)
 "size":"35687",(int)
 "sender":"SYSTEM TEST",(string)
 "filename":"Document.pdf", (string)
 "docoriginal": pdf, (multipart form-data)
 "docsigned": pdf, (multipart form-data)
 "docaudit": pdf, (multipart form-data)
 "signer":"[array]"
 - For each signatory -
 [
 {
 "name":"xxxxxx",
 "phone":"xxxxx",
 "nif":"xxxxxxx",
 "email":"soporte@firmafy.com",
 "status":"ACTIVE",
 "compliance":"true/false",
 "datesign":"2021-02-09 16:06:32"
 }
 ]
}
```



# 5. Invalidate a Signature Request

This action deactivates the links of the signers that are pending signature.

##### Method:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


| Parameter Name | Parameter Type | Parameter Value |
| -----------------| -------------- | --------------- | 
| action   | string | invalidar_documento |
| token  | string | (your token) |
| csv | string | Document CSV |
 
 Example:
 
```json
{
  "action":"invalidar_documento",
  "token":"",
  "csv":"xXXXXXXXXXX"
}
``` 

# 6. Get available consumption

This endpoint allows you to get the available consumption.

##### Method:
`POST`

##### URL:
`https://app.firmafy.com/ApplicationProgrammingInterface.php`


| Parameter Name | Parameter Type | Parameter Value |
| -----------------| -------------- | --------------- | 
| action   | string | balance |
| id_show   | string | public_key |
| token  | string | your token |
 
 Sample Answer:
 
```json
{
    "error": false,
    "message": "Disponibles", (Available)
    "credits": 50,
    "sms": 25,
    "mb": 20.331583,
    "days": 23
}
``` 
If you make a signature request to the API and do not have available credits, a message is returned:

```json
{
"error": true,
    "message": "Se ha alcanzado el numero máximo de envios disponibles en su plan. En caso de querer adquirir un nuevo plan, visite www.firmafy.com/tarifas y pongase en contacto con su proveedor" 
}
``` 
The translated response in English would be: "The maximum number of shipments available in your plan has been reached. If you wish to purchase a new plan, please visit www.firmafy.com/tarifas and contact your provider."
*
### We recommend that for API integration, you contact our technical support service ``soporte@firmafy.com`` and thus become part of the Dev community on our SLACK channel.


