---
title: Webhook-tevékenység Azure Data Factory
description: A webhook tevékenység nem folytatja a folyamat végrehajtását mindaddig, amíg a felhasználó által megadott feltételekkel ellenőrzi a csatolt adatkészletet.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 59aa395db27c26a7c94eebdc0e3b34d7776ee75f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591996"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-tevékenység Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A webhook-tevékenységek az Egyéni kódban vezérelhetik a folyamatok végrehajtását. A webhook tevékenységgel az ügyfelek kódja hívhat meg egy végpontot, és átadhatja a visszahívási URL-címet. A folyamat futása megvárja a visszahívás meghívását, mielőtt továbblép a következő tevékenységre.

> [!IMPORTANT]
> A webhook tevékenység mostantól lehetővé teszi, hogy a hibák állapotát és az egyéni üzeneteket visszalépjen a tevékenységbe és a folyamatba. Állítsa a _reportStatusOnCallBack_ igaz értékre, és adja meg a _statuscode_ és a _hiba_ értéket a visszahívási adattartalomban. További információ: [további megjegyzések](#additional-notes) szakasz.

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
**name** | A webhook tevékenység neve. | Sztring | Yes |
**típusa** | "Webhook" értékre kell beállítani. | Sztring | Yes |
**metódus** | A célként megadott végpont REST API metódusa. | Sztring. A támogatott típus a "POST". | Yes |
**URL** | A célként megadott végpont és elérési út. | Karakterlánc vagy kifejezés egy sztring **resultType** értékkel. | Yes |
**fejlécek** | A kérelembe küldendő fejlécek. Az alábbi példa egy kérelem nyelvét és típusát állítja be: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Karakterlánc vagy kifejezés egy sztring **resultType** értékkel. | Igen. Ehhez `Content-Type` hasonló fejlécre `"headers":{ "Content-Type":"application/json"}` van szükség. |
**törzse** | A végpontnak elküldhető adattartalmat jelöli. | Érvényes JSON vagy kifejezés a JSON **resultType** -értékkel. Lásd a kérelem hasznos adatainak sémájának [kérése adattartalom-sémáját](./control-flow-web-activity.md#request-payload-schema) . | Yes |
**hitelesítés** | A végpont meghívásához használt hitelesítési módszer. A támogatott típusok az "alapszintű" és a "ClientCertificate". További információért lásd: [Hitelesítés](./control-flow-web-activity.md#authentication). Ha nincs szükség hitelesítésre, zárja be ezt a tulajdonságot. | Karakterlánc vagy kifejezés egy sztring **resultType** értékkel. | No |
**timeout** | Mennyi ideig várakozik a tevékenység a **callBackUri** által meghatározott visszahívás meghívásához. Az alapértelmezett érték 10 perc ("00:10:00"). Az értékek a *d* TimeSpan formátumban jelennek meg. *óó*:*PP*:mm. | Sztring | No |
**Jelentés állapota a visszahívás során** | Lehetővé teszi, hogy a felhasználó jelentést készítsen egy webhook-tevékenység sikertelen állapotáról. | Logikai | No |

## <a name="authentication"></a>Hitelesítés

A webhook-tevékenység a következő hitelesítési típusokat támogatja.

### <a name="none"></a>Nincsenek

Ha nincs szükség hitelesítésre, ne adja meg a **hitelesítési** tulajdonságot.

### <a name="basic"></a>Alapszintű

Az alapszintű hitelesítéshez használandó Felhasználónév és jelszó megadása.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Ügyféltanúsítvány

Itt adhatja meg a PFX-fájl és a jelszó Base64 kódolású tartalmát.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Felügyelt identitás

Az adatok gyárának felügyelt identitásával adhatja meg azt az erőforrás-URI-t, amelyhez a hozzáférési jogkivonatot kéri. Az Azure Resource Management API meghívásához használja a következőt: `https://management.azure.com/` . További információ a felügyelt identitások működéséről: [felügyelt identitások az Azure-erőforrások áttekintéséhez](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Ha az adat-előállító git-tárházral van konfigurálva, az alapszintű vagy az ügyféltanúsítvány-alapú hitelesítés használatához a hitelesítő adatait Azure Key Vault kell tárolnia. A Azure Data Factory nem tárol jelszavakat a git-ben.

## <a name="additional-notes"></a>További megjegyzések

Data Factory átadja az URL-végpontnak elküldett törzs további tulajdonságának **callBackUri** . A Data Factory a megadott időtúllépési érték előtt meghívja ezt az URI-t. Ha nem hívja meg az URI-t, a tevékenység a "időtúllépés" állapottal meghiúsul.

A webhook tevékenység meghiúsul, ha az egyéni végpontra irányuló hívás sikertelen. Bármely hibaüzenet felvehető a visszahívási törzsbe, és egy későbbi tevékenységben is felhasználható.

Minden REST API hívás esetén az ügyfél időtúllépést mutat, ha a végpont egy percen belül nem válaszol. Ez a szabványos HTTP ajánlott eljárás. A probléma megoldásához hozzon létre egy 202 mintát. Az aktuális esetben a végpont 202 (elfogadva) és az ügyfél-lekérdezéseket adja vissza.

A kérelemben szereplő egyperces időkorlát semmi köze a tevékenység időtúllépéséhez. Az utóbbi a **callbackUri** által meghatározott visszahívás megvárni.

A visszahívási URI-nak visszaadott törzsnek érvényes JSON-nek kell lennie. Állítsa be a fejlécet a következőre: `Content-Type` `application/json` .

Ha a **jelentés állapotát visszahívási** tulajdonságot használja, a visszahíváshoz a következő kódot kell felvennie a törzsbe:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő, a Data Factory által támogatott irányítási folyamatokat:

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)