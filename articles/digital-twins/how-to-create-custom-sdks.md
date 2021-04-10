---
title: Egyéni nyelvi SDK-k létrehozása autorest-tel
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan hozhatja ki az egyéni nyelvi SDK-kat az autorest használatával, ha más nyelveken is szeretne Azure digitális Twins-kódokat írni, amelyek nem rendelkeznek közzétett SDK-val.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561840"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Egyéni nyelvi SDK-k létrehozása az Azure Digital Twins-hoz az autorest használatával

Ha olyan nyelvvel kell dolgoznia az Azure Digital Twins-vel, amely nem rendelkezik [közzétett Azure digitális Twins SDK](how-to-use-apis-sdks.md)-val, akkor ez a cikk bemutatja, hogyan hozhatja ki saját SDK-t az Ön által választott nyelven az autorest használatával. 

Az ebben a cikkben szereplő példák egy [adatplant SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)létrehozását mutatják be, de ez a folyamat a  [Control Plant SDK](how-to-use-apis-sdks.md#overview-control-plane-apis) létrehozásához is működni fog.

## <a name="prerequisites"></a>Előfeltételek

Az SDK létrehozásához először el kell végeznie a következő beállításokat a helyi gépen:
* Az [**autorest**](https://github.com/Azure/autorest), a Version 2.0.4413 (3-as verzió) telepítése jelenleg nem támogatott.
* Telepítse a [**Node.jst**](https://nodejs.org), amely az automatikus Rest használatának előfeltétele
* A [ **Visual Studio** telepítése](https://visualstudio.microsoft.com/downloads/)
* Töltse le a legújabb Azure Digital Twins **adatsík hencegő** (OpenAPI) fájlt az [adatsík hencegő mappájából](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins), valamint a hozzá tartozó, példákkal ellátott mappáját. A hencegő fájl neve *digitaltwins.json*.

>[!TIP]
> Ha ehelyett a **Control** PLANt SDK-t szeretné létrehozni, hajtsa végre a jelen cikkben ismertetett lépéseket a [vezérlési sík hencegő mappájában](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) található, a vezérlő síkja **(OpenAPI** ) fájl használatával az adatsík helyett.

Ha a gép a fenti listából mindent megtesz, készen áll az autorest használatára az SDK létrehozásához.

## <a name="create-the-sdk-using-autorest"></a>Az SDK létrehozása az autorest használatával 

A Node.js telepítése után futtathatja ezt a parancsot, hogy ellenőrizze, hogy rendelkezik-e az autorest szükséges verziójának telepítésével:
```cmd/sh
npm install -g autorest@2.0.4413
```

Az Azure digitális Twins hencegő fájlján az autorest futtatásához kövesse az alábbi lépéseket:
1. Másolja az Azure digitális Twins hencegő fájlját és a hozzá tartozó kapcsolódó mappát egy munkakönyvtárba.
2. Egy parancssori ablak használatával váltson át erre a munkakönyvtárra.
3. Futtassa az autorest parancsot a következő paranccsal. Cserélje le a `<language>` helyőrzőt az Ön által választott nyelvre: `python` ,, `java` `go` stb. (A beállítások teljes listáját megtalálja az [Autorest README fájlban](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Ennek eredményeképpen egy új, *DigitalTwinsApi* nevű mappa jelenik meg a munkakönyvtárban. A generált SDK-fájlok a névtér *DigitalTwinsApi* lesznek. Ezt a névteret továbbra is használhatja a jelen cikkben található használati példákban.

Az autorest számos nyelvi kód generátort támogat.

## <a name="make-the-sdk-into-a-class-library"></a>Az SDK integrálása egy osztály-tárba

Az autorest által létrehozott fájlokat közvetlenül egy .NET-megoldásba is felveheti. Azonban valószínű, hogy az Azure Digital Twins SDK-t több különálló projektben (az ügyfélalkalmazások, Azure Functions alkalmazások és egyebek) is szeretné használni. Emiatt hasznos lehet egy különálló projekt (.NET-osztály könyvtára) létrehozása a generált fájlokból. Ezt követően belefoglalhatja ezt az osztály-függvénytár-projektet több megoldásba projekt-hivatkozásként.

Ebből a szakaszból megtudhatja, hogyan hozhatja létre az SDK-t egy olyan osztály-függvénytárként, amely a saját projektje, és más projektekben is szerepelhet. Ezek a lépések a **Visual studióra** támaszkodnak.

A lépések a következők:

1. Új Visual Studio-megoldás létrehozása az osztály könyvtára számára
2. A *DigitalTwinsApi* használata projekt neveként
3. A Solutions Explorerben kattintson a jobb gombbal a generált megoldás *DigitalTwinsApi* projektre, és válassza a *> meglévő elem hozzáadása..* . lehetőséget.
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

## <a name="tips-for-using-the-sdk"></a>Tippek az SDK használatához

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
