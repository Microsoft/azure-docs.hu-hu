---
title: Nulla – állásidő üzembe helyezése Durable Functions
description: Megtudhatja, hogyan engedélyezheti a Durable Functions-előkészítést a nulla állásidőű központi telepítések esetében.
author: tsushi
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: azfuncdf
ms.custom: fasttrack-edit
ms.openlocfilehash: 707d624c47c536e00e98910a8902772703733515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558763"
---
# <a name="zero-downtime-deployment-for-durable-functions"></a>Nulla – állásidő üzembe helyezése Durable Functions

A Durable Functions [megbízható végrehajtási modellje](./durable-functions-orchestrations.md) megköveteli, hogy a determinisztikus legyenek, ami egy további, a frissítések központi telepítésekor megfontolandó kihívást hoz létre. Ha egy központi telepítés a tevékenységi függvények aláírásait vagy a Orchestrator logikát tartalmazza, a fedélzeti előkészítési példányok meghiúsulnak. Ez a helyzet különösen a hosszan futó munkafolyamatok példányai esetében jelent problémát, amely órákat vagy munkanapokat is jelenthet.

A hibák megelőzése érdekében két lehetőség közül választhat: 
- Késleltetheti az üzembe helyezést, amíg az összes futó előkészítési példány be nem fejeződik.
- Győződjön meg arról, hogy a futó előkészítési példányok a függvények meglévő verzióit használják. 

Az alábbi táblázat összehasonlítja a három fő stratégiát a Durable Functions nulla állásidős telepítésének megvalósításához: 

