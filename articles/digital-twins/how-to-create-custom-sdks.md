---
title: Egyéni nyelvi SDK-k létrehozása az AutoRest segítségével
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre egyéni nyelvi AZURE DIGITAL TWINS-kódokat más nyelveken, amelyeken nincsenek közzétéve az AutoRest.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751111"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Egyéni nyelvi SDK-k létrehozása Azure Digital Twins AutoRest használatával

Ha olyan nyelven kell dolgoznia az Azure Digital Twins-val, amely nem rendelkezik közzétett Azure Digital Twins SDK-val, ez [a](how-to-use-apis-sdks.md)cikk bemutatja, hogyan hozhat létre saját SDK-t az AutoRest használatával a választott nyelven. 

A cikkben látható példák egy [](how-to-use-apis-sdks.md#overview-data-plane-apis)adatsík SDK-ját mutatják be, de ez a folyamat a vezérlősík [SDK-ját](how-to-use-apis-sdks.md#overview-control-plane-apis) is létrehozza.

## <a name="prerequisites"></a>Előfeltételek

AZ SDK létrehozásához először a következő beállításokat kell végrehajtania a helyi gépen:
* Telepítse [**az AutoRest**](https://github.com/Azure/autorest)2.0.4413-as verzióját (a 3-as verzió jelenleg nem támogatott)
* Telepítse [**Node.js,**](https://nodejs.org)amely az AutoRest használatának előfeltétele
* A [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Töltse le Azure Digital Twins **Swagger** (OpenAPI) legfrissebb adatsíkfájlját a () adatsík [Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)mappából, a hozzá tartozó példákat tartalmazó mappával együtt. A Swagger-fájl az,digitaltwins.js *a fájlban.*

>[!TIP]
> Ha inkább vezérlősík **SDK-t** hoz létre, az adatsík helyett a vezérlősík [Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) mappájában található legújabb **Swagger** (OpenAPI) fájllal kell végrehajtania a cikkben található lépéseket.

Miután a gépe fel van szerelve a fenti lista minden funkcióval, készen áll arra, hogy az AutoRest használatával létrehoz egy SDK-t.

## <a name="create-the-sdk-using-autorest"></a>Az SDK létrehozása az AutoRest használatával 

Miután telepítette Node.js, futtassa ezt a parancsot, és győződjön meg arról, hogy telepítve van az AutoRest szükséges verziója:
```cmd/sh
npm install -g autorest@2.0.4413
```

Az AutoRest a Azure Digital Twins Swagger-fájlon való futtatásához kövesse az alábbi lépéseket:
1. Másolja a Azure Digital Twins Swagger-fájlt és a hozzá tartozó példákat tartalmazó mappát egy munkakönyvtárba.
2. A parancssori ablakban váltson erre a munkakönyvtárra.
3. Futtassa az AutoRest parancsot a következő paranccsal. Cserélje le `<language>` a helyőrzőt a választott nyelvre: `python` , , , és így `java` `go` tovább. (A lehetőségek teljes listáját az [AutoRest README](https://github.com/Azure/autorest)fájlban találja.)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Ennek eredményeképpen egy *DigitalTwinsApi* nevű új mappa fog látni a munkakönyvtárban. A létrehozott SDK-fájlok a *DigitalTwinsApi névteret fogják tartalmazni.* Ezt a névteret továbbra is használni fogja a cikk további használati példáiban.

Az AutoRest számos nyelvkódgenerátort támogat.

## <a name="make-the-sdk-into-a-class-library"></a>Az SDK létrehozása osztálytárba

Az AutoRest által létrehozott fájlokat közvetlenül egy .NET-megoldásba foglalhatja. Valószínű azonban, hogy az Azure Digital Twins SDK-t több külön projektben is szeretné majd szerepelni (ügyfélalkalmazások, Azure Functions alkalmazások stb.). Emiatt hasznos lehet a létrehozott fájloktól különálló projektet (.NET-osztálytárat) összeépíteni. Ezután ezt az osztálytárprojektet projekthivatkozásként több megoldásba is beveheti.

Ez a szakasz útmutatást nyújt az SDK osztálytárként való felépítéséhez, amely egy saját projekt, és más projektekbe is belevehető. Ezek a lépések a **következőre Visual Studio.**

A lépések a következők:

1. Új Visual Studio létrehozása osztálytárhoz
2. Használja *a DigitalTwinsApi nevet* a projekt neveként
3. A Megoldáskezelő válassza ki a jobb gombot a létrehozott megoldás *DigitalTwinsApi* projektjére, és válassza az *Add > Existing Item... (Meglévő* elem hozzáadása... > lehetőséget.
4. Keresse meg azt a mappát, ahol az SDK-t generálta, és válassza ki a fájlokat a gyökérszinten
5. Nyomja le az "OK" gombot
6. Adjon hozzá egy mappát a projekthez (válassza ki a jobb Megoldáskezelő, majd válassza az Add > New Folder (Új *mappa hozzáadása) lehetőséget)*
7. A mappa elnevezése *Models (Modellek)*
8. Kattintson a jobb oldalon *a Modellek mappára* a Megoldáskezelő, és válassza az *Add > Existing Item... (Meglévő > hozzáadása... lehetőséget.*
9. Válassza ki a létrehozott SDK *Models* mappájában található fájlokat, majd nyomja le az "OK" gombot

Az SDK sikeres felépítéséhez a projektnek a következő referenciákra lesz szüksége:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Ezek hozzáadásához nyissa meg a *Tools > NuGet Csomagkezelő > Manage NuGet Packages for Solution... (NuGet-csomagok kezelése a megoldáshoz... ) gombra.*

1. Győződjön meg arról, hogy a *panelen* a Tallózás lap van kiválasztva
2. *Microsoft.Rest keresése*
3. Válassza ki a `ClientRuntime` és `ClientRuntime.Azure` a csomagot, és adja hozzá őket a megoldáshoz

Most már felépítheti a projektet, és felhasználhatja projekthivatkozásként bármely Azure Digital Twins írt alkalmazásba.

## <a name="tips-for-using-the-sdk"></a>Tippek az SDK használatához

Ez a szakasz általános információkat és irányelveket tartalmaz a létrehozott SDK használatával kapcsolatban.

### <a name="synchronous-and-asynchronous-calls"></a>Szinkron és aszinkron hívások

Minden SDK-függvény szinkron és aszinkron verzióban van.

### <a name="typed-and-untyped-data"></a>Típusos és nem típusos adatok

REST API hívások általában erősen típusos objektumokat ad vissza. Mivel azonban a Azure Digital Twins lehetővé teszi a felhasználók számára, hogy saját egyéni típusokat határozzanak meg az ikerhez, nincs lehetőség statikus visszaadott adatok előzetes definiálása számos Azure Digital Twins híváshoz. Ehelyett az API-k erős típusos burkolótípusokat adnak vissza, ha vannak, és az egyéni típusú ikeradatok Json.NET-objektumokban vannak (akkor használatosak, amikor az "objektum" adattípus megjelenik az API-aláírások között). Ezeket az objektumokat a kódban megfelelően is átveheti.

### <a name="error-handling"></a>Hibakezelés

Ha hiba történik az SDK-ban (beleértve a HTTP-hibákat is, például a 404-es hibát), az SDK kivételt jelez. Ezért fontos, hogy az összes API-hívást a try/catch blokkokbe kell beágyazni.

Itt egy kódrészlet, amely megpróbál hozzáadni egy ikereszközt, és a folyamat során hibákat fog fel:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Lapozás

Az AutoRest kétféle lapozási mintát hoz létre az SDK-hoz:
* Egy az összes API-hoz, kivéve a Query API-t
* Egy a Query API-hoz

A nem lekérdezési lapozási mintában az alábbi minta metódus mutatja be, hogyan lehet lekérni a kimenő kapcsolatok lapozható listáját a Azure Digital Twins:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

A második minta csak a Query API-hoz jön létre. Explicit módon használ `continuationToken` egy et.

>[!TIP]
> A lapok lehívásának egyik fő oka a Query [API-hívások](concepts-query-units.md) lekérdezési egységben fizetendő díjának kiszámítása.

Példa erre a mintára:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Következő lépések

Kövesse az ügyfélalkalmazások létrehozásához szükséges lépéseket, ahol használhatja az SDK-t:
* [*Oktatóanyag: Ügyfélalkalmazások kódba való bekód használata*](tutorial-code.md)
