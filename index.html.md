---
title: OTPSecure

language_tabs:
 - http
 - python

toc_footers:

includes:
  - errors

search: true
---

# Introducción

OTPSecure es un servicio que genera otps (one time password) seguras para la "firma" o confirmación por parte del usuairo de condiciones o términos legales.
 
# Requisitos


Para poder usar este API es necesario tener un apikey
de empresa y una clave para
poder autenticarse por medio de autenticación hmac.

# Uso

## Proceso Completo

```http
GET http://api.otpsecure.net/ HTTP/1.1
Accept : application/json
date : date
authorization : hmac string
User-Agent : otpsecure/ApiClient/1.0 Python1.0
Content-Type : application/json
```

```python
#Inicialización SDK cliente

import otpsecure
----
# Change with yours.
APIKEY 	= "0-sP5fAM48_9L2bu_iY5eYnsGAI_VJjI"
SECRET 	= "SK125w0bYgtoya8JCmgMrCYzBbukeJul"

# Init the Otp Secure Client.
client = otpsecure.Client(APIKEY, SECRET)
```


```python
#Se solicita el token de operación

options = {
		'movil'		: '693751891',
		'customid'	: '123456789',
		'sandbox'	: True,
		'pdf_filename'	: 'test.pdf',
		'pdf_content'	: b64_pdf_file_content,
		'smsusr'		: '->Usuario<-',
		'smspwd'		: '->Contraseña<-',
		'callback'      : '->https://url-destino<-'
	}
		
client.otp(options)
```

*En python se debe instalar el sdk en el shell:

`$ pip install otpsecure`


1. Incluir en el footer del html la librería cliente js de la url http://otpsecure.net/otpecertic-1.0.0.min.js
`<script src="http://otpsecure.net/otpecertic-1.0.0.min.js"/>`

2. Solicitar token de operacion desde el sdk cliente en el server haciendo la petición al servicio.

La url del servicio es http://api.otpsecure.net/

`<script>`

`	otpEcertic.setup({`

`		token: "{{ otp.clientToken }}",`

`		sandbox: {{ sandbox }}",`

`		container: "validarOTP-form"`

`		});`

`</script>`


>Esta llamada devuelve como respuesta:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{ 
	"clientToken": "f39363e5413efdba2bccf....49604d1370e0",
	"id": "577a782e7c049eb24f2c44b4",
	"customid": "123456789",
	"clientOtp": "038513",
	"pdf_shortUrl": "http://bit.ly/29oCHwq",
	"html_shortUrl": "http://bit.ly/29dvhIX"
}
```

```python
# Objeto otp
	otp.clientToken
	otp.id
	otp.customid
	otp.clientOtp
	otp.pdf_shortUrl
	otp.html_shortUrl
```

> Una vez recibido el token se inicializa el cliente JavaScript

```html
<script src="http://otpsecure.net/otpecertic-1.0.0.min.js">
</script>

<script>
 otpEcertic.setup({
	 token: "{{ otp.clientToken }}",
	 sandbox: "{{ sandbox }}",
	 container: "validarOTP-form"
	});
</script>
```

> Esto inicializa el formulario de validación otp para el usuario dentro del "container" html especificado.


Método : POST

URL : http://api.otpsecure.net/sms

Data : [*Ver Sección Envío OTP / Sms](#env-o-otp-sms)


##Envío OTP / Sms

>Ejemplo Envío de OTP

```http
POST /sms HTTP/1.1
Content-Type: application/json

{
 "movil": "666123123", 								
 "pdf_filename": "contrato.pdf",							
 "pdf_content": "TWFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vd...", 
 "customid": "123456789",  
 "sandbox": true,
 "smsusr": "Usuario",
 "smspwd": "Contraseña1234*",
 "callback": "https://url-destino"
}
```

```python

import otpsecure

client = otpsecure.Client('->APIKEY<-','->SECRET<-')

