##**Descripción general**
Un AISP utiliza el recurso de cuentas para recuperar la lista completa de cuentas e información de cuentas a las que el PSU ha autorizado el acceso.

Esta descripción de recursos debe leerse junto con un perfil de API de servicios de información de cuenta compatible.

##**Endpoints**

Puntos finales para el recurso y métodos disponibles.

|  | Recurso | Operación HTTP | Punto final | ¿Obligatorio? | Scope | Tipo de Subvención | Clave de idempotencia | Parámetros | Objeto de solicitud | Objeto de respuesta |
|---|---|---|---|---|---|---|---|---|---|---|
| 1 | Cuentas | GET | GET /accounts | Si | accounts | Código de autorización | No | Paginación |  | OBReadAccount6 |
| 2 | Cuentas | GET | GET /accounts/{AccountId} | Si | accounts | Código de autorización | No |  |  | OBReadAccount6 |


###**GET /accounts**
El primer paso para un AISP después de que se autoriza una solicitud de cuenta es llamar al punto final GET /accounts.

Se le entregará al AISP la lista completa de cuentas (los AccountId) a las que el PSU ha autorizado el acceso del AISP. Los AccountId devueltos pueden usarse para recuperar otros recursos para un AccountId específico. La selección de cuentas autorizadas ocurre únicamente en la interfaz del ASPSP.

###**GET /accounts/{AccountId}**
Un AISP puede recuperar los recursos de información de la cuenta para AccountId (que se recupera en la llamada a GET /accounts).

##**Modelo de datos**
El objeto OBReadAccount6 ​​se utilizará para la llamada a:

* GET /accounts/{AccountId}
* GET /accounts

###**Definición de recurso**

Este recurso representa la cuenta en la que se realizan las entradas de crédito y débito. Cada recurso de cuenta tendrá un AccountId único e inmutable.

###**Diagrama UML**

AQUI VA UNA IMAGEN

###**Notas**
* La estructura de **Cuentas** y **Servicios** ha sido diseñada para:
    * Reflejar las estructuras DebtorAccount y DebtorAgent (y de manera similar para CreditorAccount y CreditorAgent) en el caso de uso de PISP.
    * Tener un SchemeName para los bloques Cuenta y Proveedor de servicios significa que podemos ser flexibles para acomodar múltiples tipos de cuentas.
* Para esquemas de identificación nacionales comunes en el Reino Unido:
    * Cuenta/Cuenta
        * Cuando se especifica "UK.OBIE.SortCodeAccountNumber" como SchemeName, el campo de Identificación debe completarse con el Código de clasificación de 6 dígitos y el Número de cuenta de 8 dígitos (un campo de 14 dígitos).
        * Cuando se especifica "UK.OBIE.IBAN" como nombre del esquema, el campo de identificación debe completarse con el IBAN completo.
        * Cuando se especifica "UK.OBIE.PAN" como SchemeName, el campo de identificación debe completarse con el PAN principal vinculado a la cuenta. Un ASPSP puede optar por enmascarar los dígitos devueltos en el campo de identificación.
    * Cuenta/Administrador de servicios
        * Cuando se completa "UK.OBIE.BICFI" como nombre del esquema, el campo de identificación debe completarse con el BIC.
* El campo SecondaryIdentification se utiliza para identificar una cuenta además del campo principal Account/Identification. El campo SecondaryIdentification puede completarse con un número de registro para las sociedades de crédito hipotecario o con un código de moneda cuando una cuenta tiene múltiples subcuentas en distintas monedas.
* El SwitchStatuscampo se utiliza para indicar que una cuenta está experimentando un cambio de cuenta.
    * Un ASPSP puede completar este campo con el valor UK.CASS.NotSwitchedpara indicar que la cuenta no ha sido cambiada.
    * Una vez que se ha completado un cambio de cuenta, un ASPSP puede completar este campo con el valorUK.CASS.SwitchCompleted
* Los siguientes campos son opcionales solo para las cuentas que se han cambiado. Para todas las demás cuentas, el ASPSP debe completar los campos.
    * Datos.Moneda
    * Datos.Categoría de cuenta
    * Datos.Código de tipo de cuenta

###**Códigos de permiso**

