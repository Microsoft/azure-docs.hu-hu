---
title: Oktatóanyag – a Azure Time Series Insights használatával tárolhatja és elemezheti az Azure-IoT Plug and Play eszköz telemetria
description: Oktatóanyag – Time Series Insights környezet beállítása és az IoT hub csatlakoztatása a IoT Plug and Play eszközök telemetria megtekintéséhez és elemzéséhez.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 08ae21c2cd0859b7c361756a4f0380d3ab322a28
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99834357"
---
# <a name="tutorial-create-and-configure-a-time-series-insights-gen2-environment"></a>Oktatóanyag: Time Series Insights Gen2-környezet létrehozása és konfigurálása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és konfigurálhat egy [Azure Time Series Insights Gen2](../time-series-insights/overview-what-is-tsi.md) -környezetet a IoT Plug and Play megoldással való integrációhoz. A Time Series Insights az idősoros adatok gyűjtésére, feldolgozására, tárolására, lekérdezésére és megjelenítésére használható eszközök internetes hálózata (IoT) skálán.

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Hozzon létre egy Time Series Insights-környezetet, és az IoT hub-t adatfolyam-eseményforrásként kapcsolja össze.
> * Az [idősorozat-modell](../time-series-insights/concepts-model-overview.md)létrehozásához a modell szinkronizálásával dolgozhat.
> * Használja a hőmérséklet-vezérlőhöz és a termosztáthoz használt [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) minta típusú fájlokat.

> [!NOTE]
> A Time Series Insights és a IoT közötti integráció előzetes verzióban érhető el Plug and Play. Előfordulhat, hogy a DTDL-eszköz modelljeinek a Time Series Insights idősorozat-modellhez való leképezése változhat. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Ezen a ponton a következőket teheti:

* Egy Azure IoT hub.
* Az IoT hub-hoz csatolt eszköz-kiépítési szolgáltatás (DPS) példánya. A DPS-példánynak rendelkeznie kell egy, a IoT Plug and Play eszközhöz tartozó egyéni eszköz regisztrálásával.
* Az IoT hub-hoz való kapcsolódás egyetlen összetevőből álló eszközről vagy több összetevőből álló, szimulált adatok továbbítására szolgáló eszközről.

Ha el szeretné kerülni az Azure CLI helyi telepítésének követelményét, használhatja a Azure Cloud Shell a Cloud Services beállításához.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Az eseményforrás előkészítése

A korábban létrehozott IoT hub lesz a Time Series Insights-környezete [eseményének forrása](../time-series-insights/concepts-streaming-ingestion-event-sources.md).

> [!IMPORTANT]
> Tiltsa le a meglévő IoT Hub útvonalakat. Ismert probléma van egy olyan IoT hub használatával, amelynek [útválasztása](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) konfigurálva van. Ideiglenesen tiltsa le az útválasztási végpontokat. Ha az IoT hub csatlakozik a Time Series Insightshoz, akkor ismét engedélyezheti az útválasztási végpontokat.

Az IoT hub-ban hozzon létre egy egyedi fogyasztói csoportot, amely a Time Series Insightst használja. A következő példában a helyére írja be a `my-pnp-hub` korábban használt IoT hub nevét.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-a-time-series-id"></a>Time Series-azonosító kiválasztása

A Time Series Insights-környezet kiépítésekor ki kell választania egy *idősorozat-azonosítót*. Fontos, hogy kiválassza a megfelelő idősorozat-azonosítót. Ez a tulajdonság nem módosítható, és a beállítása után nem módosítható. Az idősorozat-azonosító például egy adatbázis-partíciós kulcs. Az idősorozat-azonosító a Time Series-modell elsődleges kulcsaként működik. További információ: [ajánlott eljárások az idősorozat-azonosító kiválasztásához](../time-series-insights/how-to-select-tsid.md).

IoT Plug and Play felhasználóként az idősorozat-AZONOSÍTÓhoz adja meg a és a elemekből álló _összetett kulcsot_ `iothub-connection-device-id` `dt-subject` . Az IoT hub hozzáadja ezeket a rendszertulajdonságokat, amelyek tartalmazzák a IoT Plug and Play eszköz AZONOSÍTÓját és az eszköz összetevőinek nevét.

Ha a IoT Plug and Play eszköz modelljei jelenleg nem használnak összetevőket, akkor `dt-subject` egy összetett kulcs részeként is szerepelnie kell, hogy később is használhassa az összetevőket. Mivel az idősorozat-azonosító nem módosítható, a Microsoft javasolja a beállítás engedélyezését abban az esetben, ha a későbbiekben szüksége van rá.