response = client.otp({
			'movil'			: '666666666',
			'customid'		: '123456789',
			'sandbox'		: True,
			'pdf_filename'	: 'test.pdf',
			'pdf_content'	: '->b64_pdf_file_content<-',
			'smsusr'		: 'Usuario',
			'smspwd'		: 'Contraseña1234*',
			'callback'		: 'https://url-destino'
		})
```

Para iniciar la generación y envío de la OTP por SMS:

POST /sms

Data: Ver Tabla

Parámetro | Tipo | Descripción
--------- | ---- | -----------
movil | String | Movil al que se enviará la OTP por sms.
pdf_filename | String | Nombre del archivo PDF.
pdf_content | String | Contenido del archivo pdf codificado en Base 64.
customId | String *Opcional | Id Personalizado
sandbox | Boolean *Opcional | Modo de pruebas. Si True no se envía ningún sms y se devuelve la OTP para su visualización a la librería cliente JS.
smsusr | String | Usuario de la pasarela de envío de sms. Obligatorio para el envío de la OTP por mensaje sms. Opcional en modo SANDBOX.
smspwd | String | Contraseña de la pasarela de envío de sms. Obligatorio para el envío de la OTP por mensaje sms. Opcional en modo SANDBOX.
callback | Opcional | URL de callback a la que se devolverán los cambios de estado de una operación.

###Response

```http
HTTP/1.1 200 OK
Content-Type: application/json

{ 
  "clientToken": "87999da49d44967f272c5.....ad299c81896",	
  "id": "577b92a0fd18ebc164edeb86",
  "customid": "123456789",
  "clientOtp": "475308",
  "pdf_shortUrl": "http://bit.ly/29f7GYd",
  "html_shortUrl": "http://bit.ly/29f74BX"
}
```


Parámetro | Tipo | Descripción
--------- | ---- | -----------
clientToken | String | Token de operación. Necesario para inicializar la librería cliente JS.
id | String | Id de Operación.
customid | String | (Si se especificó) ID Personalizado.
clientOtp | String | (Solo en modo Sandbox) OTP.
pdf_shortUrl | String | Enlace corto al archivo PDF custodiado.
html_shortUrl | String | Enlace corto a la representación HTML del PDF custodiado.


##Consulta de Estado

> Ejemplo de Consulta de Estado

```http
POST /status HTTP/1.1
Content-Type: application/json

{
	"token": "87999da49d44967f272c5.....ad299c81896"
}
```

Para consultar el estado de una operación por token:

POST /status	

Data: Ver Tabla

Parámetro | Tipo | Descripción
--------- | ---- | -----------
token | String | Tóken de Operación


###Response


```http
HTTP/1.1 200 OK
Content-Type: application/json

{ 
	"status": "ESTADO"
}
```

```python

import otpsecure

client = otpsecure.Client('->APIKEY<-','->SECRET<-')

response = client.status('->Token<-')
```

Parámetro | Tipo | Descripción
--------- | ---- | -----------
status | String | Estado de la operación.

Estados posibles: `[INICIADO - ENVIADO - ERROR_ENVIO - OTP_OK - OTP_NOK - FIRMADO - CANCELADO].`


##Custodia PDF 

```http
GET /pdf/:id HTTP/1.1
Content-Type: application/json
```

```http
HTTP/1.1 200 OK
Content-Type: application/pdf
```

`GET /pdf/:id`	Para descargar el archivo PDF custodiado por Id de operación (:id).

##Custodia HTML

```http
GET /html/:id HTTP/1.1
Content_Type: application/json
```

```http
HTTP/1.1 200 OK
Content-Type: text/html
```

`GET /html/:id`	Para visualizar la representación HTML del archivo PDF custodiado por Id de operación (:id).

##Descarga PDF Firmado

```http
GET /signed/:id HTTP/1.1
Content-Type: application/json
```

```http
HTTP/1.1 200 OK
Content-Type: application/pdf
```

`GET /signed/:id`Para descargar el archivo PDF firmado por Id de operación (:id)

##Callback

>Ejemplo uso de URL de callback:

```python
#Change with yours
APIKEY = "0-sP5fAM48_9L2bu_iY5eYnsGAI_VJjl"
SECRET = "SK125w0bYgtoya8JCmgMrCYzBbukeJul"

