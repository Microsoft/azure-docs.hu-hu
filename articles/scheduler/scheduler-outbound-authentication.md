---
title: Kimenő hitelesítés
description: Ismerje meg, hogyan állíthatja be vagy távolíthatja el az Azure Scheduler kimenő hitelesítését
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 0a8d79af9f45731971cb1be1f39fc193f9d0f0d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80878969"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Kimenő hitelesítés az Azure Schedulerben

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Az ütemező már nem érhető el a Azure Portalban, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagjai](scheduler-powershell-reference.md) jelenleg is elérhetők maradnak, így a feladatok és a feladatok gyűjteményei kezelhetők.

Előfordulhat, hogy az Azure Scheduler-feladatoknak olyan szolgáltatásokat kell meghívniuk, amelyek hitelesítést igényelnek, például más Azure-szolgáltatásokat, Salesforce.com, Facebookot és biztonságos egyéni webhelyeket. A hívott szolgáltatás eldöntheti, hogy az ütemező feladata hozzáférhet-e a kért erőforrásokhoz. 

A Scheduler a következő hitelesítési modelleket támogatja: 

* *Ügyféltanúsítvány* -alapú hitelesítés SSL-/TLS-Ügyféltanúsítványok használatakor
* *Alapszintű* hitelesítés
* *Active Directory OAuth* -hitelesítés

## <a name="add-or-remove-authentication"></a>Hitelesítés hozzáadása vagy eltávolítása

* Ha egy ütemező feladatokhoz szeretne hitelesítést felvenni, a feladatok létrehozásakor vagy frissítésekor adja hozzá a `authentication` JavaScript Object Notation (JSON) gyermek elemet a `request` elemhez. 

  A válaszok soha nem adnak vissza az ütemező szolgáltatásnak átadott titkos kulcsokat egy PUT, PATCH vagy POST kéréssel az `authentication` objektumban. 
  A válaszok NULL értékűre állítanak titkos adatokat, vagy a hitelesített entitást képviselő nyilvános jogkivonatot is használhatnak. 

* Ha el szeretné távolítani a hitelesítést egy ütemező feladatokból, explicit módon futtasson egy PUT vagy PATCH kérelmet a feladatokon, és állítsa az `authentication` objektumot NULL értékre. A válasz nem tartalmaz hitelesítési tulajdonságokat.

## <a name="client-certificate"></a>Ügyféltanúsítvány

### <a name="request-body---client-certificate"></a>Kérelem törzse – ügyféltanúsítvány

Ha a modell használatával ad hozzá hitelesítést `ClientCertificate` , adja meg ezeket a további elemeket a kérelem törzsében.  

| Elem | Kötelező | Leírás |
|---------|----------|-------------|
| **hitelesítés** (szülő elem) | SSL/TLS-ügyféltanúsítvány használatára szolgáló hitelesítési objektum |
| **típusa** | Yes | A hitelesítési típus. Az SSL/TLS-Ügyféltanúsítványok esetében az érték a következő: `ClientCertificate` . |
| **pfx** | Yes | A PFX-fájl Base64 kódolású tartalma |
| **alaphelyzetbe állítása** | Yes | A PFX-fájl eléréséhez használt jelszó |
||| 

### <a name="response-body---client-certificate"></a>Válasz törzse – ügyféltanúsítvány 

Ha a rendszer hitelesítési adatokkal küldi el a kérelmet, a válasz tartalmazza ezeket a hitelesítési elemeket.

| Elem | Leírás | 
|---------|-------------| 
| **hitelesítés** (szülő elem) | SSL/TLS-ügyféltanúsítvány használatára szolgáló hitelesítési objektum |
| **típusa** | A hitelesítési típus. Az SSL/TLS-Ügyféltanúsítványok esetében az érték a következő: `ClientCertificate` . |
| **certificateThumbprint** |A Tanúsítvány ujjlenyomata |
| **certificateSubjectName** |A tanúsítvány tulajdonosának megkülönböztető neve |
| **certificateExpiration** | A tanúsítvány lejárati dátuma |
||| 