> [!NOTE]
> A cikkben szereplő példák a több összetevőt tartalmazó `TemperatureController` eszközre vonatkoznak. A nem összetevővel rendelkező eszközökhöz azonban ugyanazok a fogalmak tartoznak `Thermostat` .

## <a name="provision-your-time-series-insights-environment"></a>A Time Series Insights-környezet kiépítése

Ez a szakasz azt ismerteti, hogyan lehet kiépíteni a Azure Time Series Insights Gen2-környezetét.

Futtassa az alábbi parancsot a következőhöz:

* Hozzon létre egy Azure Storage-fiókot a környezete [hűtőházi tárolójához](../time-series-insights/concepts-storage.md#cold-store). Ez a fiók a régi adatok hosszú távú megőrzésére és elemzésére szolgál.
  * A kódban cserélje le a `mytsicoldstore` nevet a hűtőházi Storage-fiókjához tartozó egyedi névre.
* Hozzon létre egy Azure Time Series Insights Gen2-környezetet. A környezet a hét napos megőrzési időtartammal rendelkező, meleg tárolóval jön létre. A rendszer a hűtőházi tárolási fiókot fogja csatolni a végtelen megőrzéshez.
  * A kódban cserélje le a `my-tsi-env` Time Series Insights-környezet egyedi nevét.
  * A kódban cserélje le a helyére a `my-pnp-resourcegroup` telepítés során használt erőforráscsoport nevét.
  * Az idősorozat-azonosító tulajdonsága: `iothub-connection-device-id, dt-subject` .

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

IoT Hub-eseményforrás összekapcsolásához. Cserélje `my-pnp-resourcegroup` le `my-pnp-hub` a, a és a `my-tsi-env` értéket a kiválasztott értékekre. A következő parancs a korábban létrehozott Time Series Insights felhasználói csoportra hivatkozik:

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportot, majd válassza ki az új Time Series Insights-környezetet. Lépjen a példány áttekintése **Time Series Insights Explorer URL-címére** :

![Képernyőkép a portál áttekintése oldalon.](./media/tutorial-configure-tsi/view-environment.png)

Az Explorerben három példány jelenik meg:

* &lt;a PnP-eszköz azonosítója &gt; , thermostat1
* &lt;a PnP-eszköz azonosítója &gt; , thermostat2
* &lt;a PnP-eszköz azonosítója &gt; , `null`

> [!NOTE]
> A harmadik címke magától a telemetria jelöli `TemperatureController` , például az eszköz memóriájának munkakészletét. Mivel ez egy legfelső szintű tulajdonság, az összetevő nevének értéke null. Egy későbbi lépésben ezt a nevet jobban felhasználóbarátvé teszi.

![Képernyőfelvétel: az Explorer három példányát mutatja.](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Modell fordításának konfigurálása

Ezután lefordítja a DTDL-eszköz modelljét a Azure Time Series Insights eszköz modelljére. Time Series Insights a Time Series modell egy szemantikai modellezési eszköz az adatcontextualizationhoz. A modell három alapvető összetevőből áll:

* Az [Idősorozat-modell példányai](../time-series-insights/concepts-model-overview.md#time-series-model-instances) magukban foglalhatják magukban az idősorozatok virtuális ábrázolását. A példányokat az idősorozat-azonosító egyedi módon azonosítja.
* Az [idősorozat-modell hierarchiái](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies) megszervezik a példányokat a tulajdonságok neveinek és kapcsolataiknak a megadásával.
* Az [idősorozat-modellek](../time-series-insights/concepts-model-overview.md#time-series-model-types) segítségével [változók](../time-series-insights/concepts-variables.md) vagy képletek definiálhatók a számításokhoz. A típusok egy adott példánnyal vannak társítva.

### <a name="define-your-types"></a>A típusok meghatározása

Megkezdheti az adatbevitelt Azure Time Series Insights Gen2 anélkül, hogy előre definiált egy modellt. Amikor a telemetria megérkezik, Time Series Insights megkísérli az idősorozat-példányok automatikus feloldását az idősorozat-azonosító tulajdonság értékei alapján. Az összes példány az *alapértelmezett típushoz* van rendelve. A példányok megfelelő kategorizálásához manuálisan kell létrehoznia egy új típust. 

A következő részletek ismertetik a legegyszerűbb módszert az eszköz DTDL-modelljeinek az idősorozat-modellel való szinkronizálásához:

* A digitális kettős modell azonosítója a típusazonosító lesz.
* A típus neve lehet a modell neve vagy a megjelenített név.
* A modell leírása lesz a típus leírása.
* Minden olyan telemetria létre kell hozni legalább egy típus változót, amely numerikus sémával rendelkezik.
  * Változókhoz csak numerikus adattípusok használhatók, de ha egy értéket egy másik típusként küld el, amely átalakítható, például `"0"` használhat [konverziós](/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) függvényt is `toDouble` .
* A változó neve lehet a telemetria neve vagy a megjelenített név.
* Az idősorozat-kifejezés változó meghatározásakor tekintse meg a telemetria nevét a dróton és a telemetria adattípusát.

| DTDL JSON | Time Series-modell JSON-típusa | Példaérték |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` tömb| `variables` objektum  | Tekintse meg a következő példát.

![Képernyőfelvétel: D T D L – idősorozat-modell típusa.](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Ez a példa három változót mutat be, de az egyes típusok legfeljebb 100 változót tartalmazhatnak. A különböző változók hivatkozhatnak ugyanarra a telemetria-értékre, hogy szükség esetén eltérő számításokat végezzenek. A szűrők, összesítések és skaláris függvények teljes listáját itt tekintheti meg: [Time Series Insights Gen2 Time Series kifejezés szintaxisa](/rest/api/time-series-insights/reference-time-series-expression-syntax).

Nyisson meg egy szövegszerkesztőt, és mentse a következő JSON-t a helyi meghajtóra.

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

A Time Series Insights Explorerben kattintson a bal oldali modell ikonra a **modell** lap megnyitásához. Válassza a **típusok** lehetőséget, majd válassza a **JSON feltöltése** elemet:

![A JSON feltöltését bemutató képernyőkép.](./media/tutorial-configure-tsi/upload-type.png)

Válassza a **fájl kiválasztása** lehetőséget, válassza ki a korábban mentett JSON-t, majd válassza a **feltöltés** lehetőséget.

Megjelenik az újonnan definiált **hőmérséklet-vezérlő** típusa.

### <a name="create-a-hierarchy"></a>Hierarchia létrehozása

Hozzon létre egy hierarchiát a címkék a `TemperatureController` szülőn belüli rendszerezéséhez. A következő egyszerű példa egyetlen szinttel rendelkezik, de a IoT-megoldások gyakran több beágyazási szinttel rendelkeznek, hogy a contextualize a címkéket a szervezeten belüli fizikai és szemantikai helyükön belül.

Válassza a **hierarchiák** lehetőséget, majd válassza a **hierarchia hozzáadása** elemet. A név mezőbe írja be az *eszköz flottáját*. Hozzon létre egy, az *eszköz neve* nevű szintet. Kattintson a **Mentés** gombra.

![A hierarchia hozzáadását bemutató képernyőkép.](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>A példányok helyes típushoz rendelése

Ezután módosítsa a példányok típusát, és helyezze őket a hierarchiába.

Válassza a **példányok** fület. Keresse meg az eszköz munkakészletét jelképező példányt, majd a jobb szélen kattintson a **Szerkesztés** ikonra.

![A példányok szerkesztését bemutató képernyőkép.](./media/tutorial-configure-tsi/edit-instance.png)

Nyissa meg a **típus** legördülő menüt, majd válassza a **hőmérséklet-vezérlő** elemet. Adja meg a *defaultComponent, <your device name>* hogy frissítse a példány nevét, amely az eszközhöz társított összes legfelső szintű címkét jelképezi.

![A példány típusának módosítását bemutató képernyőkép.](./media/tutorial-configure-tsi/change-type.png)

A Save ( **Mentés**) gombra kattintva válassza ki a **példány mezői** lapot, majd válassza az **eszköz flotta** elemet. A telemetria együttes csoportosításához írja be a *\<your device name> -temp vezérlőt*. Kattintson a **Mentés** gombra.

![A példányok hierarchiához rendelését bemutató képernyőkép](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Az előző lépések megismétlésével rendelje hozzá a termosztát címkéit a megfelelő típushoz és hierarchiához.

## <a name="view-your-data"></a>Adatok megtekintése

Lépjen vissza a diagram panelre, és bontsa ki az eszköz **flottáját** > az eszközt. Válassza a **thermostat1** lehetőséget, válassza ki a **hőmérséklet** változót, majd kattintson a **Hozzáadás** elemre az érték ábrázolásához. Végezze el ugyanezt a **thermostat2** és a **defaultComponent** **munkakészlet** értéknél.

![A thermostat2 példány típusának módosítását bemutató képernyőkép.](./media/tutorial-configure-tsi/charting-values.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-pnp-clean-resources](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> Ha többet szeretne megtudni a különböző diagram-beállítási lehetőségekről, beleértve az intervallumok méretezését és az y tengely vezérlőelemeket, tekintse meg a [Azure Time Series Insights Explorert](../time-series-insights/concepts-ux-panels.md).
