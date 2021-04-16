---
title: Az Azure Digital Twins API-k és SDK-k használata
titleSuffix: Azure Digital Twins
description: Tekintse meg, hogyan dolgozhat a Azure Digital Twins API-okkal, beleértve az SDK-t is.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efa5061a49978ed5e7766c0e7bf9b56a1e73cf5d
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389757"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Az Azure Digital Twins API-k és SDK-k használata

Azure Digital Twins vezérlősík API-ját  és adatsík  API-ját is tartalmazza a példány és annak elemeinek kezeléséhez. 
* A vezérlősík API-k Azure Resource Manager [(ARM)](../azure-resource-manager/management/overview.md) API-k, és lefedik az erőforrás-kezelési műveleteket, például a példány létrehozását és törlését. 
* Az adatsík API-Azure Digital Twins API-k, és olyan adatkezelési műveletekhez használatosak, mint a modellek, ikerképek és a gráf kezelése.

Ez a cikk áttekintést nyújt az elérhető API-król és a velük való interakció módszereiről. A REST API-kat használhatja közvetlenül a társított Swaggerekkel (egy olyan eszközzel, mint a [Postman),](how-to-use-postman.md)vagy egy SDK-val.

## <a name="overview-control-plane-apis"></a>Áttekintés: vezérlősík API-k

A vezérlősík API-k olyan [ARM API-k,](../azure-resource-manager/management/overview.md) amelyek a Azure Digital Twins-példány egészének kezelésére használhatók, így olyan műveleteket fednek le, mint a teljes példány létrehozása vagy törlése. Ezeket végpontok létrehozására és törlésére is használni fogja.

A vezérlősík legújabb API-verziója _**a 2020-12-01.**_

