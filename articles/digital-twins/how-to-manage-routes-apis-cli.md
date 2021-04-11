---
title: Végpontok és útvonalak kezelése (API-k és parancssori felület)
titleSuffix: Azure Digital Twins
description: 'Lásd: végpontok és esemény-útvonalak beállítása és kezelése az Azure Digital Twins-adatszolgáltatásokhoz.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c7efebaf23bd8e897243f6ee12b23d3821a4c033
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257330"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Végpontok és útvonalak kezelése az Azure Digital Twinsban (API-k és parancssori felület)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

Az Azure Digital Twins-ban átirányíthatja az [eseményekre vonatkozó értesítéseket](concepts-event-notifications.md) az alsóbb rétegbeli szolgáltatásokhoz vagy a csatlakoztatott számítási erőforrásokhoz. Ehhez először be kell állítani az eseményeket fogadó **végpontokat**. Ezután létrehozhat olyan  [**esemény-útvonalakat**](concepts-route-events.md) , amelyekkel megadhatja, hogy az Azure Digital Twins mely eseményeit adja meg a végpontok.

Ez a cikk végigvezeti a végpontok és útvonalak létrehozásának folyamatán a [REST API](/rest/api/azure-digitaltwins/)-kkal, a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)-val és az [Azure digitális Twins parancssori](how-to-use-cli.md)felületével.

Azt is megteheti, hogy a végpontokat és útvonalakat is felügyeli a [Azure Portal](https://portal.azure.com). A jelen cikk a portált használó verziója esetén olvassa el a következő témakört [*: útmutató: végpontok és útvonalak kezelése (portál)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Előfeltételek

- Szüksége lesz egy **Azure-fiókra** ( [itt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)akár ingyenesen is beállíthatja)
- Azure-beli **digitális Twins-példányra** lesz szüksége az Azure-előfizetésében. Ha még nem rendelkezik példányokkal, létrehozhat egyet a következő útmutató lépéseit követve [*: példány és hitelesítés beállítása*](how-to-set-up-instance-cli.md). A telepítésből származó alábbi értékekkel a cikk későbbi részében használhatja a következő értékeket:
    - Példány neve
    - Erőforráscsoport

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Végpont létrehozása az Azure Digital Twins számára

Ezek a példányok által létrehozható támogatott típusú végpontok:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Szolgáltatásbusz](../service-bus-messaging/service-bus-messaging-overview.md)

További információ a különböző végpontok típusairól: [*választás az Azure Messaging Services között*](../event-grid/compare-messaging-services.md).

Ez a szakasz ismerteti, hogyan hozhatja létre ezeket a végpontokat az Azure CLI használatával. A végpontokat a [DigitalTwinsEndpoint vezérlő síkja API](/rest/api/digital-twins/controlplane/endpoints)-k használatával is kezelheti.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>A végpont létrehozása

Miután létrehozta a végponti erőforrásokat, használhatja őket egy Azure digitális Twins-végponthoz. Az alábbi példák azt mutatják be, hogyan hozhatók létre végpontok az [Azure Digital Twins CLI](how-to-use-cli.md)-hez készült az [DT Endpoint Create](/cli/azure/dt/endpoint/create) paranccsal. Cserélje le a parancsokban szereplő helyőrzőket a saját erőforrásainak részleteire.

Event Grid végpont létrehozása:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Event Hubs végpont létrehozása (kulcs alapú hitelesítés):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Service Bus témakör végpontjának (kulcs alapú hitelesítés) létrehozása:
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

