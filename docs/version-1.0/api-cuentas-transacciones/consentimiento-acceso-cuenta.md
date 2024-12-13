##**Descripción general**
Un AISP utiliza la API de consentimiento de acceso a la cuenta para solicitar a un ASPSP que cree un nuevo recurso de consentimiento de acceso a la cuenta, recupere el estado del recurso de consentimiento de acceso a la cuenta y elimine el recurso de consentimiento de acceso a la cuenta.

Esta descripción de recursos debe leerse junto con un perfil de API de servicios de información de cuenta compatible.

##**Endpoints**

|  | Recurso | Operación HTTP | Punto Final | ¿Obligatorio? | Alcance | Tipo de subvención | Clave de idempotencia | Parámetros | Objeto de solicitud | Objeto de respuesta |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Consentimientos de acceso a la cuenta | POST | POST /account-access-consents | Si | Scope |  |  |  | OBReadConsent1 | OBReadConsent1 |
| 2 | Consentimientos de acceso a la cuenta | GET | GET/account-access-consents | Si | Scope |  |  |  |  |  |
| 3 | Consentimientos de acceso a la cuenta | DELETE | DELETE/account-access-consents | Si | Scope |  |  |  |  |  |


##**POST /consentimientos de acceso a la cuenta**
La API permite que el AISP solicite a un ASPSP que cree un nuevo recurso de consentimiento de acceso a la cuenta.

* Esta API permite efectivamente que el AISP envíe una copia del consentimiento al ASPSP para autorizar el acceso a la información de la cuenta y las transacciones.
* Un AISP no puede preseleccionar un conjunto de cuentas para la autorización de acceso a cuentas, ya que el comportamiento de las cuentas preseleccionadas, después de la autorización, no está claro desde una perspectiva legal.
* Un ASPSP crea el recurso de consentimiento de acceso a la cuenta y responde con un ConsentId único para hacer referencia al recurso.
Antes de llamar a la API, el AISP debe tener un token de acceso emitido por el ASPSP utilizando una concesión de credenciales de cliente.

####Estado del consentimiento de acceso a la cuenta
La PSU debe autenticarse con el ASPSP y autorizar el consentimiento de acceso a la cuenta para que este se configure correctamente. El recurso de consentimiento de acceso a la cuenta que se crea correctamente debe tener la siguiente enumeración de lista de códigos de estado:

|  | Estado | Descripción del Estado |
|---|---|---|
| 1 | AWAU | El consentimiento de acceso a la cuenta está pendiente de autorización |

Una vez realizada la autorización, el recurso de consentimiento de acceso a la cuenta puede tener los siguientes estados.

|  | Estado | Descripción del Estado |
|---|---|---|
| 1 | RJCT | Se ha rechazado el consentimiento de acceso a la cuenta. |
| 2 | AUTH | El consentimiento de aceso a la cuenta ha sido autorizado exitosamente |


####Flujo de estado
Este es el diagrama de estado para el Estado.

![Flujo Estado](assets/images/flujo_estado_consentimiento.png)

##**GET /account-access-consents/{ConsentId}**
Un AISP puede, opcionalmente, recuperar un recurso de consentimiento de acceso a cuenta que haya creado para verificar su estado.

Antes de llamar a la API, el AISP debe tener un token de acceso emitido por el ASPSP utilizando una concesión de credenciales de cliente.

El uso de este punto final de API estará sujeto a las políticas de uso justo de un ASPSP.

####Estado del consentimiento de acceso a la cuenta
Una vez que la PSU autoriza el recurso de consentimiento de acceso a la cuenta, el estado del recurso de consentimiento de acceso a la cuenta se actualizará con "AUTH".

Las enumeraciones de listas de códigos de estado disponibles para el recurso account-access-consent son:

|  | Estado | Descripción del Estado |
|---|---|---|
| 1 | RJCT | Se ha rechazado el consentimiento de acceso a la cuenta. |
| 2 | AWAU | El consentimiento de acceso a la cuenta está pendiente de autorización |
| 3 | AUTH | El consentimiento de aceso a la cuenta ha sido autorizado exitosamente |
| 4 | CANC | La PSU ha cancelado el acceso otorgado al TPP mediante el panel de acceso de ASPSP, o ASPSP ha revocado el token de acceso o el token ha sido suspendido |
| 5 | EXPD | El consentimiento ha vencido. EL ASPSP marca el consentimiento como "vencido". |

Los cambios en el estado, como por ejemplo ser rechazado, deben capturarse en StatusReason, una matriz de StatusReasonCode, StatusReasonDescriptiony Path.

