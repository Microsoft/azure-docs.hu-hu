---
title: Egyéni SDK-k létrehozása az Azure Digital Twins-hoz az autorest szolgáltatással
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhatja ki az egyéni SDK-kat, hogy az Azure Digital Twins használatát a C#-tól eltérő nyelvekkel használja.
author: baanders
ms.author: baanders
ms.date: 4/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-js
ms.openlocfilehash: e0c0d18dbb3596733d02430554fd40ec16180c64
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980660"
---
# <a name="create-custom-sdks-for-azure-digital-twins-using-autorest"></a>Egyéni SDK-k létrehozása az Azure Digital Twins-hoz az autorest használatával

Jelenleg az Azure Digital Twins API-kkal való interakcióhoz az egyetlen közzétett adatsíkok-SDK a .NET (C#), a JavaScript és a Java. Ezekről az SDK-król és az API-król az [*útmutató: az Azure digitális Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md)című témakörben olvashat bővebben. Ha más nyelven dolgozik, ebből a cikkből megtudhatja, hogyan hozhatja ki saját adatközpont SDK-t az Ön által választott nyelven az autorest használatával.

>[!NOTE]
> Az autorest használatával is létrehozhatja a Control Plane SDK-t, ha szeretné. Ehhez hajtsa végre a jelen cikkben ismertetett lépéseket a [vezérlési sík hencegő mappájában](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) található, a vezérlő síkja **(OpenAPI** ) fájl használatával, az adatsík helyett.

## <a name="set-up-your-machine"></a>A gép beállítása

Az SDK létrehozásához a következőkre lesz szüksége:
* Az [autorest](https://github.com/Azure/autorest), a Version 2.0.4413 (3-as verzió jelenleg nem támogatott)
* [Node.js](https://nodejs.org) az autorest előfeltétele
* A legújabb Azure Digital Twins **adatsík hencegő** (OpenAPI) fájl az [adatsík hencegő mappájából](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)és a hozzájuk tartozó, példákat tartalmazó mappából.  Töltse le a hencegő fájlt *digitaltwins.json* és a saját helyi számítógépére.

Ha a gép a fenti listából mindent megtesz, készen áll az autorest használatára az SDK létrehozásához.

## <a name="create-the-sdk-with-autorest"></a>Az SDK létrehozása az autorest használatával 

Ha Node.js van telepítve, futtathatja ezt a parancsot, hogy ellenőrizze, van-e telepítve az autorest megfelelő verziója:
```cmd/sh
npm install -g autorest@2.0.4413
```

Az Azure digitális Twins hencegő fájlján az autorest futtatásához kövesse az alábbi lépéseket:
1. Másolja az Azure digitális Twins hencegő fájlját és a hozzá tartozó kapcsolódó mappát egy munkakönyvtárba.
2. Egy parancssori ablak használatával váltson át erre a munkakönyvtárra.
3. Futtassa az autorest parancsot a következő paranccsal. Cserélje le a `<language>` helyőrzőt az Ön által választott nyelvre: `python` ,, `java` `go` stb. (A beállítások teljes listáját megtalálja az [Autorest README fájlban](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=ADTApi --add-credentials --azure-arm --namespace=ADTApi
```

Ennek eredményeképpen egy új, *ADTApi* nevű mappa jelenik meg a munkakönyvtárban. A generált SDK-fájlok a névtér *ADTApi* lesznek. Ezt a névteret továbbra is használhatja a jelen cikkben található használati példákban.

Az autorest számos nyelvi kód generátort támogat.

## <a name="add-the-sdk-to-a-visual-studio-project"></a>Az SDK hozzáadása egy Visual Studio-projekthez

Az autorest által létrehozott fájlokat közvetlenül egy .NET-megoldásba is felveheti. Azonban valószínű, hogy az Azure digitális Twins SDK-t több különálló projektben (az ügyfélalkalmazások, Azure Functions alkalmazások stb.) szeretné felvenni. Emiatt hasznos lehet egy különálló projekt (.NET-osztály könyvtára) létrehozása a generált fájlokból. Ezt követően belefoglalhatja ezt az osztály-függvénytár-projektet több megoldásba projekt-hivatkozásként.

Ebből a szakaszból megtudhatja, hogyan hozhatja létre az SDK-t egy olyan osztály-függvénytárként, amely a saját projektje, és más projektekben is szerepelhet. Ezek a lépések a **Visual studióra** támaszkodnak ( [innen](https://visualstudio.microsoft.com/downloads/)telepítheti a legújabb verziót).

A lépések a következők:

1. Új Visual Studio-megoldás létrehozása az osztály könyvtára számára
2. A *ADTApi* használata projekt neveként
3. A Solutions Explorerben kattintson a jobb gombbal a generált megoldás *ADTApi* projektre, és válassza a *> meglévő elem hozzáadása..* . lehetőséget.
4. Keresse meg azt a mappát, ahová létrehozta az SDK-t, és válassza ki a legfelső szintű fájlokat.
5. Nyomja meg az "OK" gombot
6. Adjon hozzá egy mappát a projekthez (kattintson a jobb gombbal a projektre Megoldáskezelő, majd válassza a *> új mappa hozzáadása*) lehetőséget.
7. A mappa *modelljeinek* neve
8. Kattintson a jobb gombbal a *modellek* mappára a Solutions Explorerben, és válassza a *> meglévő elem hozzáadása..* . lehetőséget.
9. Válassza ki a generált SDK *models (modellek* ) mappájában található fájlokat, és nyomja meg az "OK" gombot.

Az SDK sikeres létrehozásához a projektnek ezekre a hivatkozásokra lesz szüksége:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Ezek hozzáadásához nyissa meg az *eszközök > NuGet Package Manager > NuGet-csomagok kezelése a megoldáshoz..*. lehetőséget.

1. A panelen ellenőrizze, hogy a *Tallózás* lap van-e kiválasztva.
2. Keressen rá a *Microsoft. Rest* kifejezésre
3. Válassza ki a `ClientRuntime` és a `ClientRuntime.Azure` csomagokat, és vegye fel őket a megoldásba

Most már felépítheti a projektet, és felhasználhatja azt projekt-referenciáként bármely olyan Azure digitális Twins-alkalmazásban, amelyet írsz.

## <a name="general-guidelines-for-generated-sdks"></a>A generált SDK-k általános iránymutatásai

Ez a szakasz a generált SDK használatának általános információit és irányelveit tartalmazza.

### <a name="synchronous-and-asynchronous-calls"></a>Szinkron és aszinkron hívások

Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

### <a name="typed-and-untyped-data"></a>Beírt és nem típusos adattípusok

REST API a hívások általában erősen beírt objektumokat adnak vissza. Mivel azonban az Azure Digital Twins lehetővé teszi, hogy a felhasználók definiálják a saját egyéni típusait az ikrek számára, nincs mód előre definiálni a statikus visszaküldési adatmennyiségeket számos Azure digitális Twins-híváshoz. Ehelyett az API-k határozottan gépelt burkoló típusokat adnak vissza, ahol alkalmazhatóak, és az egyéni típusú Twin-adat a Json.NET objektumokban van (amelyet a rendszer akkor használ, amikor az "Object" adattípus szerepel az API-aláírásokban). Ezeket az objektumokat a kódban megfelelően elvégezheti.

### <a name="error-handling"></a>Hibakezelés

Ha hiba lép fel az SDK-ban (beleértve a HTTP-hibákat (például 404), akkor az SDK kivételt jelez. Ezért fontos, hogy az összes API-hívást beágyazza a try/catch blokkon belül.

Íme egy kódrészlet, amely egy IKeret próbál felvenni, és a folyamat során hibákat észlel:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Lapozás

Az autorest két típusú lapozási mintát hoz létre az SDK számára:
* Egyet az összes API-hoz a lekérdezési API kivételével
* Egy a lekérdezési API-hoz

A nem lekérdezési lapozási mintában az alábbi példa azt szemlélteti, hogyan kérhető le az Azure Digital Twins kimenő kapcsolatainak lapozható listája:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

A második minta csak a lekérdezési API-hoz lett létrehozva. `continuationToken`Explicit módon használja.

>[!TIP]
> A lapok beolvasásának fő oka az, hogy kiszámítsa a lekérdezési [egység díjait](concepts-query-units.md) a lekérdezési API-hívásokhoz.

Íme egy példa a következő mintával:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Következő lépések

Végigvezeti egy ügyfélalkalmazás létrehozásának lépésein, ahol az SDK-t használhatja:
* [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)
