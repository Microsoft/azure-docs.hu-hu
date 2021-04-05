---
title: 'Oktatóanyag: Mobile-alkalmazások létrehozása a Xamarin és a Azure Cosmos DB'
description: 'Oktatóanyag: egy oktatóanyag, amely egy Xamarin iOS, Android vagy Forms alkalmazást hoz létre Azure Cosmos DB használatával. Az Azure Cosmos DB egy gyors, világméretű, felhőalapú adatbázis mobilalkalmazásokhoz.'
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: d1164c14b406fb66638e6a5333fe38180272aa14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359333"
---
# <a name="tutorial-build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>Oktatóanyag: Mobile-alkalmazások létrehozása a Xamarin és a Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

A legtöbb mobilalkalmazásnak felhőbeli adattárolásra van szüksége, az Azure Cosmos DB pedig egy felhőbeli adatbázis mobilalkalmazásokhoz. Mindent megtalál a mobil fejlesztői igények közül. Ez egy teljes körűen felügyelt, szolgáltatásként nyújtott adatbázis, amely igény szerint skálázható. Képes transzparens módon eljuttatni az adatokat az alkalmazáshoz, függetlenül attól, hogy a felhasználók a világ mely pontján tartózkodnak. Az [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) használatával lehetővé teszi, hogy a xamarinos mobilalkalmazások közvetlenül, középső réteg nélkül kommunikáljanak az Azure Cosmos DB-vel.

Ez a cikk egy oktatóanyagot tartalmaz mobilalkalmazások létrehozásához a Xamarin és az Azure Cosmos DB használatával. Az oktatóanyag teljes forráskódját megtalálja a [Xamarin és Azure Cosmos DB a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) oldalon, beleértve a felhasználók és engedélyek kezelését.

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>Az Azure Cosmos DB képességei mobilalkalmazásokhoz
Az Azure Cosmos DB az alábbi fő lehetőségeket nyújtja a mobilalkalmazás-fejlesztőknek:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-for-mobile.png" alt-text="Az Azure Cosmos DB képességei mobilalkalmazásokhoz":::

* Séma nélküli adatok részletes lekérdezése. Az Azure Cosmos DB az adatokat séma nélküli JSON-dokumentumok formájában tárolja, heterogén gyűjteményekben. [Részletes és gyors lekérdezéseket](./sql-query-getting-started.md) nyújt, amelyekhez nem kell sémákkal vagy indexekkel foglalkoznia.
* Gyors adatátvitel. Az Azure Cosmos DB mindössze néhány ezredmásodperc alatt végzi a dokumentumok írását és olvasását. A fejlesztők megadhatják a kívánt adatátviteli sebességet, amelyre vonatkozóan az Azure Cosmos DB 99,99%-os rendelkezésre állású SLA-t biztosít minden enyhén korlátozott konzisztenciájú egyrégiós és többrégiós fiókhoz, valamint 99,999%-os olvasási rendelkezésre állást minden többrégiós adatbázisfiókhoz.
* Korlátlan skálázhatóság. Az Azure Cosmos-tárolók az [alkalmazás növekedésével növekednek](partitioning-overview.md). Kezdetben a kis adatmennyiségek és a másodpercenkénti néhány száz kérés lehet a jellemző. A gyűjtemények vagy adatbázisok olyan mértékig növekedhetnek, hogy több petabájtnyi adatot tartalmazhatnak, és hatalmas mennyiségű, akár másodpercenként több százmillió kérést szolgálhatnak ki.
* Globálisan elosztott. A mobilalkalmazások felhasználói általában úton vannak, gyakran a világ legtávolabbi pontjai között. Az Azure Cosmos DB egy [globálisan elosztott adatbázis](distribute-data-globally.md). Kattintson a térképre, és tegye elérhetővé adatait a felhasználói számára.
* Beépített részletes hitelesítés. Az Azure Cosmos DB-vel könnyedén, összetett egyéni engedélyezési kódok használata nélkül implementálhat olyan népszerű mintákat, mint a [felhasználónkénti adatok](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems) vagy a többfelhasználós megosztott adatok.
* Térinformatikai lekérdezések. Napjainkban számos mobilalkalmazás nyújt földrajzi helyhez kötött funkciókat. A [térinformatikai típusok](./sql-query-geospatial-intro.md) kimagasló támogatásával az Azure Cosmos DB nagyban megkönnyíti az efféle élmények megteremtését.
* Bináris mellékletek. Az alkalmazásadatok gyakran tartalmaznak bináris blobokat. A mellékletek natív támogatásának köszönhetően az Azure Cosmos DB könnyebben használható teljes körűen az alkalmazásadatokhoz.

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB és Xamarin oktatóanyag
A következő oktatóanyag ismerteti, hogyan lehet létrehozni egy mobilalkalmazást a Xamarin és az Azure Cosmos DB használatával. Az oktatóanyag teljes forráskódját megtalálja a [Xamarin és Azure Cosmos DB a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) oldalon.