| Campo | Descripción |
|---|---|
| StatusReasonCode | Especifica el motivo del estado en forma de código. Para obtener una descripción completa, consulte OBExternalStatusReason1Code aquí. |
| StatusReasonDescription | Descripción de por qué se devolvió el código |
| Path | La ruta es opcional pero relevante cuando el motivo del estado se refiere a un objeto/campo y, por lo tanto, es condicional para proporcionar la ruta JSON. |

##**DELETE /account-access-consents/{ConsentId}**
Si el PSU revoca el consentimiento de acceso a los datos con el AISP, el AISP debe eliminar el recurso de consentimiento de acceso a la cuenta con el ASPSP tan pronto como sea prácticamente posible.

* Esto se hace llamando a DELETE (eliminar) el recurso account-access-consent (consentimiento de acceso a la cuenta).
* Antes de llamar a la API, el AISP debe tener un token de acceso emitido por el ASPSP utilizando una concesión de credenciales de cliente.

El TPP también debe borrar del sistema de ASPSP los recursos de Consentimiento de acceso a la cuenta, que son:

* Vencido, es decir, ExpirationDateTime ha caducado, o ExpirationDateTime no ha caducado, pero PSU no desea actualizarlo/volver a autenticarlo, y
* El recurso de consentimiento nunca podrá ser referenciado por el PSU en el dominio AISP o ASPSP.

##**Modelo de datos**

####Consentimientos de acceso a la cuenta - Solicitud
El objeto OBReadConsent1 se utilizará para la llamada a:

* POST /consentimientos de acceso a la cuenta

####**Diagrama UML**

![Diagrama UML](assets/images/diagrama-uml-consentimiento.svg)

**Notas**

* Los campos del objeto OBReadConsent1 se describen en la sección Elementos de consentimiento.
* No se han identificado campos para la sección Riesgo.

**Diccionario de datos**

AQUI VA UNA TABLA


####Consentimientos de acceso a la cuenta - Respuesta
El objeto OBReadConsentResponse1 se utilizará para la llamada a:

* GET /account-access-consents/{ConsentId}
Y respuesta a:

* POST /consentimientos de acceso a la cuenta

####**Diagrama UML**

![Respuesta de consentimiento de lectura de OB1](assets/images/diagrama-uml-consentimiento-respuesta.svg)

**Notas**

* El objeto OBReadConsentResponse1 contiene la misma información que OBReadConsent1, pero con campos adicionales:
ConsentId: 

    * Para identificar de forma única el recurso de consentimiento de acceso a la cuenta.
    * Estado.
    * Estado Razón.
    * Fecha y hora de creación.
    * Fecha y hora de actualización de estado.
    * No se han identificado campos para la sección Riesgo.

**Diccionario de datos**

AQUI VA UNA TABLA

##**Ejemplos de uso**

###**Configurar el consentimiento de acceso a la cuenta: todos los permisos concedidos**

**Solicitud de consentimiento para acceder a la cuenta posterior**


``` header
POST /account-access-consents HTTP/1.1
Authorization: Bearer 2YotnFZFEjr1zCsicMWpAA
x-fapi-auth-date: Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
Accept: application/json
```

``` json
{
  "Data": {
    "Permissions": [
      "ReadAccountsDetail",
      "ReadBalances",
      "ReadBeneficiariesDetail",
      "ReadDirectDebits",
      "ReadProducts",
      "ReadStandingOrdersDetail",
      "ReadTransactionsCredits",
      "ReadTransactionsDebits",
      "ReadTransactionsDetail",
      "ReadOffers",
      "ReadPAN",
      "ReadParty",
      "ReadPartyPSU",
      "ReadScheduledPaymentsDetail",
      "ReadStatementsDetail"
    ],
    "ExpirationDateTime": "2017-05-02T00:00:00+00:00",
    "TransactionFromDateTime": "2017-05-03T00:00:00+00:00",
    "TransactionToDateTime": "2017-12-03T00:00:00+00:00"
  }
}
```

**Respuesta de consentimiento de acceso a cuenta posterior**

