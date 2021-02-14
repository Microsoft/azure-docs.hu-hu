---
title: Az Azure Function tevékenység Azure Data Factory
description: Ismerje meg, hogyan futtathat Azure-függvényeket az Azure functions szolgáltatásban egy Data Factory-folyamatban
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 51cf396fb6f1fcc86a0187e9f3be6b81487b86c2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383401"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Az Azure Function tevékenység Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Az Azure Function tevékenység lehetővé teszi [Azure functions](../azure-functions/functions-overview.md) futtatását egy Data Factory-folyamatban. Azure-függvény futtatásához létre kell hoznia egy társított szolgáltatási kapcsolatát és egy olyan tevékenységet, amely meghatározza a végrehajtandó Azure-függvényt.

A szolgáltatás nyolc perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Azure Function társított szolgáltatás

Az Azure-függvény visszatérési típusának érvényesnek kell lennie `JObject` . (Ne feledje, hogy [](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) a JArray *nem* `JObject` .) Bármely visszatérési típus, amely nem `JObject` sikerül, és a felhasználói hiba miatti *válasz tartalma nem érvényes JObject*.

| **Tulajdonság** | **Leírás** | **Kötelező** |
| --- | --- | --- |
| típus   | A Type tulajdonságot a következőre kell beállítani: **AzureFunction** | igen |
| function alkalmazás URL-címe | Az Azure-függvényalkalmazás URL-címe. Formátum: `https://<accountname>.azurewebsites.net` . Ez az URL-cím az **URL-** cím szakaszban található, amikor a függvényalkalmazás megtekinti a Azure Portal  | igen |
| függvény kulcsa | Az Azure-függvény elérési kulcsa. Kattintson a **Manage (kezelés** ) szakaszra a megfelelő függvényhez, és másolja a **függvény** vagy a **gazda kulcsot**. További információ: [Azure FUNCTIONS http-eseményindítók és-kötések](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | igen |
|   |   |   |

## <a name="azure-function-activity"></a>Azure Function tevékenység

| **Tulajdonság**  | **Leírás** | **Megengedett értékek** | **Kötelező** |
| --- | --- | --- | --- |
| name  | A folyamatban szereplő tevékenység neve  | Sztring | igen |
| típus  | A tevékenység típusa "AzureFunctionActivity". | Sztring | igen |
| társított szolgáltatás | Az Azure Function társított szolgáltatás a megfelelő Azure-függvényalkalmazás  | Társított szolgáltatás leírása | igen |
| függvény neve  | Azon függvény neve az Azure függvényalkalmazásban, amelyre ez a tevékenység hív | Sztring | igen |
| method  | A függvény hívásának REST API metódusa | Támogatott karakterlánc-típusok: "GET", "POST", "PUT"   | igen |
| fejléc  | A kérelembe küldendő fejlécek. Például a nyelv és a típus megadásához a következőre: "headers": {"Accept-Language": "en-us", "Content-Type": "Application/JSON"} | Karakterlánc (vagy resultType karakterláncot tartalmazó kifejezés) | No |
| body (Törzs)  | a függvény API-metódusának kérésével együtt elküldett törzs  | Sztring (vagy kifejezés resultType) vagy objektummal.   | PUT/POST metódusokhoz szükséges |
|   |   |   | |

Tekintse meg a kérelem hasznos adatainak sémáját a [kérelmek hasznos adatait tartalmazó sémában](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Útválasztás és lekérdezések

Az Azure Function tevékenység támogatja az **útválasztást**. Ha például az Azure-függvény rendelkezik végponttal  `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` , akkor az `functionName` Azure Function tevékenységben való használathoz `<functionName>/<value>` . Ezt a függvényt parametrizálja, hogy a kívánt `functionName` futtatókörnyezet elérhető legyen.

Az Azure Function tevékenység is támogatja a **lekérdezéseket**. A lekérdezésnek a részeként szerepelnie kell `functionName` . Ha például a függvény neve `HttpTriggerCSharp` és a felvenni kívánt lekérdezés `name=hello` , akkor az az `functionName` Azure Function tevékenységben a következőként hozható létre: `HttpTriggerCSharp?name=hello` . Ez a függvény konfigurálható úgy, hogy az érték a futásidőben is meghatározható legyen.

## <a name="timeout-and-long-running-functions"></a>Időtúllépés és hosszan futó függvények

A beállításokban konfigurált beállítástól függetlenül Azure Functions időtúllépés a 230 másodperc után `functionTimeout` . További információkért tekintse meg [ezt a cikket](../azure-functions/functions-versions.md#timeout). A viselkedés megkerüléséhez hajtson végre egy aszinkron mintát, vagy használja a Durable Functions. A Durable Functions előnye, hogy saját állapot-követési mechanizmust biztosítanak, így nem kell saját megvalósítást végrehajtania.

További információ a Durable Functionsről [ebben a cikkben](../azure-functions/durable/durable-functions-overview.md). Beállíthat egy Azure functions-tevékenységet a tartós függvény meghívásához, amely egy másik URI-val (például [a példával](../azure-functions/durable/durable-functions-http-features.md#http-api-url-discovery)) kapcsolatos választ ad vissza. Mivel `statusQueryGetUri` a a 202 http-állapotot adja vissza, miközben a függvény fut, a függvény állapotát egy webes tevékenység használatával kérdezheti le. Egyszerűen állítson be egy webes tevékenységet, amelynek a `url` mezője a értékre van állítva `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` . Ha a tartós funkció befejeződik, a függvény kimenete a webes tevékenység kimenete lesz.


## <a name="sample"></a>Sample

Megtalálhatja az Azure-függvényt használó Data Factory mintáját, amellyel kinyerheti a Tar [-fájl tartalmát](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction).

## <a name="next-steps"></a>Következő lépések

További információ a Data Factory kapcsolódó tevékenységekről [Azure Data Factory folyamatokban és tevékenységekben](concepts-pipelines-activities.md).