El recurso varía según los permisos (ReadAccountsBasic y ReadAccountsDetail) utilizados para acceder al recurso. En caso de que se acceda al recurso tanto con ReadAccountsBasic como con ReadAccountsDetail, se debe utilizar el nivel más detallado (ReadAccountsDetail).

* Estos objetos no deben devolverse sin el permiso ReadAccountsDetail :
    * OBReadAccount6/Datos/Cuenta/Cuenta
    * OBReadAccount6/Datos/Cuenta/Administrador de servicios
    * OBReadAccount6/Datos/Cuenta/Frecuencia y formato de extracto
*Si el PSU concede ReadAccountsDetail :
    * **Se debe** devolver OBReadAccount6/Data/Account/Account (1..*)
    * **Se puede** devolver OBReadAccount6/Data/Account/Servicer si corresponde a la cuenta y ASPSP (0..1)
    * OBReadAccount6/Data/Account/StatementFrequencyAndFormat puede devolverse (0..*)

Si la PSU concede el permiso ReadPAN, el ASPSP puede optar por completar OBReadAccount6/Data/Account/Account/Identification con el PAN sin máscara (si el PAN se completa en la respuesta).

###**Diccionario de datos**

AQUI VA EL DICCIONARIO DE DATOS.	

###**Clases reutilizadas**

**Dirección postal OB7**

La clase OBPostalAddress7 se define en la página payment-initiation-api-profile

##**Ejemplos de uso**

###**Permiso de detalle a granel**

La llamada a GET /accounts es el primer paso después de que se autoriza una solicitud de cuenta. Esto permitirá que el AISP descubra qué cuentas (y valores de AccountId) están asociadas con la autorización del consentimiento. En este escenario, AccountId 22289 tiene un número de registro de una sociedad de crédito hipotecario y AccountId 31820 no lo tiene. Se ha otorgado el permiso **ReadAccountsDetail**.

####**Solicitud de obtención de cuentas**
``` header
GET /accounts HTTP/1.1
Authorization: Bearer Az90SAOJklae
x-fapi-auth-date: Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Accept: application/json
```

####**Obtener respuesta de cuentas**
``` header
HTTP/1.1 200 OK
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "Account": [
      {
        "AccountId": "22289",
        "Status": "Enabled",
        "MaturityDate": "2019-01-01T06:06:06+00:00",
        "StatusUpdateDateTime": "2019-01-01T06:06:06+00:00",
        "Currency": "GBP",
        "AccountCategory": "Personal",
        "AccountTypeCode": "CACC",
        "Description" :  "For paying bills",
        "Nickname": "Bills",
        "OpeningDate": "01-05-2002",
        "SwitchStatus": "UK.CASS.NotSwitched",
        "StatementFrequencyAndFormat": [
          {
            "Frequency": "YEAR",
            "CommunicationMethod": "EMAL",
            "Format": "DPDF",
            "DeliveryAddress": {
              "AddressType": "HOME",
              "StreetName": "Bank Street",
              "BuildingNumber": "11",
              "Floor": "6",
              "PostCode": "Z78 4TY",
              "TownName": "London",
              "Country": "UK"
            }
          }
        ],
        "Servicer": {
          "SchemeName": "UK.OBIE.BICFI",
          "Identification": "8020441910203345",
          "Name": "ServicerName"
        },
        "Account": [
          {
            "SchemeName": "UK.OBIE.SortCodeAccountNumber",
            "Identification": "80200110203345",
            "Name": "Mr Kevin",
            "SecondaryIdentification": "00021",
            "LEI": "9193001QZMP2PQT4AK86"
          }
        ]
      },
      {
        "AccountId": "31820",
        "Status": "Enabled",
        "StatusUpdateDateTime": "2018-01-01T06:06:06+00:00",
        "Currency": "GBP",
        "AccountCategory": "Personal",
        "AccountTypeCode": "CACC",
        "Nickname": "Household",
        "Account": [
          {
            "SchemeName": "UK.OBIE.SortCodeAccountNumber",
            "Identification": "80200110203348",
            "Name": "Mr Kevin"
          }
        ]
      }
    ]
  },
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/accounts/"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```

###**Cuenta específica - Permiso detallado**

Un AISP también puede recuperar los detalles del recurso de cuenta específicamente para AccountId 22289. Se ha otorgado el permiso **ReadAccountsDetail**.

####**Solicitud de obtención de cuentas**

