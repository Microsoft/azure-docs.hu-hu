---
title: Az Azure Digital Twins API-k és SDK-k használata
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan dolgozhat az Azure Digital Twins API-kkal, beleértve az SDK-t is.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f4f3fc8c928cd284088cc51120f1a7b485b4fac0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595345"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Az Azure Digital Twins API-k és SDK-k használata

Az Azure digitális Twins a **vezérlési sík API** -kkal és az **adatsík API** -kkal is rendelkezik a példány és az elemek kezeléséhez. 
* A vezérlési sík API-jai [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) API-k, és olyan erőforrás-kezelési műveleteket takarnak, mint például a példányok létrehozása és törlése. 
* Az adatsíkok API-jai az Azure Digital Twins API-k, és olyan adatkezelési műveletekhez használatosak, mint például a modellek, az ikrek és a gráfok kezelése.

Ez a cikk áttekintést nyújt az elérhető API-król, valamint a velük való interakció módszereiről. Használhatja a REST API-kat közvetlenül a hozzájuk kapcsolódó hencegő adatokkal (például [Poster](how-to-use-postman.md)-eszközön keresztül) vagy egy SDK-val.

## <a name="overview-control-plane-apis"></a>Áttekintés: felügyeleti sík API-k

A vezérlési sík API-k az Azure Digital Twins-példányok egészének kezelésére szolgáló [ARM](../azure-resource-manager/management/overview.md) API-k, így olyan műveletekre is kiterjednek, mint például a teljes példány létrehozása vagy törlése. Ezeket a végpontok létrehozásához és törléséhez is használni fogja.

A jelenlegi Control Plant API-verzió a _**2020-12-01**_.

