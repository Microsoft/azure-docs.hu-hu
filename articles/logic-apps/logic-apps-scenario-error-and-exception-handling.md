---
title: Kivételek & hibák naplózása esetén
description: Valós használati eset és forgatókönyv a speciális kivételek kezelésére és a hibák naplózására Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: fdf5f25ae6f89ccc06c95ee1be021691dab0047a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96000351"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Forgatókönyv: Kivételkezelés és hibanaplózás a logikai alkalmazásokhoz kapcsolódóan

Ez a forgatókönyv azt ismerteti, hogyan bővíthető egy logikai alkalmazás a kivételek kezelésének jobb támogatásához. A következő kérdésre adott válasz: "a Azure Logic Apps támogatási kivétel és a hibakezelés?"

> [!NOTE]
> A jelenlegi Azure Logic Apps séma egy szabványos sablont biztosít a műveletekre adott válaszokhoz. Ez a sablon magában foglalja az API-alkalmazásokból érkező belső érvényesítési és hibaüzeneteket is.

## <a name="scenario-and-use-case-overview"></a>Forgatókönyv és használati eset áttekintése

Ebben a forgatókönyvben a következő történetet használjuk: 

Egy jól ismert egészségügyi szervezet olyan Azure-megoldást fejlesztünk, amely a Microsoft Dynamics CRM Online-nal létrehoz egy beteg-portált. A találkozókat a Dynamics CRM Online fekvőbeteg-portál és a Salesforce között kellett elküldenie. A [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) standard használatát kérték az összes beteg rekord esetében.

A projektnek két fő követelménye volt:  

* A Dynamics CRM Online-portálról eljuttatott rekordok naplózásának módszere
* A munkafolyamaton belül bekövetkezett hibák megtekintése