``` header
GET /accounts/22289 HTTP/1.1
Authorization: Bearer Az90SAOJklae
x-fapi-auth-date: Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Accept: application/json
```

####**Obtener respuesta de cuentas**

``` header
HTTP/1.1 200 OK
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "Account": [
      {
        "AccountId": "22289",
        "Status": "Enabled",
        "MaturityDate": "2019-01-01T06:06:06+00:00",
        "StatusUpdateDateTime": "2019-01-01T06:06:06+00:00",
        "Currency": "GBP",
        "AccountCategory": "Personal",
        "Description" :  "For paying bills",
        "AccountTypeCode": "CACC",
        "Nickname": "Bills",
        "OpeningDate": "01-05-2002",
        "SwitchStatus": "UK.CASS.NotSwitched",
        "StatementFrequencyAndFormat": [
          {
            "Frequency": "YEAR",
            "CommunicationMethod": "EMAL",
            "Format": "DPDF",
            "DeliveryAddress": {
              "AddressType": "BIZZ",
              "StreetName": "Bank Street",
              "BuildingNumber": "11",
              "Floor": "6",
              "PostCode": "Z78 4TY",
              "TownName": "London",
              "Country": "UK"
            }
          }
        ],
        "Servicer": {
          "SchemeName": "UK.OBIE.BICFI",
          "Identification": "8020441910203345",
          "Name": "ServicerName"
        },
        "Account": [
          {
            "SchemeName": "UK.OBIE.SortCodeAccountNumber",
            "Identification": "80200110203345",
            "Name": "Mr Kevin",
            "SecondaryIdentification": "00021",
            "LEI": "9193001QZMP2PQT4AK86"
          }
        ]
      }
    ]
  },
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/accounts/22289"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```

###**Permiso básico a granel**

Se ha concedido el permiso **ReadAccountsBasic**.

####**Solicitud de obtención de cuentas**

``` header
GET /accounts HTTP/1.1
Authorization: Bearer Az90SAOJklae
x-fapi-auth-date:  Sun, 10 Sep 2017 19:43:31 GMT
x-fapi-customer-ip-address: 104.25.212.99
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Accept: application/json
```

####**Obtener respuesta de cuentas**

``` header
HTTP/1.1 200 OK
x-fapi-interaction-id: 93bac548-d2de-4546-b106-880a5018460d
Content-Type: application/json
```

``` json
{
  "Data": {
    "Account": [
      {
        "AccountId": "22289",
        "Status": "Enabled",
        "MaturityDate": "2019-01-01T06:06:06+00:00",
        "StatusUpdateDateTime": "2019-01-01T06:06:06+00:00",
        "Currency": "GBP",
        "AccountCategory": "Personal",
        "AccountTypeCode": "CACC",
        "Description" :  "For paying bills",
        "Nickname": "Bills",
        "OpeningDate": "01-05-2002",
        "SwitchStatus": "UK.CASS.NotSwitched",
        "StatementFrequencyAndFormat": [
          {
            "Frequency": "YEAR",
            "CommunicationMethod": "EMAL",
            "Format": "DPDF",
            "DeliveryAddress": {
              "AddressType": "HOME",
              "StreetName": "Bank Street",
              "BuildingNumber": "11",
              "Floor": "6",
              "PostCode": "Z78 4TY",
              "TownName": "London",
              "Country": "UK"
            }
          }
        ],
        "Servicer": {
          "SchemeName": "UK.OBIE.BICFI",
          "Identification": "8020441910203345",
          "Name": "ServicerName"
        },
        "Account": [
          {
            "SchemeName": "UK.OBIE.SortCodeAccountNumber",
            "Identification": "80200110203345",
            "Name": "Mr Kevin",
            "SecondaryIdentification": "00021",
            "LEI": "9193001QZMP2PQT4AK86"
          }
        ]
      },
      {
        "AccountId": "31820",
        "Status": "Enabled",
        "StatusUpdateDateTime": "2018-01-01T06:06:06+00:00",
        "Currency": "GBP",
        "AccountCategory": "Personal",
        "AccountTypeCode": "CACC",
        "Nickname": "Household"
      }
    ]
  },
  "Links": {
    "Self": "https://api.alphabank.com/open-banking/v4.0/aisp/accounts/"
  },
  "Meta": {
    "TotalPages": 1
  }
}
```