### <a name="get-started"></a>Bevezetés
Az Azure Cosmos DB használatát könnyű elsajátítani. Lépjen az Azure Portalra, és hozzon létre egy új Azure Cosmos DB-fiókot. Kattintson a **gyors üzembe helyezés** fülre. Töltse le a Xamarin űrlap-Feladatlista mintát, amely már csatlakoztatva van a Azure Cosmos DB fiókjához. 

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-quickstart.png" alt-text="Rövid útmutató: Az Azure Cosmos DB és mobilalkalmazások":::

Vagy ha már rendelkezik Xamarin-alkalmazással, hozzáadhatja az [Azure Cosmos DB NuGet-csomagot](sql-api-sdk-dotnet-core.md). A Azure Cosmos DB támogatja a Xamarin. iOS, az Xamarin. Android és a Xamarin űrlap megosztott kódtárait.

### <a name="work-with-data"></a>Adatok használata
Az Azure Cosmos DB az adatokat séma nélküli JSON-dokumentumokban tárolja, heterogén gyűjteményekben. Ugyanabban a gyűjteményben különböző struktúrájú dokumentumok is tárolhatók:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

A Xamarin-projektekben nyelvintegrált lekérdezéseket futtathat a séma nélküli adatokra vonatkozóan:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Felhasználók hozzáadása
Mint sok az első lépések mintája, a letöltött Azure Cosmos DB minta az alkalmazás kódjában lévő elsődleges kulcs hardcoded használatával hitelesíti a szolgáltatást. Az alapértelmezett eljárás használata nem célszerű, ha az alkalmazást a helyi emulátoron kívül bárhol máshol is futtatni kívánja. Ha egy jogosulatlan felhasználó megszerezte az elsődleges kulcsot, a rendszer a Azure Cosmos DB-fiók összes adatait feltörte. Ehelyett az ajánlott módszer az, ha az alkalmazás csak a bejelentkezett felhasználó rekordjaihoz fér hozzá. Az Azure Cosmos DB lehetővé teszi a fejlesztőknek, hogy az alkalmazásoknak olvasási vagy olvasási/írási engedélyeket adjanak egy gyűjteményre, egy partíciókulcs szerint csoportosított dokumentumkészletre vagy egy adott dokumentumra. 

Az alábbi lépésekkel módosítható a teendőlista alkalmazás egy többfelhasználós teendőlista alkalmazássá: 

  1. Az alkalmazáshoz hozzáadhat a Facebookon, Active Directoryn vagy bármely egyéb szolgáltatón keresztül történő bejelentkezési lehetőséget is.

  2. Hozzon létre egy Azure Cosmos DB UserItems gyűjteményt, amelynek partíciókulcsa **/felhasználóazonosító**. A gyűjtemény partíciókulcsának megadásával az Azure Cosmos DB-t végtelenül lehet skálázni az alkalmazás felhasználószámának növekedésével párhuzamosan, miközben továbbra is gyorsak maradnak a lekérdezések.

  3. Adja hozzá az Azure Cosmos DB erőforrásjogkivonat-közvetítőjét. Ez az egyszerű webes API hitelesíti a felhasználókat, és rövid élettartamú jogkivonatokat bocsát ki a bejelentkezett felhasználóknak, akik csak a saját partíciójukon belül tárolt dokumentumokhoz férhetnek hozzá. Ebben a példában az erőforrásjogkivonat-közvetítőt az App Services üzemelteti.

  4. Módosítsa az alkalmazást úgy, hogy az a Facebookon keresztül végezze az erőforrásjogkivonat-közvetítőbe való hitelesítést, és a bejelentkezett Facebook-felhasználók számára kérje le az erőforrás-jogkivonatokat. Ezt követően hozzáférhet a felhasználók adataihoz a UserItems gyűjteményben.  