> [!TIP]
> A projekttel kapcsolatos magas szintű videókért lásd: [integrációs felhasználói csoport](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integrációs felhasználói csoport").

## <a name="how-we-solved-the-problem"></a>A probléma megoldása

Úgy döntöttünk, [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") a napló és a hibák rekordjainak tárháza (Cosmos db a rekordokra hivatkozik dokumentumokként). Mivel Azure Logic Apps az összes válaszhoz standard sablonnal rendelkezik, nem kell egyéni sémát létrehoznia. Létrehozhatunk egy API-alkalmazást a hibák és a naplóbejegyzések **beszúrásához** és **lekérdezéséhez** . Az API-alkalmazásokon belüli sémákat is meghatározhatjuk.  

Egy másik követelmény, hogy egy adott dátum után törölheti a rekordokat. Cosmos DB rendelkezik egy [time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Élettartam") (TTL) nevű tulajdonsággal, amely lehetővé tette, hogy az egyes rekordok vagy gyűjtemények esetében az **élettartam értéket állítsa** be. Ez a funkció nem szükséges manuálisan törölni a rekordokat a Cosmos DBban.

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez létre kell hoznia egy Cosmos DB adatbázist és két gyűjteményt (naplózás és hibák).

## <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

Első lépésként hozza létre a logikai alkalmazást, és nyissa meg az alkalmazást a Logic app Designerben. Ebben a példában szülő-gyermek Logic apps-alkalmazásokat használunk. Tegyük fel, hogy már létrehozta a szülőt, és egy alárendelt logikai alkalmazást fog létrehozni.

Mivel a Dynamics CRM Online-ból érkező rekordot naplózni fogjuk, kezdjük a csúcson. A **kérelem** eseményindítóját kell használnia, mert a szülő logikai alkalmazás elindítja ezt a gyermeket.

### <a name="logic-app-trigger"></a>Logikai alkalmazás triggere

Az alábbi példában látható módon egy **kérelem** -triggert használunk:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Lépések

A beteg rekord forrását (kérését) be kell jelentkezni a Dynamics CRM Online-portálról.

1. A Dynamics CRM Online-ból új találkozós rekordot kell beszereznie.

   A CRM-ből érkező trigger a **CRM PatentId**, **bejegyzéstípussal**, **új vagy frissített rekorddal** (új vagy logikai értékkel) és **SalesforceId** rendelkezik. A **SalesforceId** lehet null, mert csak frissítéshez használható.
   A CRM-rekordot a CRM **PatientID** és a bejegyzéstípus használatával szerezjük **be**.

2. Ezután hozzá kell adnia a Azure Cosmos DB SQL API app **InsertLogEntry** műveletet a Logic app Designerben bemutatott módon.

   **Naplóbejegyzés beszúrása**

   ![Képernyőkép a Logic app Designerről, amely a InsertLogEntry konfigurációs beállításait mutatja.](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Hibanapló beszúrása**

   ![Képernyőkép a Logic app Designerről, amely a CreateErrorRecord konfigurációs beállításait mutatja.](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **A rekord létrehozásakor fellépő hiba keresése**

   ![Képernyőkép a CreateErrorRecord képernyőről a Logic app Designerben, amely a hibaüzenetek létrehozására szolgáló mezőket mutatja.](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Logic app-forráskód

> [!NOTE]
> Az alábbi példák csak mintákat tartalmaznak. Mivel ez az oktatóanyag az éles környezetben végrehajtott implementáción alapul, előfordulhat, hogy a **forrás-csomópontok** értéke nem jeleníti meg a találkozó ütemezéséhez kapcsolódó tulajdonságokat. > 

### <a name="logging"></a>Naplózás

A következő logikai alkalmazás kódjának mintája a naplózás kezelését mutatja be.

#### <a name="log-entry"></a>Naplóbejegyzés

Itt látható a logikai alkalmazás forráskódja egy naplóbejegyzés beszúrásához.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Napló kérése

Itt látható az API-alkalmazásba feladott bejelentkezési kérelem üzenete.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Napló válasza

Itt látható a log-válaszüzenet az API-alkalmazásból.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Most nézzük meg a hibakezelés lépéseit.

### <a name="error-handling"></a>Hibakezelés

A következő logikai alkalmazás kódjának mintája bemutatja, hogyan valósítható meg a hibakezelés.

#### <a name="create-error-record"></a>Hiba rekord létrehozása

Itt látható a logikai alkalmazás forráskódja egy hiba rekord létrehozásához.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Hiba beszúrása Cosmos DBba – kérelem

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Hiba beszúrása Cosmos DB--Response

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce-hiba

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>A válasz visszaadása a szülő logikai alkalmazásnak

A válasz beolvasása után visszaadhatja a választ a szülő logikai alkalmazásnak.

#### <a name="return-success-response-to-parent-logic-app"></a>Sikeres válasz küldése a szülő logikai alkalmazásnak

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Hibaüzenet küldése a szülő logikai alkalmazásnak

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB adattár és portál

A megoldás a [Azure Cosmos DBokkal](https://azure.microsoft.com/services/cosmos-db)bővült.

### <a name="error-management-portal"></a>Hiba a felügyeleti portálon

A hibák megtekintéséhez létrehozhat egy MVC-webalkalmazást, amely megjeleníti a hibák rekordjait Cosmos DBból. A **lista**, a **részletek**, a **szerkesztési** és a **törlési** műveletek a jelenlegi verzióban szerepelnek.

> [!NOTE]
> Szerkesztési művelet: Cosmos DB a teljes dokumentumot lecseréli. A **listában** és a **részletes** nézetekben látható rekordok csak minták. Nem az aktuálisan beteg-találkozók rekordjai.

Íme néhány példa a korábban ismertetett megközelítéssel létrehozott MVC-alkalmazás részleteire.

#### <a name="error-management-list"></a>Hiba-felügyeleti lista
![Hibalista](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Hiba kezelése – részletes nézet
![A hiba adatai](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Naplózási felügyeleti portál

A naplók megtekintéséhez létrehozunk egy MVC-webalkalmazást is. Íme néhány példa a korábban ismertetett megközelítéssel létrehozott MVC-alkalmazás részleteire.

#### <a name="sample-log-detail-view"></a>Példa a napló részletes nézetére
![Napló részletes nézete](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API-alkalmazás részletei

#### <a name="logic-apps-exception-management-api"></a>Logic Apps kivételek kezelése API

A nyílt forráskódú Azure Logic Apps kivételek kezelésére szolgáló API-alkalmazás az itt leírtaknak megfelelően működik – két vezérlő áll rendelkezésére:

* A **ErrorController** beszúr egy rekordot (dokumentumot) egy Azure Cosmos db gyűjteménybe.
* **LogController** Naplóbejegyzés (dokumentum) beszúrása egy Azure Cosmos DB gyűjteménybe.

> [!TIP]
> Mindkét vezérlő `async Task<dynamic>` műveleteket használ, lehetővé téve a műveletek futtatását futásidőben, így a művelet törzsében létrehozhatja a Azure Cosmos db sémát. 
> 

Azure Cosmos DB összes dokumentumának egyedi AZONOSÍTÓval kell rendelkeznie. Használunk `PatientId` , és hozzáadunk egy időbélyeget, amely egy Unix timestamp értékre (Double) lett konvertálva. Lerövidítjük az értéket a tört érték eltávolításához.

A hiba-vezérlő API forráskódját a [githubról](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs)tekintheti meg.

Az API-t egy logikai alkalmazásból hívjuk a következő szintaxis használatával:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Az előző kódban szereplő kifejezés a *Create_NewPatientRecord* **sikertelen** állapotra vonatkozó ellenőrzéseit ellenőrzi.

## <a name="summary"></a>Összefoglalás

* Egy logikai alkalmazásban egyszerűen megvalósítható a naplózás és a hibakezelés.
* A naplózási és a hibajelentési (dokumentumok) adattárházként Azure Cosmos DB is használhatja.
* Az MVC használatával létrehozhat egy portált a naplók és a hibajelentések megjelenítéséhez.

### <a name="source-code"></a>Forráskód

A Logic Apps kivételek kezelése API-alkalmazás forráskódja ebben a GitHub- [tárházban](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Logic app-kivételek kezelési API-ját")érhető el.

## <a name="next-steps"></a>Következő lépések

* [További példák és forgatókönyvek a Logic app szolgáltatásban](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Logikai alkalmazások figyelése](../logic-apps/monitor-logic-apps.md)
* [A logikai alkalmazás üzemelő példányának automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