| Stratégia |  A következő esetekben használja | Előnyök | Hátrányok |
| -------- | ------------ | ---- | ---- |
| [Verziókezelés](#versioning) |  Olyan alkalmazások, amelyek nem tapasztalnak gyakori [feltörési változásokat.](durable-functions-versioning.md) | Egyszerűen implementálható. |  A Function app-méret növelése a memóriában és a függvények száma.<br/>A kód duplikálása. |
| [Állapot-ellenőrzési tárolóhelytel](#status-check-with-slot) | Olyan rendszer, amely nem rendelkezik 24 óránál hosszabb ideig futó, illetve gyakran átfedésben lévő előkészítési folyamatokkal. | Egyszerű kód alapja.<br/>Nincs szükség további Function app-felügyeletre. | További Storage-fiókot vagy a Task hub felügyeletét igényli.<br/>Olyan időszakot igényel, amikor a rendszer nem fut. |
| [Alkalmazás-Útválasztás](#application-routing) | Olyan rendszer, amely nem rendelkezik olyan időtartammal, amely nem fut, például azok az időszakok, amelyek 24 óránál rövidebbek, vagy gyakran átfedésben vannak. | Kezeli a rendszerek új verzióit, és folyamatosan futó, a változásokat megsértő munkafolyamatokat. | Intelligens alkalmazás-útválasztót igényel.<br/>Az előfizetés által engedélyezett Function Apps-alkalmazások maximális száma. Az alapértelmezett érték 100. |

## <a name="versioning"></a>Verziókezelés

Adja meg a függvények új verzióit, és hagyja meg a régi verziókat a Function alkalmazásban. Ahogy az ábrán látható, a függvény verziója a neve részévé válik. Mivel a függvények korábbi verziói megmaradnak, a fedélzeti előkészítési példányok továbbra is hivatkozhatnak rájuk. Eközben az új előkészítési példányokra vonatkozó kérések a legújabb verziót kérik, amelyet a rendszerelőkészítési ügyfél függvénye hivatkozhat az alkalmazás beállításától függően.

![Verziószámozási stratégia](media/durable-functions-zero-downtime-deployment/versioning-strategy.png)

Ebben a stratégiában minden függvényt át kell másolni, és a többi függvényre mutató hivatkozásokat frissíteni kell. Egyszerűbbé teheti a parancsfájlok írását. Példa egy áttelepítési parancsfájlt tartalmazó [projektre](https://github.com/TsuyoshiUshio/DurableVersioning) .

>[!NOTE]
>Ez a stratégia üzembe helyezési résidőket használ az üzembe helyezés során felmerülő állásidő elkerüléséhez. További információ az új üzembe helyezési pontok létrehozásáról és használatáról: [Azure functions üzembe helyezési](../functions-deployment-slots.md)pontok.

## <a name="status-check-with-slot"></a>Állapot-ellenőrzési tárolóhelytel

Amíg a Function alkalmazás aktuális verziója fut az üzemi tárolóhelyen, a Function app új verzióját üzembe helyezheti az átmeneti tárolóhelyen. Az éles üzemi és átmeneti tárolóhelyek cseréje előtt ellenőrizze, hogy vannak-e futó előkészítési példányok. Az összes előkészítési példány befejezése után elvégezheti a cserét. Ez a stratégia akkor működik, ha előre jelezhető időszakok állnak rendelkezésre, ha nincsenek előkészítési példányok a repülésben. Ez az a legjobb megoldás, ha a rendszer nem működik, és ha az előkészítési műveletek nem gyakran átfedésben vannak.

### <a name="function-app-configuration"></a>Function app-konfiguráció

A forgatókönyv beállításához kövesse az alábbi eljárást.

1. [Üzembe helyezési pontok hozzáadása](../functions-deployment-slots.md#add-a-slot) a Function alkalmazáshoz átmeneti és éles környezetben.

1. Az egyes tárolóhelyek esetében állítsa be a [AzureWebJobsStorage alkalmazás beállítását](../functions-app-settings.md#azurewebjobsstorage) egy megosztott Storage-fiók kapcsolódási karakterláncára. Ezt a Storage-fiókhoz tartozó kapcsolási karakterláncot a Azure Functions futtatókörnyezet használja. Ezt a fiókot a Azure Functions futtatókörnyezet használja, és kezeli a függvény kulcsait.

1. Az egyes tárolóhelyek esetében hozzon létre egy új alkalmazást, például: `DurableManagementStorage` . Állítsa az értékét a különböző Storage-fiókok kapcsolati karakterláncára. Ezeket a Storage-fiókokat a Durable Functions bővítmény használja a [megbízható végrehajtáshoz](./durable-functions-orchestrations.md). Mindegyik tárolóhelyhez külön Storage-fiókot használjon. Ne jelölje be a beállítást üzembe helyezési tárolóhelyként beállításként.

1. A Function app [host.jsfájl durableTask szakaszában](durable-functions-bindings.md#hostjson-settings)adja meg a `connectionStringName` (tartós 2. x) vagy a `azureStorageConnectionStringName` (tartós 1. x) értéket a 3. lépésben létrehozott Alkalmazásbeállítások nevével.

A következő ábra az üzembe helyezési pontok és a Storage-fiókok leírt konfigurációját mutatja be. Ebben a lehetséges előtelepítési forgatókönyvben a Function app 2. verziója az üzemi tárolóhelyen fut, míg az 1. verzió az átmeneti tárolóhelyen marad.

![Üzembe helyezési pontok és Storage-fiókok](media/durable-functions-zero-downtime-deployment/deployment-slot.png)

### <a name="hostjson-examples&quot;></a>host.jspéldákon

A következő JSON-töredékek példák a *host.js* fájlhoz tartozó kapcsolatok karakterlánc-beállítására.

#### <a name=&quot;functions-20&quot;></a>Függvények 2,0

```json
{
  &quot;version&quot;: 2.0,
  &quot;extensions&quot;: {
    &quot;durableTask&quot;: {
      &quot;hubName&quot;: &quot;MyTaskHub&quot;,
      &quot;storageProvider&quot;: {
        &quot;connectionStringName&quot;: &quot;DurableManagementStorage&quot;
      }
    }
  }
}
```

#### <a name=&quot;functions-1x&quot;></a>Functions 1.x

```json
{
  &quot;durableTask&quot;: {
    &quot;azureStorageConnectionStringName&quot;: &quot;DurableManagementStorage&quot;
  }
}
```

### <a name=&quot;cicd-pipeline-configuration&quot;></a>CI/CD-folyamat konfigurációja

Konfigurálja a CI/CD-folyamatot úgy, hogy csak akkor telepítsen, ha a Function alkalmazás nem rendelkezik függőben lévő vagy nem futó munkafolyamati példányokkal. Az Azure-folyamatok használatakor létrehozhat egy függvényt, amely ezeket a feltételeket ellenőrzi, ahogy az alábbi példában is látható:

```csharp
[FunctionName(&quot;StatusCheck")]
public static async Task<IActionResult> StatusCheck(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient client,
    ILogger log)
{
    var runtimeStatus = new List<OrchestrationRuntimeStatus>();

    runtimeStatus.Add(OrchestrationRuntimeStatus.Pending);
    runtimeStatus.Add(OrchestrationRuntimeStatus.Running);

    var result = await client.ListInstancesAsync(new OrchestrationStatusQueryCondition() { RuntimeStatus = runtimeStatus }, CancellationToken.None);
    return (ActionResult)new OkObjectResult(new { HasRunning = result.DurableOrchestrationState.Any() });
}
```

Ezután konfigurálja az átmeneti kaput, és várjon, amíg a rendszer nem fut. További információ: üzembehelyezési [üzembe helyezési vezérlő kiadása Gates használatával](/azure/devops/pipelines/release/approvals/gates)

![Üzembe helyezési kapu](media/durable-functions-zero-downtime-deployment/deployment-gate.png)

Az Azure-folyamatok az üzembe helyezés megkezdése előtt ellenőrzik a Function alkalmazást a rendszer-előkészítési példányok futtatásához.

![Üzembe helyezési kapu (fut)](media/durable-functions-zero-downtime-deployment/deployment-gate-2.png)

Most a Function alkalmazás új verzióját kell telepíteni az átmeneti tárolóhelyre.

![Átmeneti tárolóhely](media/durable-functions-zero-downtime-deployment/deployment-slot-2.png)

Végül cserélje le a tárolóhelyeket. 

Az üzembe helyezési pont beállításainak meg nem jelölt Alkalmazásbeállítások is felcserélve lesznek, így a 2-es verziójú alkalmazás az A Storage-fiókra hivatkozik. Mivel a rendszer a hangfelvételi állapotot a Storage-fiókban követi nyomon, a 2. verziójú alkalmazáson futó összes előkészítési művelet megszakítás nélkül fut az új tárolóhelyen.

![Üzembehelyezési pont](media/durable-functions-zero-downtime-deployment/deployment-slot-3.png)

Ha ugyanazt a Storage-fiókot szeretné használni mindkét tárolóhelyhez, módosíthatja a feladatok hubok nevét. Ebben az esetben a tárolóhelyek állapotát és az alkalmazás HubName beállításait kell kezelnie. További információ: [Durable Functionsban található feladatok hubok](durable-functions-task-hubs.md).

## <a name="application-routing"></a>Alkalmazás-Útválasztás

Ez a stratégia a legbonyolultabb. Azonban olyan Function-alkalmazásokhoz is használható, amelyek nem rendelkeznek időponttal a futók között.

Ehhez a stratégiához létre kell hoznia egy *alkalmazás-útválasztót* a Durable functions előtt. Ez az útválasztó Durable Functions használatával valósítható meg. Az útválasztó feladata a következő:

* Telepítse a Function alkalmazást.
* Durable Functions verziójának kezelése. 
* Átirányítja az alkalmazások működéséhez szükséges előkészítési kérelmeket.

Az útválasztó a következő feladatokat fogadja el az első alkalommal, amikor egy előkészítési kérelem érkezik:

1. Létrehoz egy új Function alkalmazást az Azure-ban.
2. Üzembe helyezi a Function alkalmazás kódját az új Function alkalmazásban az Azure-ban.
3. Továbbítja a előkészítési kérelmet az új alkalmazásnak.

Az útválasztó felügyeli, hogy az alkalmazás kódjának melyik verziója van telepítve az Azure-beli Function alkalmazáshoz.

![Alkalmazás-útválasztás (első alkalommal)](media/durable-functions-zero-downtime-deployment/application-routing.png)

Az útválasztó az üzembe helyezési és-előkészítési kérelmeket a kérelemmel elküldött verzió alapján irányítja a megfelelő Function alkalmazásba. Figyelmen kívül hagyja a javítási verziót.

Ha új verziót helyez üzembe az alkalmazásban, és ez nem módosul, megnövelheti a javítási verziót. Az útválasztó üzembe helyezi a meglévő Function alkalmazást, és kéréseket küld a kód régi és új verzióira, amelyek ugyanahhoz a Function alkalmazáshoz vannak irányítva.

![Alkalmazás-útválasztás (nincs megszakítási változás)](media/durable-functions-zero-downtime-deployment/application-routing-2.png)

Ha új verziót helyez üzembe az alkalmazásban, akkor megnövelheti a fő-vagy alverziót. Ezután az alkalmazás-útválasztó létrehoz egy új Function alkalmazást az Azure-ban, üzembe helyezi azt, és átirányítja az alkalmazás új verziójára irányuló kérelmeket. A következő ábrán az alkalmazás 1.0.1-es verziójában futó munkafolyamatok futnak, de a 1.1.0-verzióra vonatkozó kérések az új Function alkalmazásba lesznek irányítva.

![Alkalmazás-útválasztás (megszakítási változás)](media/durable-functions-zero-downtime-deployment/application-routing-3.png)

Az útválasztó figyeli a (z) 1.0.1 verziójának összehangolása állapotát, és eltávolítja az alkalmazásokat az összes előkészítés befejeződése után. 

### <a name="tracking-store-settings"></a>Nyomkövetési tároló beállításai

Mindegyik Function alkalmazásnak külön ütemezési várólistákat kell használnia, valószínűleg külön Storage-fiókokban. Ha az alkalmazás összes verziójában le szeretné kérdezni az összes előkészítési példányt, megoszthatja a példányok és az előzmények táblázatait a Function apps szolgáltatásban. A táblázatok megoszthatók úgy, `trackingStoreConnectionStringName` hogy a és a `trackingStoreNamePrefix` beállításokat a [host.jsa beállítási](durable-functions-bindings.md#host-json) fájlban konfigurálja, hogy mindegyik ugyanazt az értéket használja.

További információ: a [példányok kezelése az Azure](durable-functions-instance-management.md)-ban Durable functions.

![Nyomkövetési tároló beállításai](media/durable-functions-zero-downtime-deployment/tracking-store-settings.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Verziószámozás Durable Functions](durable-functions-versioning.md)
