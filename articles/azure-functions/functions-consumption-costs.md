---
title: A használaton Azure Functions
description: Megtudhatja, hogyan becsülheti meg jobban a költségeket, amelyek akkor előfordulhatnak, ha a függvényalkalmazást használat alapján futtatja az Azure-ban.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 648be6325cce5bad36795b113c8bbccb3e21d37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774002"
---
# <a name="estimating-consumption-plan-costs"></a>A használaton áteső csomag költségeinek becslése

Jelenleg háromféle típusú üzemeltetési csomag érhető el a Azure Functions-ban futó alkalmazásokhoz, amelyek mindegyikének saját díjszabási modellje van: 

| Felkészülés | Description |
| ---- | ----------- |
| [**Használat**](consumption-plan.md) | Csak a függvényalkalmazás futási ideje után kell fizetnie. Ez a csomag egy [ingyenes,]előfizetésenként[elérhető] díjszabási oldalt tartalmaz.|
| [**Prémium**](functions-premium-plan.md) | Ugyanazokkal a funkciókkal és méretezési mechanizmusokkal rendelkezik, mint a használat alapján elérhető csomag, de jobb teljesítményt és virtuális hálózatokhoz való hozzáférést biztosít. A költségek a választott tarifacsomagtól függnek. További információ: [Prémium Azure Functions csomag.](functions-premium-plan.md) |
| [**Dedikált (App Service)**](dedicated-plan.md) <br/>(alapszintű vagy magasabb szintű) | Ha dedikált virtuális gépeken vagy elkülönítetten kell futnia, használjon egyéni rendszerképeket, vagy a többletkapacitást App Service használni. Rendszeres [számlázási App Service használ.](https://azure.microsoft.com/pricing/details/app-service/) A költségek a választott tarifacsomagtól függnek.|

Azt a tervet választotta, amely a legjobban támogatja a függvények teljesítmény- és költségkövetelményét. További információ: Azure Functions [méretezése és üzemeltetése.](functions-scale.md)

Ez a cikk csak a használatban van, mivel ez a csomag változó költségekkel jár. Ez a cikk a használat alapján való [számlázással kapcsolatos gyakori kérdéseket olvassa](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) el.

Durable Functions használatban is futtathatók. A számlázással kapcsolatos költségekre vonatkozó Durable Functions lásd: [Durable Functions használata.](./durable/durable-functions-billing.md)

## <a name="consumption-plan-costs"></a>Használatalapú csomag költségei

Egy függvény *végrehajtásának* végrehajtási költségét *GB-másodpercben mérjük.* A végrehajtási költségek kiszámítása a memóriahasználat és a végrehajtási idő kombinálásával történik. Egy hosszabb költségekkel futó függvény, akár több memóriát fogyasztó függvény. 

Vegyük például azt az esetet, amikor a függvény által használt memória mennyisége állandó marad. Ebben az esetben a költség kiszámítása egyszerű szorzás. Tegyük fel például, hogy a függvény 3 másodpercig 0,5 GB-ot fogyasztott. Ezután a végrehajtási költség `0.5GB * 3s = 1.5 GB-seconds` . 

Mivel a memóriahasználat idővel változik, a számítás lényegében a memóriahasználat szerves része az idő múlásához.  A rendszer ezt a számítást a folyamat memóriahasználatának mintavételezésével (a gyermekfolyamatokkal együtt) rendszeres időközönkénti mintavételezésével teszi meg. A díjszabási oldalon [említettek szerint a]memóriahasználat a legközelebbi 128 MB-os gyűjtőre van kerekülve. Ha a folyamat 160 MB-ot használ, 256 MB díjat kell fizetnie. A számítás figyelembe veszi az egyidejűséget, amely több egyidejű függvényvégrehajtás ugyanabban a folyamatban.

> [!NOTE]
> Bár a processzorhasználat nem számít közvetlenül figyelembe a végrehajtási költségekben, hatással lehet a költségre, ha az befolyásolja a függvény végrehajtási idejét.

HTTP-eseményindítóval aktivált függvények esetén, ha hiba történik a függvénykód végrehajtásának megkezdése előtt, nem számítunk fel díjat a végrehajtásért. Ez azt jelenti, hogy a platformról az API-kulcs érvényesítése vagy az App Service Authentication /Authorization funkció miatt kapott 401-es válasz nem számít bele a végrehajtási költségekbe. Hasonlóképpen, az 5xx állapotkódválaszok nem számítanak bele, amikor a platformon előfordulnak, mielőtt egy függvény feldolgozta volna a kérést. A platform által a függvénykód végrehajtása után generált 5xx válasz akkor is végrehajtásnak számít, még akkor is, ha a hibát nem a függvénykód okozza.

## <a name="other-related-costs"></a>Egyéb kapcsolódó költségek

Amikor megbecsüli a függvények futtatásának teljes költségét bármely tervben, ne feledje, hogy a Functions-futtatókörnyezet több más Azure-szolgáltatást is használ, amelyek számlázása külön történik. A függvényalkalmazások díjszabásának kiszámításakor a más Azure-szolgáltatásokkal integrálható triggerek és kötések esetében létre kell hoznia ezeket a további szolgáltatásokat, és fizetnie kell ezekért a szolgáltatásokért. 

A használat alapján csomagon futó függvények esetén a teljes költség a függvények végrehajtási költsége, valamint a sávszélesség és a további szolgáltatások költsége. 

A függvényalkalmazás és a kapcsolódó szolgáltatások összköltségének becslésekor használja az [Azure díjkalkulátorát.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Kapcsolódó költségek | Leírás |
| ------------ | ----------- |
| **Storage-fiók** | Minden függvényalkalmazáshoz társítva kell általános célú Azure Storage-fiókhoz, amely külön [van kiszámlázva.](https://azure.microsoft.com/pricing/details/storage/) [](../storage/common/storage-introduction.md#types-of-storage-accounts) Ezt a fiókot a Functions-futtatás belsőleg használja, de Storage-eseményindítókhoz és -kötésekhez is használható. Ha nem rendelkezik tárfiókkal, a rendszer létrehoz egyet a függvényalkalmazás létrehozásakor. További információ: [Storage-fiókra vonatkozó követelmények.](storage-considerations.md#storage-account-requirements)|
| **Application Insights** | A Függvények [Application Insights,](../azure-monitor/app/app-insights-overview.md) hogy nagy teljesítményű monitorozási élményt nyújtson a függvényalkalmazások számára. Bár nem szükséges, engedélyezze a Application Insights [integrációját.](configure-monitoring.md#enable-application-insights-integration) Minden hónapban ingyenesen biztosítunk telemetriai adatokat. További tudnivalókért tekintse meg a Azure Monitor [oldalon.](https://azure.microsoft.com/pricing/details/monitor/) |
| **Hálózati sávszélesség** | Az ugyanabban a régióban található Azure-szolgáltatások közötti adatátvitelért nem kell fizetnie. Azonban költségekkel is jár a kimenő adatátvitel egy másik régióba vagy az Azure-n kívülre. További információ: [Sávszélesség díjszabása.](https://azure.microsoft.com/pricing/details/bandwidth/) |

## <a name="behaviors-affecting-execution-time"></a>A végrehajtási időt befolyásoló viselkedések

A függvények alábbi viselkedése befolyásolhatja a végrehajtási időt:

+ **Eseményindítók és kötések:** A bemeneti adatoknak a [](functions-triggers-bindings.md) függvénykötésekbe való be- és írásához szükséges idő végrehajtási időnek számít. Ha például a függvény kimeneti kötéssel ír egy üzenetet egy Azure Storage-üzenetsorba, a végrehajtási idő magában foglalja az üzenet üzenetsorba írásához szükséges időt, amely a függvény költségének kiszámításához szükséges. 

+ **Aszinkron végrehajtás:** Az az idő, ameddig a függvény egy aszinkron kérésre vár ( `await` C#-ban) végrehajtási időnek számít. A GB-másodperces számítás a függvény kezdő és záró időpontán, valamint az adott időszakra vonatkozó memóriahasználaton alapul. Az, hogy mi történik ebben az időszakban a cpu-tevékenység szempontjából, nem számít bele a számításba. A használatával csökkentheti a költségeket az aszinkron [műveletek Durable Functions.](durable/durable-functions-overview.md) A vezénylési függvények várakozó ideje nem lesz kiszámlázva.

## <a name="viewing-cost-related-data"></a>Költséggel kapcsolatos adatok megtekintése

A [számlán megtekintheti](../cost-management-billing/understand/download-azure-invoice.md)a teljes végrehajtások  – függvények és végrehajtási idő – Függvények – **Függvények** költségekkel kapcsolatos adatait, valamint a tényleges számlázott költségeket. Ezek a számlaadatok azonban havi összesítést tartalmaznak a korábbi számlázási időszakra vonatkozóan. 

### <a name="function-app-level-metrics"></a>Függvényalkalmazásszintű metrikák

A függvények költségeire gyakorolt hatás jobb Azure Monitor a függvényalkalmazások által aktuálisan létrehozott költségeket mutató metrikák megtekintéséhez. Ezeket az adatokat a Azure Monitor [metrikák](../azure-monitor/essentials/metrics-getting-started.md) Azure Portal REST API-k használatával is lekértheti. []

#### <a name="monitor-metrics-explorer"></a>Metrikák explorerének figyelése

A [Azure Monitor explorerrel grafikus](../azure-monitor/essentials/metrics-getting-started.md) formában megtekintheti a használatban használt csomag függvényalkalmazásaival kapcsolatos költségeket. 

1. A keresési [szolgáltatások Azure Portal] erőforrások és dokumentumok között **keresse** meg és válassza a Figyelás lehetőséget a `monitor` Szolgáltatások **alatt.** 

1. A bal oldalon válassza a **Metrikák** Válasszon ki egy erőforrást lehetőséget, majd használja a kép alatti beállításokat  >  a függvényalkalmazás kiválasztásához.

    ![A függvényalkalmazás erőforrásának kiválasztása](media/functions-consumption-costing/select-a-resource.png)

      
    |Beállítás  |Ajánlott érték  |Leírás  |
    |---------|---------|---------|
    | Előfizetés    |  Az Ön előfizetése  | Az előfizetés a függvényalkalmazással.  |
    | Erőforráscsoport     | Az erőforráscsoport  | A függvényalkalmazást tartalmazó erőforráscsoport.   |
    | Erőforrás típusa     |  App Services | A függvényalkalmazások a App Services példányaiként jelennek meg. |
    | Erőforrás     |  A függvényalkalmazás  | A figyelni szükséges függvényalkalmazás.        |

1. Válassza **az Alkalmaz** lehetőséget, és válassza ki a figyelni kívánt erőforrásként a függvényalkalmazást.

1. A **Metrika alatt** válassza a **Függvényvégrehajtások száma és** az Összeg lehetőséget az **Összesítéshez.**  Ez hozzáadja a kiválasztott időszak végrehajtási számainak összegét a diagramhoz.

    ![Függvényalkalmazás-metrika definiálása a diagramhoz való hozzáadáshoz](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Válassza **a Metrika hozzáadása lehetőséget,** és ismételje meg a 2–4. lépést, hogy függvény-végrehajtási **egységeket adjon a** diagramhoz. 

Az eredményül kapott diagram a választott időtartomány mindkét végrehajtási metrika összegét tartalmazza, amely ebben az esetben két óra.

![Függvényvégrehajtások számát és végrehajtási egységeket ábrázoló diagram](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Mivel a végrehajtási egységek száma sokkal nagyobb, mint a végrehajtások száma, a diagram csak a végrehajtási egységeket jeleníti meg.

Ez a diagram összesen 1,11 milliárd fogyasztást mutat egy kétórás `Function Execution Units` időszakban, MB-ezredmásodpercben mérve. A GB-másodpercre való konvertáláshoz ossza el az 1024000-et. Ebben a példában a függvényalkalmazás `1110000000 / 1024000 = 1083.98` GB-másodperceket fogyasztott. Ezt az értéket megszorozhatja a [Függvények][]díjszabási oldalán található végrehajtási idő aktuális árának szorz meg, amely ennek a két óranek a költségét adja meg, feltéve, hogy már felhasznált bármilyen ingyenes végrehajtási időt. 

#### <a name="azure-cli"></a>Azure CLI

Az [Azure CLI](/cli/azure/) parancsokkal rendelkezik a metrikák leolvasására. A parancssori felület a helyi parancskörnyezetből vagy közvetlenül a portálról is használható a [Azure Cloud Shell.](../cloud-shell/overview.md) A következő [az monitor metrics list parancs például](/cli/azure/monitor/metrics#az_monitor_metrics_list) óránkénti adatokat ad vissza a korábban használt időszakra vonatkozóan.

A helyére írja be a parancsot futtató `<AZURE_SUBSCRIPTON_ID>` Azure-előfizetés azonosítóját.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Ez a parancs egy JSON hasznos adatokat ad vissza, amely az alábbi példához hasonlít:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Ez a konkrét válasz azt mutatja, hogy a és a között az alkalmazás `2019-09-11T21:46` `2019-09-11T23:18` 1110000000 MB ezredmásodpercet (1083,98 GB-másodperc) fogyasztott.

### <a name="function-level-metrics"></a>Függvényszintű metrikák

A függvény-végrehajtási egységek a végrehajtási idő és a memóriahasználat kombinációja, ami megnehezíti a memóriahasználat megértését. A memóriaadatok jelenleg nem érhetők el metrikák Azure Monitor. Ha azonban optimalizálni szeretné az alkalmazás memóriahasználatát, használhatja az alkalmazás által gyűjtött teljesítményszámláló-Application Insights.  

Ha még nem tette meg, engedélyezze a Application Insights [a függvényalkalmazásban.](configure-monitoring.md#enable-application-insights-integration) Ha ez az integráció engedélyezve van, [lekérdezheti a telemetriai adatokat a portálon.](analyze-telemetry-data.md#query-telemetry-data) 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a függvényalkalmazások figyelésről](functions-monitoring.md)

[díjszabási oldal]:https://azure.microsoft.com/pricing/details/functions/
[Azure Portal]: https://portal.azure.com