#Init the Otp Secure Client
client = otpsecure.Client(APIKEY, SECRET)

app = Flask(__name__)

@app.route('/callbackUri', methods = ['POST'])
def api_echo():
	
	if request.method == 'POST'
		
		#Process callback request.
		Callback = client.callback(request)
		print Callback.id
		print Callback.cid
		print Callback.status

		if Callback.status == 'SIGNED'
			with open(Callback.filename, "wb") as fh:
				fh.write(base64.decodestring(Callback.filecontent))

		return render_template('callback.html')

```

```http
POST /**callbackUrl** HTTP/1.1
Content-Type: application/json

{
	"data": {    
		"id": "577b92a0fd18ebc164edeb86",
		"cid": "123456789",
		"status": "INICIADO",
		"filename": "57a7886631d825514167b918_signed.pdf",
		"filecontent": "JVBERi0xLjUKJeLjz9M....jAwNjc0CiUlRU9GCg=="
	}
}


```

Si al solicitar la generación del envío de otp se indica una URL en el parámetro callback el sistema notificará los cambios de estado de la operación a esta URL.

Las notificaciones se realizan por medio de una petición **HTTP POST**, en la que se envía un objeto JSON cifrado con la clave secreta del cliente mediante el esquema AES 256 CBC.

Las librerías SDK cliente proveen un método adecuado para facilitar el desencriptado de esta petición una vez inicializadas.

La estructura del objeto JSON de notificación de cambio de estado contiene los siguientes parámetros:

Propiedad | Descripción
--------- | -----------
id | Identificador único de la operacion OTP secure.
cid | Identificador personalizado de la operación.
status | Estado de la operación. [Ver tabla de estados.](#callback-estados)
filename | Nombre del fichero firmado (solo en estado SIGNED).
filecontent | Contenido del fichero firmado codificado en base64 (solo en estado SIGNED).

##Callback - Estados


Estado | Descripción
------ | -----------
INICIADO | Se ha generado la operación en el sistema y se ha iniciado el proceso de envío de la otp al usuario.
ENVIADO | La pasarela de envío SMS ha recibido la petición de envío de la otp.
ERROR_ENVIO | La pasarela de envío SMS ha devuelto un error en la petición de envío de la otp.
OTP_NOK | El usuario ha introducido una otp incorrecta.
OTP_OK | EL usuario ha introducido una otp correcta u se ha iniciado el procesa de firma electrónica del documento.
SIGNED | El sistema ha finalizado el proceso de firma y devuelve el documento firmado.

#Autenticación HMAC

Cada Cliente tiene dos valores que sirven para identificarle:

* API key: cadena que sirve de identificador de usuario. Es pública y debe incluirse en todas las peticiones que se hagan. Para ello se añadirá como cabecera HTTP con el nombre "api-key".
* Secreto: es una cadena compartida únicamente por el sevidor y el cliente. Cualquiera que conozca este secreto podrá utilizar el API en el nombre del cliente. Se utiliza para firmar el contenido de todas las peticiones.

Para autenticar las peticiones un cliente deberá:

* Incluir el api key en las cabeceras.
* Incluir una cabecera de nombre "Date" con el valor de la fecha actual en milisegundos.
* Calcular el MD5 del contenido de la petición que se va a enviar. El MD5 se calculará en bruto (array de bytes) y se codificará en BASE64.
* Calcular el HMACSHA1 (con el secreto) de la cadena resultante de la concatenación, separado por caracteres de nueva linea  (\n) de:

* Método de la petición (POST,GET...)
* MD5 del contenido de la petición.
* Valor de la cabecera "Date".
* Path al que se está enviando la petición.

* Incluir el HMACSHA1 en BASE64 en una cabecera con nombre hmac.
