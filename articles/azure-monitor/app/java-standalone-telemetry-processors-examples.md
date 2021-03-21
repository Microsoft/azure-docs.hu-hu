---
title: Telemetria processzor-példák – Azure Monitor Application Insights Javához
description: Ismerkedjen meg azokkal a példákkal, amelyek telemetria-processzorokat jelenítenek meg Azure Monitor Application Insights Javához.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734587"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Telemetria processzor-példák – Azure Monitor Application Insights Javához

Ez a cikk példákat tartalmaz a Java-Application Insights telemetria processzorokra. Mintákat is talál a konfigurációk belefoglalásához és kizárásához. Emellett mintákat is talál a Attribute processors és a span processzorok számára.
## <a name="include-and-exclude-samples"></a>Minták belefoglalása és kizárása

Ebben a szakaszban láthatja, hogyan lehet belefoglalni és kizárni a felölelt. Azt is megtudhatja, hogyan zárhat ki több ívelt, és hogyan alkalmazhat szelektív feldolgozást.
### <a name="include-spans"></a>Belefoglalási felölelés

Ez a szakasz azt mutatja be, hogyan lehet felölelni egy attribútum-processzort. A processzor által nem feldolgozott tulajdonságokkal rendelkező átnyúló.

A egyezéshez a span nevének egyenlőnek vagy értéknek kell lennie `spanA` `spanB` . 

Ezek a lépések megfelelnek a belefoglalási tulajdonságokkal, és a rendszer alkalmazza a processzor műveleteit:
* Span1 neve: "spana" attribútumok: {env: dev, test_request: 123, credit_card: 1234}
* Span2 neve: "spanB" attribútumok: {env: dev, test_request: false}
* Span3 neve: "spana" attribútumok: {env: 1, test_request: dev, credit_card: 1234}

Ez az időtartam nem egyezik a belefoglalási tulajdonságokkal, és a processzor műveletei nem alkalmazhatók:
* Span4 neve: "spanC" attribútumok: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>Elterjedések kizárása

Ez a szakasz azt mutatja be, hogyan lehet kizárni egy attribútum-feldolgozó felölelét. A processzor által nem feldolgozott tulajdonságok átívelnek.

A egyezéshez a span nevének egyenlőnek vagy értéknek kell lennie `spanA` `spanB` .

A következő találatok egyeznek a kizárás tulajdonságaival, és a processzor műveletei nem alkalmazhatók:
* Span1 neve: "spana" attribútumok: {env: dev, test_request: 123, credit_card: 1234}
* Span2 neve: "spanB" attribútumok: {env: dev, test_request: false}
* Span3 neve: "spana" attribútumok: {env: 1, test_request: dev, credit_card: 1234}

Ez az időtartam nem felel meg a kizárási tulajdonságoknak, és a rendszer alkalmazza a processzor műveleteit:
* Span4 neve: "spanC" attribútumok: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>Kihagyott kilépések több feltétel használatával

Ez a szakasz azt mutatja be, hogyan lehet kizárni egy attribútum-feldolgozó felölelét. A processzor által nem feldolgozott tulajdonságok átívelnek.

Az egyeztetéshez a következő feltételeknek kell teljesülniük:
* Egy attribútumnak (például `env` vagy `dev` ) léteznie kell a tartományon belül.
* A span attribútumnak rendelkeznie kell kulccsal `test_request` .

A következő találatok egyeznek a kizárás tulajdonságaival, és a processzor műveletei nem alkalmazhatók.
* Span1 neve: "spanB" attribútumok: {env: dev, test_request: 123, credit_card: 1234}
* Span2 neve: "spana" attribútumok: {env: dev, test_request: false}

A következő span nem felel meg a kizárási tulajdonságoknak, és a rendszer alkalmazza a processzor műveleteit:
* Span3 neve: "spanB" attribútumok: {env: 1, test_request: dev, credit_card: 1234}
* Span4 neve: "spanC" attribútumok: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>Szelektív feldolgozás

