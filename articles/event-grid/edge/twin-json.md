---
title: Modul Twin – Azure Event Grid IoT Edge | Microsoft Docs
description: Konfigurálás külön modulon keresztül.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f39d22fe58d4375b3b68bacd237c1b200328c4b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171329"
---
# <a name="module-twin-json-schema-azure-event-grid"></a>Modul Twin JSON-sémája (Azure Event Grid)

A Event Grid on IoT Edge integrálódik a IoT Edge ökoszisztémával, és támogatja a témakörök és előfizetések létrehozását a Twin modulon keresztül. Azt is jelenti, hogy az összes témakör és esemény-előfizetés aktuális állapota a (z) Twin modul jelentett tulajdonságaira mutat.

> [!WARNING]
> A IoT Edge ökoszisztémájának korlátai miatt a következő JSON-példa összes tömb eleme JSON-karakterláncként lett kódolva. `EventSubscription.Filter.EventTypes` `EventSubscription.Filter.AdvancedFilters` A következő példában a és a kulcsok láthatók.

## <a name="desired-properties-json"></a>Kívánt tulajdonságok JSON

* A témakörök szakaszban szereplő egyes kulcs-érték párok értéke pontosan ugyanazt a JSON-sémát tartalmazza, amelyet az API-hoz használ a `Topic.Properties` témakörök létrehozásakor.
* A **EventSubscriptions** szakaszban szereplő egyes kulcs-érték párok értéke pontosan ugyanazt a JSON-sémát tartalmazza, amelyet az API-hoz használ a `EventSubscription.Properties` témakörök létrehozásakor.
* Egy témakör törléséhez állítsa az értékét a `null` kívánt tulajdonságokra.
* Az esemény-előfizetések a kívánt tulajdonságok használatával történő törlése nem támogatott.

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    }
}
```

## <a name="reported-properties-json"></a>Jelentett tulajdonságok JSON

A modul következő részében található jelentett tulajdonságok szakasz az alábbi információkat tartalmazza:

* A modul tárolójában található témakörök és előfizetések készlete
* A kívánt témakörök/esemény-előfizetések létrehozásakor előforduló hibák
* Minden rendszerindítási hiba (például a kívánt tulajdonságok JSON-elemzése sikertelen volt)

```json
{
    "topics": {
        "twinTopic1": {},
        "twinTopic2": {
            "inputSchema": "customEventSchema"
        }
    },
    "eventSubscriptions": {
        "twinTopic1WebhookSub": {
            "topic": "twinTopic1",
            "retryPolicy": {
                "eventExpiryInMinutes": 120,
                "maxDeliveryAttempts": 30
            },
            "destination": {
                "endpointType": "WebHook",
                "properties": {
                    "endpointUrl": "https://localhost:4438"
                }
            },
            "filter": {
                "subjectBeginsWith": "^",
                "subjectEndsWith": "$",
                "isSubjectCaseSensitive": false,
                "includedEventTypes": "[\"et1\",\"et2\"]",
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"data.b\"},{\"values\":[\"\\\"\",\"c\"],    \"operatorType\":\"StringContains\",\"key\":\"data.s\"}]"
            }
        },
        "twinTopic2EdgeHubSub": {
            "topic": "twinTopic2",
            "deliveryPolicy": {
                "approxBatchSizeInBytes": 200000,
                "maxEventsPerBatch": 25
            },
            "destination": {
                "endpointType": "EdgeHub",
                "properties": {
                    "outputName": "twinTopic2EdgeHubSub"
                }
            },
            "filter": {
                "advancedFilters": "[{\"value\":true,\"operatorType\":\"BoolEquals\",\"key\":\"dAt\\\"A.a\"},{\"values\":[\"\\\"\",    \"c\"],\"operatorType\":\"StringContains\",\"key\":\"dAt\\\"A.a\"}]"
            }
        }
    },
    "errors": {
        "bootupMessage": "",
        "bootupException": "",
        "topics": {},
        "eventSubscriptions": {
            "twinTopic1EventGridUserTopicSub": "HttpStatusCode='BadRequest' ErrorCode='InvalidDestination' Message='EventSubscription.Properties.Destination failed validation. Reason: EndpointUrl must target the /api/events API of Azure Event Grid in the cloud..'"
        }
    }
}
```