A parancsok sikeres futtatása után az Event Grid, az Event hub vagy a Service Bus témakör az Azure digitális Twins-beli végpontként lesz elérhető, az `--endpoint-name` argumentummal megadott néven. Ezt a nevet általában egy **esemény-útvonal** céljaként fogja használni, amelyet később fog létrehozni [ebben a cikkben](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Végpont létrehozása identitás-alapú hitelesítéssel

Létrehozhat egy identitás-alapú hitelesítést tartalmazó végpontot is, hogy a végpontot [felügyelt identitással](concepts-security.md#managed-identity-for-accessing-other-resources-preview)használja. Ez a beállítás csak az Event hub és a Service Bus típusú végpontok esetében érhető el (Event Grid esetén nem támogatott).

Az ilyen típusú végpont létrehozására szolgáló CLI-parancs alább látható. A következő értékeknek kell megadnia a helyőrzőket a parancsban:
* Az Azure Digital Twins-példány Azure-erőforrás-azonosítója
* végpont neve
* végpont típusa
* a végpont erőforrásának névtere
* az Event hub vagy a Service Bus témakör neve
* Az Azure Digital Twins-példány helye

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Végpont létrehozása a kézbesítetlen levelekkel

Ha egy végpont nem tud eseményt kézbesíteni egy adott időszakon belül, vagy ha az eseményt bizonyos számú alkalommal próbálta kézbesíteni, akkor a kézbesítetlen eseményt elküldheti egy Storage-fiókba. Ezt a folyamatot **Kézbesítetlen levélnek** nevezzük.

Az Azure Digital Twins [CLI](how-to-use-cli.md) vagy a [Control Plant API](how-to-use-apis-sdks.md#overview-control-plane-apis)-k használatával beállítható, hogy a kézbesítetlen üzenetek engedélyezve legyenek.

A kézbesítetlen levelekről további információt a [*fogalmak: események útvonalai*](concepts-route-events.md#dead-letter-events)című témakörben talál. A leállási végpontok bekapcsolásával kapcsolatos útmutatásért folytassa a szakasz további részében leírtakat.

#### <a name="set-up-storage-resources"></a>Tárolási erőforrások beállítása

A kézbesítetlen levelek helyének [beállítása előtt](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) rendelkeznie kell egy, az Azure-fiókban [beállított tárolóval](../storage/common/storage-account-create.md?tabs=azure-portal) . 

A tárolóhoz tartozó URI-t a későbbiekben a végpont létrehozásakor adja meg. A kézbesítetlen levelek helye a végpontnak egy [sas-tokent](../storage/common/storage-sas-overview.md)tartalmazó tároló URI-ként lesz megadva. Ennek a tokennek engedélyre van szüksége a `write` Storage-fiókban lévő cél tárolóhoz. A teljesen formázott **Kézbesítetlen sas URI** formátuma: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` .

Az alábbi lépéseket követve állíthatja be ezeket a tárolási erőforrásokat az Azure-fiókjában, hogy előkészítse a végponti kapcsolatok beállítását a következő szakaszban.

1. Az Azure-előfizetéshez tartozó **Storage-fiók** létrehozásához kövesse a [*Storage-fiók létrehozása*](../storage/common/storage-account-create.md?tabs=azure-portal) című témakör lépéseit. Jegyezze fel a Storage-fiók nevét, hogy később használhassa.
2. Az új Storage-fiókban **lévő tároló** létrehozásához kövesse a [*tároló létrehozása*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) című témakör lépéseit. Jegyezze fel a tároló nevét, hogy később használhassa.
3. Ezután hozzon létre egy **sas-jogkivonatot** a Storage-fiókjához, amelyet a végpont az eléréséhez használhat. Először navigáljon a Storage-fiókjához a [Azure Portalban](https://ms.portal.azure.com/#home) (a portál keresési sávjával megkeresheti a nevet).
4. A Storage-fiók lapon a bal oldali navigációs sávban válassza a _közös hozzáférési aláírás_ HIVATKOZÁST a sas-jogkivonat beállításának megkezdéséhez.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="A Storage-fiók lapja a Azure Portal" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. A *közös hozzáférésű aláírás lapon* az *engedélyezett szolgáltatások* és az *engedélyezett erőforrástípusok* területen válassza ki a kívánt beállításokat. Minden kategóriában ki kell választania legalább egy mezőt. Az *engedélyezett engedélyek* területen válassza az **írás** lehetőséget (ha szeretné, további engedélyeket is kijelölhet).
1. Állítsa be a többi beállításhoz használni kívánt értékeket.
1. Ha elkészült, kattintson a _sas létrehozása és kapcsolati karakterlánc létrehozása_ gombra az SAS-jogkivonat létrehozásához. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="A Storage-fiók lap a Azure Portal megjeleníti az SAS-token előállítására kijelölt összes beállítást, és kiemelve az &quot;SAS és kapcsolati karakterlánc létrehozása&quot; gombot." lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Ezzel a beállítással több SAS-és kapcsolatok-karakterlánc-értéket fog létrehozni ugyanazon oldal alján. Görgessen lefelé az értékek megtekintéséhez, és a *Másolás a vágólapra* ikonra kattintva másolja az **sas-jogkivonat** értékét. Mentse, hogy később használhassa.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="A kézbesítetlen levél titkos kódjában használandó SAS-token másolása." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>A kézbesítetlen levél végpontjának létrehozása

Ha olyan végpontot szeretne létrehozni, amelynél engedélyezve van a kézbesítetlen üzenetek használata, adja hozzá a következő kézbesítetlen levelek paramétert az az [DT Endpoint Create](/cli/azure/dt/endpoint/create) parancshoz az [Azure Digital Twins CLI](how-to-use-cli.md)-hez.

A paraméter értéke a **Kézbesítetlen levél sas URI-ja** , amely a Storage-fiók neve, a tároló neve és az [előző szakaszban](#set-up-storage-resources)összegyűjtött sas-token. Ez a paraméter létrehozza a végpontot a kulcs alapú hitelesítéssel.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Adja hozzá ezt a paramétert a végpont-létrehozási parancsok végéhez a végpont [*létrehozása*](#create-the-endpoint) szakaszból, hogy létrehozzon egy olyan végpontot a kívánt típusból, amelynél engedélyezve van a kézbesítetlen üzenetek használata.

Azt is megteheti, hogy a parancssori felület helyett az [Azure Digital Twins Control Plant API](how-to-use-apis-sdks.md#overview-control-plane-apis) -k használatával is létrehoz kézbesítetlen levelek végpontokat. Ehhez tekintse meg a [DigitalTwinsEndpoint dokumentációját](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) , és tekintse át a kérelem strukturálása és a kézbesítetlen levelek paramétereinek hozzáadása című témakört.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Kézbesítetlen levélbeli végpont létrehozása identitás-alapú hitelesítéssel

Létrehozhat egy olyan kézbesítetlen betűs végpontot is, amely rendelkezik identitás-alapú hitelesítéssel a végpont [felügyelt identitással](concepts-security.md#managed-identity-for-accessing-other-resources-preview)való használatához. Ez a beállítás csak az Event hub és a Service Bus típusú végpontok esetében érhető el (Event Grid esetén nem támogatott).

Az ilyen típusú végpont létrehozásához használja ugyanazt a CLI-parancsot a korábbi verzióról, ha [identitás-alapú hitelesítéssel szeretne létrehozni egy végpontot](#create-an-endpoint-with-identity-based-authentication), amely egy további mező a JSON-adattartalomban `deadLetterUri` .

Az alábbi értékeket kell csatlakoztatnia a helyőrzőhöz a parancsban:
* Az Azure Digital Twins-példány Azure-erőforrás-azonosítója
* végpont neve
* végpont típusa
* a végpont erőforrásának névtere
* az Event hub vagy a Service Bus témakör neve
* **Kézbesítetlen levél sas URI-ja** – részletek: Storage-fiók neve, tároló neve
* Az Azure Digital Twins-példány helye

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Üzenet tárolási sémája

Miután beállította a végpontot a kézbesítetlen üzenetek beállításával, a rendszer a következő formátumban tárolja a kézbesítetlen üzeneteket a Storage-fiókban:

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

A kézbesítetlen üzenetek megegyeznek az eredeti végpontnak kézbesíteni kívánt eredeti esemény sémájával.

Itt látható egy példa a [kettős létrehozási értesítésre](concepts-event-notifications.md#digital-twin-lifecycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Esemény útvonalának létrehozása

**Előfeltétel**: a cikk korábbi részében leírtak szerint hozzon létre végpontokat, mielőtt továbblép az útvonal létrehozásához. Ha befejezte a végpontok beállítását, folytassa az esemény-útvonal létrehozásával.

> [!NOTE]
> Ha nemrég telepítette a végpontokat, ellenőrizze, hogy készen állnak-e a telepítésre, **mielőtt** új esemény-útvonalra próbálja használni őket. Ha az útválasztás telepítése sikertelen, mert a végpontok nem állnak készen, várjon néhány percet, és próbálkozzon újra.
>
> Ha a folyamat futtatásakor ezt a folyamatot szeretné használni, akkor érdemes lehet ezt megvárnia, ha 2-3 perc várakozási idő alatt a végponti szolgáltatás befejezi a telepítést, mielőtt továbblép az útvonal beállítására.

Az Azure digitális Twins-ból egy végpontba küldött adatok tényleges elküldéséhez meg kell határoznia egy **esemény-útvonalat**. Az esemény-útvonalak a rendszer és az alárendelt szolgáltatások összekapcsolására szolgálnak.

Az útvonalak definíciója a következő elemeket tartalmazza:
* A használni kívánt útvonal neve
* A használni kívánt végpont neve
* Egy szűrő, amely meghatározza, hogy mely események kerülnek a végponthoz 

Ha nincs útvonal neve, az Azure digitális Ikreken kívül egyetlen üzenet sem lesz átirányítva. Ha van egy útvonal neve, és a szűrő `true` , az összes üzenet a végponthoz lesz irányítva. Ha van egy útvonal neve, és egy másik szűrő van hozzáadva, az üzenetek a szűrő alapján lesznek szűrve.

Az egyik útvonalnak engedélyezni kell több értesítés és eseménytípus kijelölését. 

Az esemény-útvonalak az Azure Digital Twins [ **EventRoutes** adatsík API](/rest/api/digital-twins/dataplane/eventroutes) -k vagy [ **az DT Route** CLI-parancsok](/cli/azure/dt/route)segítségével hozhatók létre. A szakasz további részében a létrehozás folyamata látható.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Útvonalak létrehozása az API-kkal és a C# SDK-val

Az események útvonalának definiálásának egyik módja az [adatsík API](how-to-use-apis-sdks.md#overview-data-plane-apis)-k használata. Az ebben a szakaszban szereplő minták a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)-t használják.

`CreateOrReplaceEventRouteAsync` egy esemény-útvonal hozzáadására szolgáló SDK-hívás. Íme egy példa a használatra:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

#### <a name="event-route-sample-sdk-code"></a>Event Route minta SDK-kód

Az alábbi példa bemutatja, hogyan hozhat létre, listázhat és törölhet egy esemény-útvonalat a C# SDK-val:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Útvonalak létrehozása a parancssori felülettel

Az útvonalakat az az [DT Route](/cli/azure/dt/route) paranccsal is kezelheti az Azure Digital Twins CLI-hez. 

További információ a parancssori felület használatáról és a rendelkezésre álló parancsokról [*: útmutató: az Azure digitális Twins parancssori*](how-to-use-cli.md)felületének használata.

## <a name="filter-events"></a>Események szűrése

Szűrés nélkül a végpontok számos eseményt kapnak az Azure Digital ikrektől:
* Az Azure Digital Twins szolgáltatás API-ját használó [digitális Twins](concepts-twins-graph.md) telemetria
* A Twin Property változási értesítései az Azure Digital Twins-példányon található bármelyik Twin tulajdonság változásakor
* Olyan életciklus-események, amelyek ikrek vagy kapcsolatok létrehozásakor vagy törlésekor következnek be

Az elküldött eseményeket úgy korlátozhatja, hogy hozzáad egy **szűrőt** egy végponthoz az esemény-útvonalhoz.

>[!NOTE]
> A szűrők **megkülönböztetik a kis-** és nagybetűket, és meg kell egyezniük a hasznos adatokkal. 
>
> Az telemetria szűrők esetében ez azt jelenti, hogy a burkolatnak meg kell egyeznie az eszköz által eljuttatott telemetria, nem feltétlenül a Twin modellben definiált burkolattal. 

Szűrő hozzáadásához használhat egy PUT kérelmet a *https://{saját-Azure-Digital-Twins-hostname}/eventRoutes/{Event-Route-Name}? API-Version = 2020-10-31* és a következő törzs használatával:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Itt láthatók a támogatott útválasztási szűrők. A *szűrő szöveg sémája* oszlopban található részletek használatával cserélje le a `<filter-text>` helyőrzőt a fenti kérelem törzsében.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Következő lépések

További információ a fogadott üzenetek különböző típusairól:
* [*Fogalmak: események értesítései*](concepts-event-notifications.md)