A minta teljes mintakódját megtalálja az [Erőforrásjogkivonat-közvetítő a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems) oldalon. Az alábbi ábra illusztrálja a megoldást:

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png" alt-text="Az Azure Cosmos DB felhasználó- és engedélyközvetítője" border="false":::

Ha azt szeretné, hogy két felhasználó hozzáférjen ugyanahhoz a feladatlistához, akkor további engedélyeket adhat hozzá a hozzáférési jogkivonathoz az erőforrás-jogkivonat-átvitelszervezőben.

### <a name="scale-on-demand"></a>Igény szerinti méretezés
Az Azure Cosmos DB egy felügyelt, szolgáltatásként nyújtott adatbázis. Ahogy a felhasználóbázis növekszik, nem kell aggódnia a virtuális gépek üzembe helyezése vagy a magok számának növelése miatt. Mindössze meg kell adnia az Azure Cosmos DB-nek, hogy az alkalmazásának másodpercenként hány műveletre (mekkora átviteli sebességre) van szüksége. Az átviteli sebességet a **Skálázás** lapon adhatja meg, az alapján, hogy másodpercenként hány úgynevezett kérelemegységre van szüksége. Egy 1 KB-os dokumentum olvasási művelete például egy RU-t igényel. Emellett hozzáadhat riasztásokat az **átviteli** metrikához az adatforgalom növekedésének monitorozására, és a riasztások életbe lépésekor programozott módon módosíthatja az átviteli sebességet.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png" alt-text="Az Azure Cosmos DB átviteli sebességének igény szerinti skálázása":::

### <a name="go-planet-scale"></a>Váltson világszintre
Ahogy alkalmazása népszerűbbé válik, a világ minden tájáról szerezhet új felhasználókat. Vagy az is lehet, hogy csak fel szeretne készülni a váratlan eseményekre. Lépjen az Azure Portalra, és nyissa meg az Azure Cosmos DB-fiókját. Kattintson a térképre, ha szeretné, hogy adatai folyamatosan replikálva legyenek a világ bármely régiójában. Ez a funkció elérhetővé teszi az adatokat, függetlenül attól, hogy a felhasználók hol tartózkodnak. A váratlan helyzetekre való felkészültség érdekében hozzáadhat feladatátvételi szabályzatokat is.

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png" alt-text="Az Azure Cosmos DB skálázása több földrajzi régióra" border="false":::

Gratulálunk! Elkészítette a megoldást és a mobilalkalmazást a Xamarin és az Azure Cosmos DB segítségével. Hasonló lépésekkel hozhat létre Cordova-alkalmazásokat az Azure Cosmos DB JavaScript SDK-val, illetve natív iOS-/Android-alkalmazásokat az Azure Cosmos DB REST API-kkal.

## <a name="next-steps"></a>Következő lépések
* Forráskód megtekintése a [Xamarin és Azure Cosmos DB a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) oldalon.
* Az [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) letöltése.
* További kódminták [.NET-alkalmazásokhoz](sql-api-dotnet-samples.md).
* Az [Azure Cosmos DB részletes lekérdezési képességeinek](./sql-query-getting-started.md) ismertetése.
* Az [Azure Cosmos DB térinformatikai támogatásának](./sql-query-geospatial-intro.md) ismertetése.