A vezérlő sík API-k használata:
* Az API-kat közvetlenül hívhatja a [vezérlési sík hencegő](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)tárházának legújabb hencegő mappájára hivatkozva. Ez a mappa a használatot bemutató példákat tartalmazó mappát is tartalmaz.
* Jelenleg a vezérlési API-k számára érhető el az SDK-ban...
  - [**.Net (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([hivatkozás [automatikusan generált]](/dotnet/api/overview/azure/digitaltwins/management)) ([forrás](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([hivatkozás [automatikusan generált]](/java/api/overview/azure/digitaltwins)) ([forrás](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([forrás](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([forrás](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([forrás](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

A vezérlési sík API-kat a [Azure Portal](https://portal.azure.com) és a [CLI](how-to-use-cli.md)használatával is használhatja az Azure digitális ikrekkel való interakcióval.

## <a name="overview-data-plane-apis"></a>Áttekintés: adatsík API-k

Az adatsík API-k az Azure Digital Twins-példányban található elemek kezelésére szolgáló Azure digitális Twins API-k. Olyan műveleteket tartalmaznak, mint például útvonalak létrehozása, modellek feltöltése, kapcsolatok létrehozása és az ikrek kezelése. A következő kategóriákba sorolhatók:
* **DigitalTwinModels** – a DigitalTwinModels kategória API-kat tartalmaz az Azure Digital Twins-példány [modelljeinek](concepts-models.md) kezeléséhez. A felügyeleti tevékenységek közé tartozik a DTDL-ben létrehozott modellek feltöltése, ellenőrzése, beolvasása és törlése.
* **DigitalTwins** – a DigitalTwins kategória tartalmazza azokat az API-kat, amelyek segítségével a fejlesztők [digitális ikreket](concepts-twins-graph.md) és kapcsolataikat hozhatnak létre, módosíthatnak és törölhetnek egy Azure digitális Twins-példányban.
* **Lekérdezés** – a lekérdezési kategória lehetővé teszi a fejlesztők számára [, hogy a kettős gráfban a kapcsolatok között megtalálják a digitális ikrek készleteit](how-to-query-graph.md) .
* **Esemény-útvonalak** – az Event Routes kategória API-kat tartalmaz az [adattovábbításhoz](concepts-route-events.md)a rendszeren és az alárendelt szolgáltatásokon keresztül.

A legfrissebb adatsíkok API-verziója _**2020-10-31**_.

Az adatsík API-k használata:
* Az API-kat közvetlenül, a... alapján hívhatja le.
   - a legfrissebb hencegő mappára hivatkozik az [adatközpontban](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Ez a mappa a használatot bemutató példákat tartalmazó mappát is tartalmaz. 
   - az [API-referenciák dokumentációjának](/rest/api/azure-digitaltwins/)megtekintése.
* Használhatja a **.net (C#) SDK-** t. A .NET SDK használata...
   - a csomagot a NuGet: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core)fájlból tekintheti meg és adhatja hozzá. 
   - megtekintheti az [SDK-referenciák dokumentációját](/dotnet/api/overview/azure/digitaltwins/client).
   - megtalálhatja az SDK-forrást, beleértve a példákat tartalmazó mappát is a GitHubban: az [Azure IoT Digital Twins ügyféloldali kódtára a .net-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - a részletes információkat és használati példákat a jelen cikk [*.net (C#) SDK (adatsíkja)*](#net-c-sdk-data-plane) című szakaszában tekintheti meg.
* Használhatja a **Java SDK**-t. A Java SDK használata...
   - a csomagot a Mavenből tekintheti meg és telepítheti: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - megtekintheti az [SDK-referenciák dokumentációját](/java/api/overview/azure/digitaltwins/client)
   - Az SDK-forrást a GitHubon találja: az [Azure IoT Digital Twins ügyféloldali kódtára a Javához](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Használhatja a **JavaScript SDK**-t. A JavaScript SDK használata...
   - a csomagot a NPM-ből tekintheti meg és telepítheti: [Azure Azure Digital Twins Core ügyféloldali kódtár a javascripthez](https://www.npmjs.com/package/@azure/digital-twins-core).
   - megtekintheti az [SDK-referenciák dokumentációját](/javascript/api/@azure/digital-twins-core/).
   - Az SDK-forrást a GitHubon találja: [Azure Azure Digital Twins Core ügyféloldali kódtár a javascripthez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Használhatja a **PYTHON SDK**-t. A Python SDK használata...
   - a csomagot a PyPi-ből tekintheti meg és telepítheti: az [Azure Azure Digital Twins Core ügyféloldali kódtára a Pythonhoz](https://pypi.org/project/azure-digitaltwins-core/).
   - megtekintheti az [SDK-referenciák dokumentációját](/python/api/azure-digitaltwins-core/azure.digitaltwins.core).
   - Az SDK forrását a GitHubon találja: az [Azure Azure Digital Twins Core ügyféloldali kódtára a Pythonhoz](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Az autorest használatával egy másik nyelvhez is létrehozhat SDK-t. Kövesse az útmutató [*: egyéni SDK-k létrehozása az Azure digitális ikrekhez az autorest használatával*](how-to-create-custom-sdks.md)című témakör utasításait.

A Date Plant API-kat a [CLI](how-to-use-cli.md)használatával is használhatja az Azure digitális ikrekkel.

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (adatsík)

Az Azure Digital Twins .NET (C#) SDK része a .NET-hez készült Azure SDK-nak. Nyílt forráskódú, és az Azure Digital Twins adatsíkja API-jai alapján érhető el.

> [!NOTE]
> Az SDK kialakításával kapcsolatos további információkért tekintse meg az Azure SDK-k általános [tervezési alapelveit](https://azure.github.io/azure-sdk/general_introduction.html) és az adott [.net tervezési irányelveket](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Az SDK használatához foglalja bele az **Azure. DigitalTwins. Core** NuGet-csomagot a projektbe. Az **Azure. Identity** csomag legújabb verziójára is szüksége lesz. A Visual Studióban ezeket a csomagokat a NuGet Package Managerrel veheti fel (az eszközökön keresztül érhető el *> NuGet csomagkezelő > NuGet-csomagok kezelése a megoldáshoz*). Azt is megteheti, hogy a .NET parancssori eszközt használja az alábbi NuGet-csomag hivatkozásaiban található parancsokkal a projekthez való hozzáadáshoz:
* [**Azure. DigitalTwins. Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Ez a csomag a .NET-hez készült [Azure Digital Twins SDK](/dotnet/api/overview/azure/digitaltwins/client)-hoz. 
* [**Azure. Identity**](https://www.nuget.org/packages/Azure.Identity). Ez a kódtár eszközöket biztosít az Azure-beli hitelesítéshez.

Az API-k gyakorlatban való használatának részletes ismertetését az [*oktatóanyag: az ügyfélalkalmazás kódolása*](tutorial-code.md)című témakörben tekintheti meg. 

### <a name="net-sdk-usage-examples"></a>Példák a .NET SDK használatára

Íme néhány kód, amely a .NET SDK használatát szemlélteti.

Hitelesítés a szolgáltatással:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Modell feltöltése:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Modellek listázása:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Ikrek létrehozása:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Az ikrek és a hurok lekérdezése az eredmények között:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Tekintse meg a következő [*oktatóanyagot: az ügyfélalkalmazás kódjának*](tutorial-code.md) bejárására szolgáló alkalmazás. 

További mintákat is talál a GitHub-tárházban a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)-hoz.

#### <a name="serialization-helpers"></a>Szerializálási segítők

A szerializáló segítői az SDK-n belül elérhető segítő függvények, amelyek lehetővé teszik a Twin adatok gyors létrehozását vagy deszerializálását az alapvető információk eléréséhez. Mivel az alapszintű SDK-metódusok alapértelmezés szerint a következő két értéket adják vissza JSON-ként, hasznos lehet ezeket a segítő osztályokat használni a Twin-adatsorok további bontásához.

Az elérhető segítő osztályok a következők:
* `BasicDigitalTwin`: Általánosságban a digitális iker alapértékeit jelöli
* `BasicDigitalTwinComponent`: Általánosságban a egy összetevőt jelöl a `Contents` Tulajdonságok között `BasicDigitalTwin`
* `BasicRelationship`: Általánosságban a kapcsolat alapvető értékeit jelöli
* `DigitalTwinsJsonPropertyName`: Az egyéni digitális Twin-típusok JSON-szerializálásához és deszerializálásához használható karakterlánc-konstansokat tartalmazza.

##### <a name="deserialize-a-digital-twin"></a>Digitális Twin deszerializálása

A Twin-adatait bármikor deszerializálhatja a választott JSON-kódtár használatával, például `System.Text.Json` vagy `Newtonsoft.Json` . A Twin-hez való alapszintű hozzáféréshez a segítő osztályok kényelmesebbvé tehetik ezt a lehetőséget.

A `BasicDigitalTwin` Helper osztály emellett hozzáférést biztosít a Twin-en, a-on keresztül meghatározott tulajdonságokhoz `Dictionary<string, object>` . A Twin tulajdonságok listázásához a következőket használhatja:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin``System.Text.Json`attribútumok használata. Ahhoz, hogy használni tudja a `BasicDigitalTwin` [DigitalTwinsClient](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true), vagy inicializálnia kell az ügyfelet az alapértelmezett konstruktorral, vagy ha testre szeretné szabni a szerializáló beállítást, használja a [JsonObjectSerializer](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true).

##### <a name="create-a-digital-twin"></a>Digitális Twin létrehozása

A `BasicDigitalTwin` osztály használatával előkészítheti az adatkészletet egy kettős példány létrehozásához:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

A fenti kód a következő "kézi" változattal egyenértékű:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Kapcsolat deszerializálása

A kapcsolati adatait bármikor deszerializálhatja tetszőleges típusra. A kapcsolat alapszintű eléréséhez használja a típust `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

A `BasicRelationship` segítő osztály a kapcsolaton keresztül megadott tulajdonságok elérését is lehetővé teszi `IDictionary<string, object>` . A tulajdonságok listázásához a következőket használhatja:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Kapcsolat létrehozása

A (z) osztály használatával létrehozhatók `BasicRelationship` olyan kapcsolatok, amelyekkel kapcsolat hozható létre egy kettős példányon:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Javítás készítése a Twin Update szolgáltatáshoz

Az ikrek és a kapcsolatok frissítési hívásainak használata [JSON-javítási](http://jsonpatch.com/) struktúrát használ. A JSON-javítási műveletek listáját a `JsonPatchDocument` lent látható módon is létrehozhatja.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Általános API-/SDK-használati megjegyzések

> [!NOTE]
> Vegye figyelembe, hogy az Azure Digital Twins jelenleg nem támogatja a **több eredetű erőforrás-megosztást (CORS)**. A hatás-és megoldási stratégiákkal kapcsolatos további információkért tekintse meg a *fogalmak: biztonság Azure digitális Twins-megoldásokhoz* című szakaszát. [](concepts-security.md#cross-origin-resource-sharing-cors)

Az alábbi lista további részleteket és általános irányelveket tartalmaz az API-k és SDK-k használatához.

* Használhat olyan HTTP REST-tesztelési eszközt, mint a Poster, amely közvetlen hívásokat hajt végre az Azure digitális Twins API-kon. További információ erről a folyamatról [*: útmutató: kérelmek küldése a Poster használatával*](how-to-use-postman.md).
* Az SDK használatához hozza létre az `DigitalTwinsClient` osztályt. A konstruktorhoz olyan hitelesítő adatok szükségesek, amelyek különböző hitelesítési módszerekkel szerezhetők be a `Azure.Identity` csomagban. További `Azure.Identity` információ: a [névtér dokumentációja](/dotnet/api/azure.identity). 
* Hasznos lehet az `InteractiveBrowserCredential` első lépések során, de több más lehetőség is van, beleértve a [felügyelt identitáshoz](/dotnet/api/azure.identity.interactivebrowsercredential)tartozó hitelesítő adatokat is, amelyeket valószínűleg az MSI-vel az Azure Digital Twins szolgáltatásban [beállított Azure-függvények](../app-service/overview-managed-identity.md?tabs=dotnet) hitelesítésére fog használni. További információ `InteractiveBrowserCredential` : az [osztály dokumentációja](/dotnet/api/azure.identity.interactivebrowsercredential).
* Az összes szolgáltatás API-hívása tag-függvényként van kitéve az `DigitalTwinsClient` osztályban.
* Az összes szolgáltatási funkció szinkron és aszinkron verziókban található.
* Az összes szolgáltatási funkció kivételt jelez a 400-es vagy újabb visszaküldési állapot esetén. Ügyeljen arra, hogy a hívásokat egy `try` szakaszba csomagolja, és legalább a fogást `RequestFailedExceptions` . Az ilyen típusú kivételekről [itt](/dotnet/api/azure.requestfailedexception)talál további információt.
* A legtöbb szolgáltatási módszer visszaadja `Response<T>` vagy (az `Task<Response<T>>` aszinkron hívások esetében), ahol a a `T` szolgáltatás hívásához tartozó visszatérési objektum osztálya. A [`Response`](/dotnet/api/azure.response-1) osztály beágyazza a szolgáltatás visszaadását, és megadja a visszatérési értékeket a `Value` mezőben.  
* A lapozható eredményekkel `Pageable<T>` vagy eredményekkel rendelkező szolgáltatási metódusok `AsyncPageable<T>` . További információ az `Pageable<T>` osztályról: itt [](/dotnet/api/azure.pageable-1)talál további információt `AsyncPageable<T>` . [](/dotnet/api/azure.asyncpageable-1)
* A lapozható eredmények egy hurok használatával is megadhatók `await foreach` . További információ erről a folyamatról: [.](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)
* A mögöttes SDK a `Azure.Core` . Az SDK-infrastruktúrával és-típusokkal kapcsolatos tudnivalókat az [Azure névtér dokumentációjában](/dotnet/api/azure) találja.

A szolgáltatási metódusok nagy mértékben gépelt objektumokat adnak vissza, ahol lehetséges. Mivel azonban az Azure Digital Twins a felhasználó által a futásidőben konfigurált modelleken alapul (a szolgáltatásba feltöltött DTDL-modelleken keresztül), számos szolgáltatás-API-t fogad el és ad vissza a két, JSON formátumú formában.

## <a name="monitor-api-metrics"></a>API-metrikák figyelése

Az API-metrikák, például a kérelmek, a késések és a hibák aránya a [Azure Portal](https://portal.azure.com/)tekinthető meg. 

A portál kezdőlapján keresse meg az Azure Digital Twins-példányát, és adja meg a részleteit. Válassza ki a **mérőszámok** lehetőséget az Azure Digital Twins-példány menüjében a *metrikák* lap megjelenítéséhez.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Képernyőfelvétel az Azure Digital Twins metrikáinak oldaláról":::

Itt megtekintheti a példány metrikáit, és létrehozhat egyéni nézeteket is.

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan lehet közvetlen kérelmeket készíteni az API-khoz a Poster használatával:
* [*Útmutató: kérelmek küldése a Poster használatával*](how-to-use-postman.md)

Vagy a .NET SDK-t a következő oktatóanyaggal rendelkező ügyfélalkalmazás létrehozásával használhatja:
* [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)