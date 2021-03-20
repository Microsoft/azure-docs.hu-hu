---
title: REST API-Azure Event Grid IoT Edge | Microsoft Docs
description: REST API on Event Grid on IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171754"
---
# <a name="rest-api"></a>REST API
Ez a cikk a Azure Event Grid REST API-jait ismerteti IoT Edge

## <a name="common-api-behavior"></a>Általános API-viselkedés

### <a name="base-url"></a>Kiindulási URL-cím
A Event Grid on IoT Edge a következő API-kat teszi elérhetővé a HTTP-n (5888-as porton) és a HTTPS-en (4438-es port

* HTTP-alap URL-cím: http://eventgridmodule:5888
* HTTPS alap URL-címe: https://eventgridmodule:4438

### <a name="request-query-string"></a>Lekérdezési karakterlánc kérése
Minden API-kérelemhez a következő lekérdezési karakterlánc paraméter szükséges:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Kérelem tartalmának típusa
Minden API-kérelemnek tartalmaznia kell egy **Content-Type értéket**.

**EventGridSchema** vagy **CustomSchema** esetén a Content-Type érték a következő értékek egyike lehet:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

Strukturált módban lévő **CloudEventSchemaV1_0** esetén a Content-Type érték a következő értékek egyike lehet:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

Bináris módban való **CloudEventSchemaV1_0** esetén a részletekért tekintse meg a [dokumentációt](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) .

### <a name="error-response"></a>Hiba válasza
Minden API hibát ad vissza a következő adattartalommal:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Témakörök kezelése

### <a name="put-topic-create--update"></a>Téma létrehozása (létrehozás/frissítés)

**Kérelem**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Hasznos** adatok:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Válasz**: http 200

**Hasznos** adatok:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Témakör lekérése

**Kérelem**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Válasz**: http 200

**Hasznos** adatok:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Összes téma beolvasása

**Kérelem**: ``` GET /topics?api-version=2019-01-01-preview ```

**Válasz**: http 200

**Hasznos** adatok:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Témakör törlése

**Kérelem**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Válasz**: http 200, üres hasznos adat

## <a name="manage-event-subscriptions"></a>Esemény-előfizetések kezelése
Az ebben a szakaszban szereplő minták a következőt használják: `EndpointType=Webhook;` . A JSON-minták a `EndpointType=EdgeHub / EndpointType=EventGrid` következő szakaszban találhatók. 

### <a name="put-event-subscription-create--update"></a>Esemény-előfizetés elhelyezése (létrehozás/frissítés)

**Kérelem**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Hasznos** adatok:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Válasz**: http 200

**Hasznos** adatok:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Esemény-előfizetés beolvasása

**Kérelem**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Válasz**: http 200

**Hasznos** adatok:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Esemény-előfizetések beolvasása

**Kérelem**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Válasz**: http 200

**Hasznos** adatok:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Esemény-előfizetés törlése

**Kérelem**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Válasz**: http 200, nincs hasznos adat


## <a name="publish-events-api"></a>Események közzététele API

### <a name="send-batch-of-events-in-event-grid-schema"></a>Események kötegének elküldése (Event Grid sémában)

**Kérelem**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Válasz**: http 200, üres hasznos adat


**Adattartalom mező leírása**
- ```Id``` kötelező. A hívó által feltöltött bármely karakterláncérték lehet. Event Grid nem végez ismétlődő észlelést, vagy nem kényszeríti ki a szemantikai műveleteket ezen a területen.
- ```Topic``` nem kötelező, de ha meg kell egyeznie a kérelem URL-címével topic_name
- ```Subject``` kötelező, bármilyen sztring lehet.
- ```EventType``` kötelező, bármilyen sztring lehet.
- ```EventTime``` kötelező, nem érvényesítve, de megfelelő DateTime értéknek kell lennie.
- ```DataVersion``` kötelező
- ```MetadataVersion``` megadása nem kötelező, ha meg van adva, akkor az értéket tartalmazó sztringnek kell lennie ```"1"```
- ```Data``` nem kötelező, és bármely JSON-token lehet (szám, karakterlánc, logikai, tömb, objektum)

### <a name="send-batch-of-events-in-custom-schema"></a>Események kötegének elküldése (egyéni sémában)

**Kérelem**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Válasz**: http 200, üres hasznos adat


**Hasznos adatokra vonatkozó korlátozások**
- Események tömbje kell, hogy legyen.
- Minden tömb-bejegyzésnek JSON-objektumnak kell lennie.
- Nincs más korlátozás (a hasznos adatok méretétől eltérő).

## <a name="examples"></a>Példák

### <a name="set-up-topic-with-eventgrid-schema"></a>Témakör beállítása a EventGrid-sémával
Egy olyan témakört állít be, amely az események **eventgridschema** való közzétételét igényli.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Témakör beállítása egyéni sémával
Egy olyan témakört állít be, amely az események közzétételét kéri a alkalmazásban `customschema` .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Témakör beállítása a Cloud Event Schema szolgáltatással
Egy olyan témakört állít be, amely az események közzétételét kéri a alkalmazásban `cloudeventschema` .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Webhook beállítása célként, a eventgridschema-ben küldendő események
Ennek a célhelynek a használatával minden más modulba (amely egy HTTP-végpontot) vagy a hálózaton/interneten található bármely HTTP-címezhető végpontba küldheti az eseményeket.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Megkötések az `endpointUrl` attribútumon:
- Nem null értékűnek kell lennie.
- Abszolút URL-címnek kell lennie.
- Ha outbound__webhook__httpsOnly értéke TRUE (igaz) értékre van állítva a EventGridModule-beállításokban, csak HTTPS-nek kell lennie.
- Ha outbound__webhook__httpsOnly hamis értékre van állítva, akkor HTTP vagy HTTPS lehet.

A tulajdonságra vonatkozó megkötések `eventDeliverySchema` :
- Meg kell egyeznie a feliratkozási témakör bemeneti sémájával.
- Ez lehet null értékű. Alapértelmezés szerint a témakör bemeneti sémája.

### <a name="set-up-iot-edge-as-destination"></a>IoT Edge beállítása célként

Ezen a célhelyen küldhet eseményeket IoT Edge hubhoz, és az Edge hub útválasztási/szűrési/továbbítási alrendszerének kell alávetni.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Event Grid felhő beállítása célként

Ezen a célhelyen küldhet eseményeket a felhőbe (Azure) Event Gridba. Először létre kell hoznia egy felhasználói témakört a felhőben, amelybe az eseményeket el kell juttatni, mielőtt a peremhálózati esemény-előfizetést hozna létre.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Nem null értékűnek kell lennie.
- Abszolút URL-címnek kell lennie.
- Az elérési utat meg kell `/api/events` adni a kérelem URL-címének elérési útjában.
- `api-version=2018-01-01`A lekérdezési karakterláncban kell szerepelnie.
- Ha outbound__eventgrid__httpsOnly True értékre van állítva a EventGridModule-beállításokban (alapértelmezés szerint igaz), csak HTTPS-ként kell megadni.
- Ha outbound__eventgrid__httpsOnly hamis értékre van állítva, akkor HTTP vagy HTTPS lehet.
- Ha a outbound__eventgrid__allowInvalidHostnames hamis értékre van állítva (alapértelmezés szerint False), akkor a következő végpontok egyikét kell megcéloznia:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Nem null értékűnek kell lennie.

TopicName:
- Ha az előfizetés. EventDeliverySchema értéke EventGridSchema, akkor az ebben a mezőben szereplő érték minden esemény témakör-mezőjébe kerül, mielőtt a felhőben Event Grid.
- Ha az előfizetés. EventDeliverySchema értéke CustomEventSchema, akkor a rendszer figyelmen kívül hagyja ezt a tulajdonságot, és az egyéni esemény adattartalmát pontosan úgy továbbítja a rendszer, ahogy az érkezett.

## <a name="set-up-event-hubs-as-a-destination"></a>Event Hubs beállítása célként

Az Event hub-ban való közzétételhez állítsa be a következőt `endpointType` `eventHub` :

* connectionString: az adott esemény központhoz tartozó kapcsolati karakterlánc, amelyet egy megosztott elérési házirend használatával generált.

    >[!NOTE]
    > A kapcsolatok karakterláncának entitás-specifikusnak kell lennie. A névtér-kapcsolatok karakterláncának használata nem fog működni. Létrehozhat egy entitás-specifikus kapcsolati karakterláncot úgy, hogy az Azure Portalon közzétenni kívánt esemény hubhoz navigál, és a **megosztott hozzáférési házirendek** elemre kattint egy új entitás-specifikus connecection-karakterlánc létrehozásához.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Service Bus várólisták beállítása célhelyként

Service Bus üzenetsor közzétételéhez állítsa be a következőt `endpointType` `serviceBusQueue` :

* connectionString: a megadott Service Bus-várólista kapcsolati karakterlánca, amelyet egy megosztott elérési házirend használatával generált.

    >[!NOTE]
    > A kapcsolatok karakterláncának entitás-specifikusnak kell lennie. A névtér-kapcsolatok karakterláncának használata nem fog működni. Hozzon elő egy entitás-specifikus kapcsolati karakterláncot úgy, hogy az Azure Portalon közzétenni kívánt Service Bus várólistára navigál, és a **megosztott hozzáférési házirendek** elemre kattintva létrehoz egy új entitás-specifikus connecection karakterláncot.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Service Bus témakörök beállítása célhelyként

Service Bus témakör közzétételéhez állítsa be a következőt `endpointType` `serviceBusTopic` :

* connectionString: a megadott Service Bus témakör kapcsolati karakterlánca, amelyet egy megosztott elérési házirend használatával generált.

    >[!NOTE]
    > A kapcsolatok karakterláncának entitás-specifikusnak kell lennie. A névtér-kapcsolatok karakterláncának használata nem fog működni. Hozzon elő egy entitás-specifikus kapcsolati karakterláncot, és navigáljon az Azure Portalon közzétenni kívánt Service Bus témakörre, majd kattintson a **megosztott hozzáférési házirendek** elemre egy új entitás-specifikus connecection-karakterlánc létrehozásához.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Tárolási várólisták beállítása célhelyként

A Storage-üzenetsor közzétételéhez állítsa be a következőt  `endpointType` `storageQueue` :

* queueName: az a tárolási várólista neve, amelyet közzé szeretne tennie.
* connectionString: a Storage-fiókhoz tartozó kapcsolati sztring a tárolási üzenetsor.

    >[!NOTE]
    > A Event Hubs, Service Bus a várólisták és a Service Bus témakörök nem használhatók, mert a tárolási várólistákhoz használt kapcsolatok karakterlánca nem entitás-specifikus. Ehelyett a Storage-fiókhoz tartozó kapcsolatok karakterláncát kell megadnia.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```