``` header
HTTP/1.1 201 Created
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "ConsentId": "urn-alphabank-intent-88379",
    "Status": "AWAU",
    "StatusUpdateDateTime": "2017-05-02T00:00:00+00:00",
    "CreationDateTime": "2017-05-02T00:00:00+00:00",
    "StatusReason": {
      "StatusReasonCode": "U036", 
      "StatusReasonDescription":"Waiting for completion of consent authorisation to be completed by user",
    },
    "Permissions": [
      "ReadAccountsDetail",
      "ReadBalances",
      "ReadBeneficiariesDetail",
      "ReadDirectDebits",
      "ReadProducts",
      "ReadStandingOrdersDetail",
      "ReadTransactionsCredits",
      "ReadTransactionsDebits",
      "ReadTransactionsDetail",
      "ReadOffers",
      "ReadPAN",
      "ReadParty",
      "ReadPartyPSU",
      "ReadScheduledPaymentsDetail",
      "ReadStatementsDetail"
    ],
    "ExpirationDateTime": "2017-08-02T00:00:00+00:00",
    "TransactionFromDateTime": "2017-05-03T00:00:00+00:00",
    "TransactionToDateTime": "2017-12-03T00:00:00+00:00"
  },
  "Risk": {},
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/account-access-consents/urn-alphabank-intent-88379"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```

###**Estado - AWAU**

Este es un ejemplo de una solicitud GET que se realiza antes de que se autorice el recurso de consentimiento de acceso a la cuenta.

**Obtener solicitud de consentimiento para acceder a la cuenta**

``` header
GET /account-access-consents/urn-alphabank-intent-88379 HTTP/1.1
Authorization: Bearer 2YotnFZFEjr1zCsicMWpAA
x-fapi-auth-date: Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Accept: application/json
```

**Obtener respuesta de consentimiento de acceso a la cuenta**
``` header
HTTP/1.1 200 OK
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "ConsentId": "urn-alphabank-intent-88379",
    "Status": "AWAU",
   "StatusReason": {
      "StatusReasonCode": "U036", 
      "StatusReasonDescription":"Waiting for completion of consent authorisation to be completed by user",
    },
    "StatusReasonDescription":"Waiting for completion of consent authorisation to be completed by user",
    "StatusUpdateDateTime": "2017-05-02T00:00:00+00:00",
    "CreationDateTime": "2017-05-02T00:00:00+00:00",
    "Permissions": [
      "ReadAccountsDetail",
      "ReadBalances",
      "ReadBeneficiariesDetail",
      "ReadDirectDebits",
      "ReadProducts",
      "ReadStandingOrdersDetail",
      "ReadTransactionsCredits",
      "ReadTransactionsDebits",
      "ReadTransactionsDetail",
      "ReadOffers",
      "ReadPAN",
      "ReadParty",
      "ReadPartyPSU",
      "ReadScheduledPaymentsDetail",
      "ReadStatementsDetail"
    ],
    "ExpirationDateTime": "2017-08-02T00:00:00+00:00",
    "TransactionFromDateTime": "2017-05-03T00:00:00+00:00",
    "TransactionToDateTime": "2017-12-03T00:00:00+00:00"
  },
  "Risk": {},
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/account-access-consents/urn-alphabank-intent-88379"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```

###**Estado - AUTH**

Este es un ejemplo de una solicitud GET que se realiza después de que se autoriza el recurso de consentimiento de acceso a la cuenta.

####**Obtener solicitud de consentimiento para acceder a la cuenta**

``` header
GET /account-access-consents/urn-alphabank-intent-88379 HTTP/1.1
Authorization: Bearer 2YotnFZFEjr1zCsicMWpAA
x-fapi-auth-date: Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Accept: application/json
```

####**Obtener respuesta de consentimiento de acceso a la cuenta**

``` header
HTTP/1.1 200 OK
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "ConsentId": "urn-alphabank-intent-88379",
    "Status": "AUTH",
    "StatusReason": {
      "StatusReasonCode": "U038", 
      "StatusReasonDescription":"Consent has been completed successfully"
    },
    "StatusUpdateDateTime": "2017-05-02T00:05:00+00:00",
    "CreationDateTime": "2017-05-02T00:00:00+00:00",
    "Permissions": [
      "ReadAccountsDetail",
      "ReadBalances",
      "ReadBeneficiariesDetail",
      "ReadDirectDebits",
      "ReadProducts",
      "ReadStandingOrdersDetail",
      "ReadTransactionsCredits",
      "ReadTransactionsDebits",
      "ReadTransactionsDetail",
      "ReadOffers",
      "ReadPAN",
      "ReadParty",
      "ReadPartyPSU",
      "ReadScheduledPaymentsDetail",
      "ReadStatementsDetail"
    ],
    "ExpirationDateTime": "2017-08-02T00:00:00+00:00",
    "TransactionFromDateTime": "2017-05-03T00:00:00+00:00",
    "TransactionToDateTime": "2017-12-03T00:00:00+00:00"
  },
  "Risk": {},
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/account-access-consents/urn-alphabank-intent-88379"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```
###**Estado - RJCT**