Ebből a szakaszból megtudhatja, hogyan határozhatja meg, hogy a rendszer milyen tartományokra alkalmazza ezt a processzort. A belefoglalási tulajdonságok azt jelzik, hogy mely átnyúlt kell feldolgozni. A kihagyott tulajdonságok kiszűrése kiszűri a nem feldolgozott tulajdonságokat.

A következő konfigurációban ezek a tulajdonságok megfelelnek a tulajdonságoknak, és a rendszer a processzor műveleteit alkalmazza:

* Span1 neve: "spanB" attribútumok: {env: Production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 neve: "spana" attribútumok: {env: előkészítés, test_request: false, redact_trace: true}

Ezek az átnyúló tulajdonságok nem egyeznek a belefoglalási tulajdonságokkal, és a processzor műveletei nem alkalmazhatók:
* Span3 neve: "spanB" attribútumok: {env: Production, test_request: true, credit_card: 1234, redact_trace: false}
* Span4 neve: "spanC" attribútumok: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```
## <a name="attribute-processor-samples"></a>Attribútum-feldolgozó minták

### <a name="insert"></a>Beszúrás

Az alábbi minta beszúrja az új attribútumot olyanra, `{"attribute1": "attributeValue1"}` ahol a kulcs `attribute1` nem létezik.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Beszúrás másik kulcsból

A következő minta az attribútum értékének használatával `anotherkey` szúrja be az új attribútumot, `{"newKey": "<value from attribute anotherkey>"}` ahol a kulcs `newKey` nem létezik. Ha az attribútum `anotherkey` nem létezik, a rendszer nem szúr be új attribútumot a felölelő elembe.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Frissítés

Az alábbi minta frissíti az attribútumot a következőre: `{"db.secret": "redacted"}` . Frissíti az attribútumot az `boo` attribútum értékének használatával `foo` . Az attribútum nem `boo` módosul.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Törlés

Az alábbi példa bemutatja, hogyan törölhet egy kulcsot tartalmazó attribútumot `credit_card` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Kivonat

Az alábbi példa bemutatja, hogyan lehet a meglévő attribútumok értékeit kiszámítani.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>Kinyerés

Az alábbi minta azt mutatja be, hogyan használható egy reguláris kifejezés (regex) új attribútumok létrehozására egy másik attribútum értéke alapján.
A megadott példa `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` a következő attribútumokat szúrja be:
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. URL: *nincs* változás

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

Az alábbi minta azt mutatja be, hogyan lehet feldolgozni az olyan átnyúló folyamatokat, amelyek a regex-mintáknak megfelelő span névvel rendelkeznek
Ez a processzor eltávolítja az `token` attribútumot. Felfedi az `password` attribútumot az elterjedésekben, ahol a span neve megegyezik `auth.*` , és a span neve nem egyezik `login.*` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>Kiterjedésű processzor mintái

### <a name="name-a-span"></a>Egy span neve

Az alábbi minta az attribútumok, a és a értékeit határozza meg `db.svc` `operation` `id` . A tartomány új nevét a (z) Ezen attribútumok alapján, ebben a sorrendben, az értékkel elválasztva képezi `::` .
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name"></a>Attribútumok kinyerése egy tartomány nevéből

Tegyük fel, hogy a bemeneti span neve a (z `/api/v1/document/12345678/update` ). A következő minta a kimeneti tartomány nevét eredményezi `/api/v1/document/{documentId}/update` . Hozzáadja az új attribútumot `documentId=12345678` a span értékhez.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Attribútumok kinyerése egy tartomány nevéből Belefoglalás és kizárás használatával

Az alábbi minta azt mutatja be, hogyan módosíthatja a span nevét a következőre: `{operation_website}` . `operation_website` `{oldSpanName}` Ha a span a következő tulajdonságokkal rendelkezik, egy kulcs és egy értékkel rendelkező attribútumot adhat hozzá:
- A span neve a `/` karakterláncban bárhol szerepel.
- A span neve nem `donot/change` .
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```