### <a name="sample-rest-request---client-certificate"></a>Példa REST-kérelemre – ügyféltanúsítvány

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Példa REST-válaszra – ügyféltanúsítvány

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Alapszintű

### <a name="request-body---basic"></a>Kérelem törzse – alapszintű

Ha a modell használatával ad hozzá hitelesítést `Basic` , adja meg ezeket a további elemeket a kérelem törzsében.

| Elem | Kötelező | Leírás |
|---------|----------|-------------|
| **hitelesítés** (szülő elem) | Az alapszintű hitelesítés használatára szolgáló hitelesítési objektum | 
| **típusa** | Yes | A hitelesítési típus. Az alapszintű hitelesítés esetében az érték a következő: `Basic` . | 
| **username** | Yes | A hitelesíteni kívánt Felhasználónév | 
| **alaphelyzetbe állítása** | Yes | A hitelesíteni kívánt jelszó |
|||| 

### <a name="response-body---basic"></a>Válasz törzse – alapszintű

Ha a rendszer hitelesítési adatokkal küldi el a kérelmet, a válasz tartalmazza ezeket a hitelesítési elemeket.

| Elem | Leírás | 
|---------|-------------|
| **hitelesítés** (szülő elem) | Az alapszintű hitelesítés használatára szolgáló hitelesítési objektum |
| **típusa** | A hitelesítési típus. Az alapszintű hitelesítés esetében az érték a következő: `Basic` . |
| **username** | A hitelesített Felhasználónév |
||| 

### <a name="sample-rest-request---basic"></a>Példa REST-kérelemre – alapszintű

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Minta REST-válasz – alapszintű

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Kérelem törzse – Active Directory OAuth 

Ha a modell használatával ad hozzá hitelesítést `ActiveDirectoryOAuth` , adja meg ezeket a további elemeket a kérelem törzsében.

| Elem | Kötelező | Leírás |
|---------|----------|-------------|
| **hitelesítés** (szülő elem) | Yes | A ActiveDirectoryOAuth-hitelesítés használatára szolgáló hitelesítési objektum |
| **típusa** | Yes | A hitelesítési típus. A ActiveDirectoryOAuth-hitelesítés esetében az érték a következő: `ActiveDirectoryOAuth` . |
| **bérlő** | Yes | Az Azure AD-bérlő bérlői azonosítója. Az Azure AD-bérlő bérlői azonosítójának megkereséséhez futtassa a `Get-AzureAccount` következőt: Azure PowerShell. |
| **célközönség** | Yes | Ez az érték a következőre van beállítva: `https://management.core.windows.net/` . | 
| **clientId** | Yes | Az Azure AD-alkalmazás ügyfél-azonosítója | 
| **titkos kód** | Yes | A jogkivonatot kérő ügyfél titka | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Válasz törzse – Active Directory OAuth

Ha a rendszer hitelesítési adatokkal küldi el a kérelmet, a válasz tartalmazza ezeket a hitelesítési elemeket.

| Elem | Leírás |
|---------|-------------|
| **hitelesítés** (szülő elem) | A ActiveDirectoryOAuth-hitelesítés használatára szolgáló hitelesítési objektum |
| **típusa** | A hitelesítési típus. A ActiveDirectoryOAuth-hitelesítés esetében az érték a következő: `ActiveDirectoryOAuth` . | 
| **bérlő** | Az Azure AD-bérlő bérlői azonosítója |
| **célközönség** | Ez az érték a következőre van beállítva: `https://management.core.windows.net/` . |
| **clientId** | Az Azure AD-alkalmazás ügyfél-azonosítója |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Példa REST-kérelemre – Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Példa REST-válaszra – Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="next-steps"></a>Következő lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)