A vezérlősík API-k használata:
* Az API-kat közvetlenül a vezérlősík Swagger-repo nevű legújabb Swagger mappájában [hívhatja meg.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable) Ez a mappa egy példákat tartalmazó mappát is tartalmaz, amely a használatot mutatja.
* A vezérlési API-khoz jelenleg a következő szolgáltatásokban férhet hozzá:
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([hivatkozás [automatikusan generált]](/dotnet/api/overview/azure/digitaltwins/management)) ([forrás](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([referencia [automatikusan generált]](/java/api/overview/azure/digitaltwins)) ([forrás](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([forrás](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([forrás](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([forrás](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

A vezérlősík API-ját úgy is gyakorolhatja, [](https://portal.azure.com) hogy a Azure Digital Twins a Azure Portal [cli-val lép kapcsolatba.](how-to-use-cli.md)

## <a name="overview-data-plane-apis"></a>Áttekintés: adatsík API-k

Az adatsík API-Azure Digital Twins API-k, amelyek a saját példányon belüli elemek Azure Digital Twins kezelésére. Ide tartoznak például az útvonalak létrehozása, a modellek feltöltése, a kapcsolatok létrehozása és az ikerkapcsolatok kezelése. Ezek széles körben a következő kategóriákra oszthatók:
* **DigitalTwinModels** – A DigitalTwinModels kategória API-kat tartalmaz a modellek kezeléséhez egy Azure Digital Twins példányban. [](concepts-models.md) A felügyeleti tevékenységek közé tartozik a DTDL-ben írt modellek feltöltése, ellenőrzése, lekérése és törlése.
* **DigitalTwins** – A DigitalTwins kategória tartalmazza azokat az API-kat, amelyek segítségével a fejlesztők digitális ikereket és azok kapcsolatait hozhatják létre, módosíthatják és törölhetik egy Azure Digital Twins példányban. [](concepts-twins-graph.md)
* **Lekérdezés** – A Lekérdezés kategóriában a fejlesztők digitális ikereszköz-készleteket találhatnak az [ikergráfban](how-to-query-graph.md) a kapcsolatok között.
* **Eseményútvonalak** – Az Eseményútvonalak [](concepts-route-events.md)kategória api-kat tartalmaz az adatok a rendszeren és az lefelé irányuló szolgáltatásokhoz való útválasztására.

Az adatsík legújabb API-verziója _**a 2020-10-31.**_

Az adatsík API-k használata:
* Az API-kat közvetlenül is meg lehet hívni...
   - hivatkozik az adatsík [Swagger-adattára legújabb Swagger mappára.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) Ez a mappa egy példákat tartalmazó mappát is tartalmaz, amely a használatot mutatja. 
   - az [API referenciadokumentációjának megtekintése.](/rest/api/azure-digitaltwins/)
* Használhatja a **.NET (C#) SDK-t.** A .NET SDK használata...
   - A csomagot megtekintheti és hozzáadhatja a NuGet: [Azure.DigitalTwins.Core ről.](https://www.nuget.org/packages/Azure.DigitalTwins.Core) 
   - az SDK [referenciadokumentációját.](/dotnet/api/overview/azure/digitaltwins/client)
   - Az SDK-forrást, beleértve a minták mappáját is, a GitHubon találja: [Az Azure IoT Digital Twins .NET-hez készült ügyféloldali kódtára.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) 
   - Részletes információkat és használati példákat talál, ha továbblép a [*cikk .NET (C#) SDK (adatsík)*](#net-c-sdk-data-plane) szakaszára.
* Használhatja a **Java SDK-t.** A Java SDK használata...
   - A csomagot megtekintheti és telepítheti a Mavenből: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - az SDK [referenciadokumentációját](/java/api/overview/azure/digitaltwins/client)
   - Az SDK-forrást a GitHubon találja: [Azure IoT Digital Twins Java-ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Használhatja a **JavaScript SDK-t.** A JavaScript SDK használata...
   - A csomagot megtekintheti és telepítheti az npm:Azure Azure Digital Twins Core JavaScript-ügyféloldali [kódtárából.](https://www.npmjs.com/package/@azure/digital-twins-core)
   - az SDK [referenciadokumentációját.](/javascript/api/@azure/digital-twins-core/)
   - Az SDK-forrást a GitHubon találja: [Azure Azure Digital Twins Core ügyféloldali kódtár JavaScripthez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* Használhatja a **Python SDK-t.** A Python SDK használata...
   - A csomagot a PyPi: Azure Azure Digital Twins Core Python ügyféloldali [kódtárából lehet megtekinteni és telepíteni.](https://pypi.org/project/azure-digitaltwins-core/)
   - az SDK [referenciadokumentációját.](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)
   - Az SDK-forrást a GitHubon találja: [Azure Azure Digital Twins Core ügyféloldali kódtár Pythonhoz](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* Az AutoRest használatával létrehozhat egy SDK-t egy másik nyelvhez. Kövesse az [*Útmutató: Egyéni SDK-k*](how-to-create-custom-sdks.md)létrehozása Azure Digital Twins AutoRest segítségével.

A dátumsík API-kat a CLI-Azure Digital Twins is [gyakorolhatja.](how-to-use-cli.md)

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (adatsík)

A Azure Digital Twins .NET (C#) SDK az Azure SDK for .NET része. Nyílt forráskódú, és az adatsík API Azure Digital Twins alapul.

> [!NOTE]
> Az SDK-tervezéssel kapcsolatos további információkért tekintse meg az [Azure SDK-k](https://azure.github.io/azure-sdk/general_introduction.html) általános tervezési alapelveit és a [.NET-tervezési irányelveket.](https://azure.github.io/azure-sdk/dotnet_introduction.html)

Az SDK-hoz foglalja bele az **Azure.DigitalTwins.Core** NuGet-csomagot a projektbe. Szüksége lesz az **Azure.Identity** csomag legújabb verziójára is. A Visual Studio a NuGet-csomagokkal (a Tools > Nu *Csomagkezelő > Get* és a NuGet-csomagok kezelése a megoldáshoz) Csomagkezelő hozzáadhatja ezeket a csomagokat. Másik lehetőségként használhatja a .NET parancssori eszközt az alábbi NuGet-csomaghivatkozások között található parancsokkal, hogy hozzáadja ezeket a projekthez:
* [**Azure.DigitalTwins.Core.**](https://www.nuget.org/packages/Azure.DigitalTwins.Core) Ez a [.NET-hez készült Azure Digital Twins SDK csomagja.](/dotnet/api/overview/azure/digitaltwins/client) 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Ez a kódtár eszközöket biztosít az Azure-beli hitelesítéshez.

Az API-k gyakorlati használatának részletes útmutatójért lásd: [*Oktatóanyag: Ügyfélalkalmazás megkódolva.*](tutorial-code.md) 

### <a name="net-sdk-usage-examples"></a>.NET SDK használati példák

Íme néhány kódminta, amelyek a .NET SDK használatát szemléltetik.

Hitelesítés a szolgáltatáson:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Modell feltöltése:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Modellek listása:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Ikereket hoz létre:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Ikereket kell lekérdezni, és végig kell hurkolni az eredményeken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

A [*mintaalkalmazás*](tutorial-code.md) kódját az Oktatóanyag: Ügyfélalkalmazás megkódolva útmutatója tartalmazza. 

További mintákat a [.NET (C#) SDK GitHub-adattárában is találhat.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)

#### <a name="serialization-helpers"></a>Szerializálási segítők

A szerializálási segítők az SDK-ban elérhető segítő függvények az ikeradatok gyors létrehozásához vagy de szerializálásához az alapvető információk eléréséhez. Mivel az alapvető SDK-metódusok alapértelmezés szerint JSON-ként adatokat ad vissza, hasznos lehet ezeket a segítő osztályokat használni az ikeradatok további lebontása érdekében.

A következő segítőosztályok érhetők el:
* `BasicDigitalTwin`: Általánosan egy digitális iker alapvető adatait jelöli
* `BasicDigitalTwinComponent`: Általánosan egy összetevőt képvisel egy `Contents``BasicDigitalTwin`
* `BasicRelationship`: Általánosan egy kapcsolat alapvető adatait jelöli
* `DigitalTwinsJsonPropertyName`: A JSON-szerializáláshoz és deszerializáláshoz használható sztring-konstansokat tartalmazza az egyéni digitális ikertípusokhoz

##### <a name="deserialize-a-digital-twin"></a>Digitális iker deserializálása

Az ikeradatokat mindig deserializálhatja a választott JSON-kódtár (például vagy `System.Text.Json` ) `Newtonsoft.Json` használatával. Az ikerhez való alapszintű hozzáféréshez a segítő osztályok ezt kényelmesebbé teszik.

A segítőosztály az ikereszközben definiált tulajdonságokhoz is hozzáférést biztosít `BasicDigitalTwin` egy `Dictionary<string, object>` segítségével. Az ikertulajdonságok listához a következőt használhatja:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` attribútumokat `System.Text.Json` használ. A DigitalTwinsClient ügyféllel való használathoz inicializálja az ügyfelet az alapértelmezett konstruktorral, vagy ha testre szeretné szabni a szerializáló beállítást, használja a `BasicDigitalTwin` [JsonObjectSerializert.](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true) [](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)

##### <a name="create-a-digital-twin"></a>Digitális iker létrehozása

A osztály `BasicDigitalTwin` használatával előkészítheti az adatokat egy ikerpéldány létrehozásához:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

A fenti kód a következő "manuális" változatnak felel meg:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Kapcsolat deserializálása

A kapcsolati adatokat mindig deserializálhatja egy ön által választott típusra. Egy kapcsolat alapszintű eléréséhez használja a `BasicRelationship` típust.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

A segítő osztály a kapcsolaton definiált tulajdonságokhoz is hozzáférést biztosít `BasicRelationship` egy `IDictionary<string, object>` segítségével. A tulajdonságok listához a következőt használhatja:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Kapcsolat létrehozása

A osztály `BasicRelationship` használatával előkészítheti az adatokat egy ikerpéldány kapcsolatainak létrehozásához:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Javítás létrehozása ikerfrissítéshez

Az ikerhívások és kapcsolatok frissítési hívásai [JSON-javítási struktúrát](http://jsonpatch.com/) használnak. A JSON Patch-műveletek listáinak létrehozásához az alább látható módon `JsonPatchDocument` használhatja a et.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Általános API-/SDK-használati megjegyzések

> [!NOTE]
> Vegye figyelembe, Azure Digital Twins nem támogatja az eredetközi **erőforrás-megosztást (CORS).** A hatásra és a megoldásra vonatkozó stratégiákkal kapcsolatos további információkért tekintse meg a Fogalmak: Biztonság a különböző Azure Digital Twins [*(CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) *című szakaszát.*

Az alábbi lista további részleteket és általános irányelveket tartalmaz az API-k és az API-k használatával kapcsolatosakhoz.

* Egy HTTP REST-tesztelési eszközzel, például a Postmannel közvetlen hívásokat kezdeményezhet a Azure Digital Twins API-khoz. További információ erről a folyamatról: [*How-to: Make requests with Postman (How-to: Kérések kérése a Postmannel).*](how-to-use-postman.md)
* Az SDK-hoz példányosodjon a `DigitalTwinsClient` osztály. A konstruktorhoz olyan hitelesítő adatokra van szükség, amelyek a csomagban található különböző hitelesítési módszerekkel szerezhetők `Azure.Identity` be. A-ről további információt `Azure.Identity` a [névtér dokumentációjában talál.](/dotnet/api/azure.identity) 
* Első lépésekben hasznosnak találhatja a használatát, de számos más lehetőség is rendelkezésre áll, például a felügyelt identitás hitelesítő adatai, amelyeket valószínűleg az MSI-val beállított Azure-függvények hitelesítésére fog használni a `InteractiveBrowserCredential` Azure Digital Twins. [](/dotnet/api/azure.identity.interactivebrowsercredential) [](../app-service/overview-managed-identity.md?tabs=dotnet) A-ről további információt `InteractiveBrowserCredential` a [osztálydokumentációjában talál.](/dotnet/api/azure.identity.interactivebrowsercredential)
* A Azure Digital Twins API-khoz való kéréshez olyan felhasználóra vagy szolgáltatásnévre van szükség, amely ugyanannak az [Azure Active Directory-](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD-) bérlőnek a része, amelyben a Azure Digital Twins példány található. Annak megakadályozása érdekében, hogy a hibás a szereplők URL-címeket szkennelnek be annak felderítéséhez, hogy hol találhatók Azure Digital Twins-példányok, a rendszer a 404-es Sub-Domain található" hibaüzenetet fogja visszaadni a kezdeményező bérlőn kívülről származó hozzáférési jogkivonatokkal. A rendszer akkor  is ezt a hibát fogja visszaadni, ha a felhasználó vagy szolgáltatásnév Azure Digital Twins adattulajdonos vagy Azure Digital Twins Adatolvasó szerepkört kapott az [Azure AD B2B együttműködésen](../active-directory/external-identities/what-is-b2b.md) keresztül.
* Minden szolgáltatási API-hívás tagfunkcióként van elérhetővé téve a `DigitalTwinsClient` osztályon.
* Minden szolgáltatásfunkció szinkron és aszinkron verzióban létezik.
* Minden szolgáltatásfunkció kivételt ad vissza a 400-as vagy magasabb visszatérési állapothoz. Ügyeljen arra, hogy a hívásokat egy szakaszba csomagolja, és legalább a `try` et `RequestFailedExceptions` elkapja. Az ilyen típusú kivételekkel kapcsolatos további információkért lásd [itt:](/dotnet/api/azure.requestfailedexception).
* A legtöbb szolgáltatás-metódus a vagy a ( metódust adja vissza az aszinkron hívásokhoz), ahol a a szolgáltatáshívás visszatérési `Response<T>` `Task<Response<T>>` `T` objektumának osztálya. A osztály a szolgáltatás visszatérési értékét és a visszatérési értékeket mutatja [`Response`](/dotnet/api/azure.response-1) be a `Value` mezőjében.  
* A lapszámált eredményekkel használt szolgáltatás-metódusok `Pageable<T>` vagy `AsyncPageable<T>` eredményt adnak vissza. A osztályról további információt itt, a -ről pedig itt `Pageable<T>` [](/dotnet/api/azure.pageable-1) `AsyncPageable<T>` tud [meg.](/dotnet/api/azure.asyncpageable-1)
* Az oldalakra lapozó eredményeket iterálhatja egy hurok `await foreach` használatával. A folyamattal kapcsolatos további információkért lásd [itt.](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)
* A mögöttes SDK a `Azure.Core` . Az [SDK-infrastruktúrával és](/dotnet/api/azure) -típusokkal kapcsolatos referenciaért tekintse meg az Azure-névtér dokumentációját.


A szolgáltatás metódusai lehetőség szerint erősen típusos objektumokat ad vissza. Mivel azonban Azure Digital Twins a felhasználó által futásidőben (a szolgáltatásba feltöltött DTDL-modelleken keresztül) egyénileg konfigurált modelleken alapulnak, számos szolgáltatási API veszi és adja vissza az ikeradatokat JSON formátumban.

## <a name="monitor-api-metrics"></a>API-metrikák monitorozása

Az API-metrikák, például a kérések, a késés és a hibaarány a következő [Azure Portal.](https://portal.azure.com/) 

A portál kezdőlapján keresse meg a Azure Digital Twins a részletek lekért példányát. Válassza a **Metrikák** lehetőséget a Azure Digital Twins menüjében a Metrikák *oldalának a kiválasztásához.*

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Képernyőkép a metrikák lapról a Azure Digital Twins":::

Itt megtekintheti a példánya metrikákat, és egyéni nézeteket hozhat létre.

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan lehet közvetlen kéréseket elküldeni az API-knak a Postman használatával:
* [*How-to: Requests with Postman (How-to: Kérések kérése a Postmannel)*](how-to-use-postman.md)

Vagy gyakorolhatja a .NET SDK-t egy ügyfélalkalmazás létrehozásával az oktatóanyag segítségével:
* [*Oktatóanyag: Ügyfélalkalmazások kódba való bekód használata*](tutorial-code.md)