Este es un ejemplo de una solicitud GET que se realiza después de que se haya rechazado el recurso de consentimiento de acceso a la cuenta.

####**Obtener solicitud de consentimiento para acceder a la cuenta**

``` header
GET /account-access-consents/urn-alphabank-intent-88379 HTTP/1.1
Authorization: Bearer 2YotnFZFEjr1zCsicMWpAA
x-fapi-auth-date: Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Accept: application/json
```

####**Obtener respuesta de consentimiento de acceso a la cuenta**

``` header
HTTP/1.1 200 OK
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "ConsentId": "urn-alphabank-intent-88379",
    "Status": "REJT",
    "StatusReason": {
      "StatusReasonCode": "U038", 
      "StatusReasonDescription":"Data.LocalInstrument is invalid.  Expected ExternalLocalInstrument1Code value, received SEPE",
      "Path": "Data.LocalInstrument"
    },
    "StatusUpdateDateTime": "2017-05-02T00:05:00+00:00",
    "CreationDateTime": "2017-05-02T00:00:00+00:00",
    "Permissions": [
      "ReadAccountsDetail",
      "ReadBalances",
      "ReadBeneficiariesDetail",
      "ReadDirectDebits",
      "ReadProducts",
      "ReadStandingOrdersDetail",
      "ReadTransactionsCredits",
      "ReadTransactionsDebits",
      "ReadTransactionsDetail",
      "ReadOffers",
      "ReadPAN",
      "ReadParty",
      "ReadPartyPSU",
      "ReadScheduledPaymentsDetail",
      "ReadStatementsDetail"
    ],
    "ExpirationDateTime": "2017-08-02T00:00:00+00:00",
    "TransactionFromDateTime": "2017-05-03T00:00:00+00:00",
    "TransactionToDateTime": "2017-12-03T00:00:00+00:00"
  },
  "Risk": {},
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/account-access-consents/urn-alphabank-intent-88379"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```

###**Eliminar el consentimiento de acceso a la cuenta**

La llamada DELETE /account-access-consents permite que un AISP elimine un consentimiento de acceso a cuenta creado previamente (ya sea que esté autorizado o no). El PSU puede querer eliminar su consentimiento a través del AISP en lugar de revocar la autorización con el ASPSP.

Esta llamada API permite al PSU revocar el consentimiento con el AISP y que ese consentimiento se refleje en la autorización con el ASPSP.

####**Solicitud de consentimiento para eliminar acceso a la cuenta**

``` header
DELETE /account-access-consents/urn-alphabank-intent-88379 HTTP/1.1
Authorization: Bearer 2YotnFZFEjr1zCsicMWpAA
x-fapi-auth-date:  Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
```

####**Eliminar respuesta de consentimiento de acceso a la cuenta**

``` header
HTTP/1.1 204 No Content
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
```

###**Configurar el consentimiento de acceso a la cuenta con permisos limitados**

####**Solicitud de consentimiento para acceder a la cuenta posterior**

``` header
POST /account-access-consents HTTP/1.1
Authorization: Bearer 2YotnFZFEjr1zCsicMWpAA
x-fapi-auth-date:  Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
Accept: application/json
```

``` json
{
  "Data": {
    "Permissions": [
      "ReadAccountsBasic",
      "ReadBalances"
    ],
    "ExpirationDateTime": "2017-05-02T00:00:00+00:00",
    "TransactionFromDateTime": "2017-05-03T00:00:00+00:00",
    "TransactionToDateTime": "2017-12-03T00:00:00+00:00"
  }
}
```

####**Respuesta de consentimiento de acceso a la cuenta posterior**

``` header
HTTP/1.1 201 Created
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "ConsentId": "urn-alphabank-intent-88379",
    "Status": "AWAU",
    "StatusUpdateDateTime": "2017-05-02T00:00:00+00:00",
    "CreationDateTime": "2017-05-02T00:00:00+00:00",
    "Status": "AWAU",
    "StatusReason": {
      "StatusReasonCode": "U036", 
      "StatusReasonDescription":"Waiting for completion of consent authorisation to be completed by user",
    },
    "Permissions": [
      "ReadAccountsBasic",
      "ReadBalances"
    ],
    "ExpirationDateTime": "2017-08-02T00:00:00+00:00",
    "TransactionFromDateTime": "2017-05-03T00:00:00+00:00",
    "TransactionToDateTime": "2017-12-03T00:00:00+00:00"
  },
  "Risk": {},
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/account-access-consents/urn-alphabank-intent-88379"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```