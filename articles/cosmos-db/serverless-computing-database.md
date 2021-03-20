---
title: Kiszolgáló nélküli adatbázis-számítástechnika Azure Cosmos DB és Azure Functions
description: Ismerje meg, hogyan használhatók együtt Azure Cosmos DB és Azure Functions az eseményvezérelt kiszolgáló nélküli számítástechnikai alkalmazások létrehozásához.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 73a34cc27eaba33d04f4d31585c7f494f58e7274
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334072"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Kiszolgáló nélküli adatbázis-számítástechnika Azure Cosmos DB és Azure Functions használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A kiszolgáló nélküli számítástechnika lényege, hogy az egyes, ismételhető és állapot nélküli logikára koncentráljon. Ezeknek a daraboknak nincs szükségük infrastruktúra-felügyeletre, és csak a másodpercben, vagy ezredmásodpercben használják az erőforrásokat. A kiszolgáló nélküli számítástechnikai mozgalom magja a függvények, amelyek az Azure-ökoszisztémában elérhetővé [Azure functions](https://azure.microsoft.com/services/functions). További információ az Azure-beli kiszolgáló nélküli végrehajtási környezetekről: [kiszolgáló nélküli az Azure-](https://azure.microsoft.com/solutions/serverless/) oldalon. 

A [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db) és Azure functions közötti natív integrációval adatbázis-eseményindítókat, bemeneti kötéseket és kimeneti kötéseket hozhat létre közvetlenül a Azure Cosmos db-fiókból. Azure Functions és Azure Cosmos DB használatával az eseményvezérelt, kiszolgáló nélküli alkalmazásokat hozhat létre és helyezhet üzembe, amelyek alacsony késésű hozzáférést biztosítanak a globális felhasználói bázisok gazdag adataihoz.

## <a name="overview"></a>Áttekintés

Azure Cosmos DB és Azure Functions lehetővé teszi az adatbázisok és kiszolgáló nélküli alkalmazások integrálását az alábbi módokon:

* Hozzon létre egy eseményvezérelt **Azure functions eseményindítót a Cosmos DBhoz**. Ez az aktiválás a hírcsatorna-adatfolyamok [módosítására](change-feed.md) támaszkodik, hogy figyelje az Azure Cosmos-tárolót a változásokhoz. Ha egy tárolóban bármilyen változást végez, a rendszer elküldi a változási adatfolyamot az triggernek, amely meghívja az Azure-függvényt.
* Azt is megteheti, hogy egy Azure-függvényt egy **bemeneti kötés** használatával kötést használ egy Azure Cosmos-tárolóhoz. A bemeneti kötések adatokat olvasnak be egy tárolóból egy függvény végrehajtásakor.
* Függvény kötése egy Azure Cosmos-tárolóhoz **kimeneti kötés** használatával. A kimeneti kötések adatokat írnak egy tárolóba, amikor egy függvény befejeződik.

> [!NOTE]
> Jelenleg a Cosmos DB esetében Azure Functions trigger, bemeneti kötések és kimeneti kötések támogatottak az SQL API-val való használatra. Az összes többi Azure Cosmos DB API esetében az API-hoz készült statikus ügyfél használatával kell elérnie az adatbázist a függvényből.


A következő ábra az alábbi három integrációt szemlélteti: 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Azure Cosmos DB és Azure Functions integrálás" border="false":::

A Azure Cosmos DBhoz tartozó Azure Functions trigger, bemeneti kötés és kimeneti kötés a következő kombinációkban használható:

* A Cosmos DB Azure Functions triggere egy másik Azure Cosmos-tárolóhoz tartozó kimeneti kötéssel is használható. Miután egy függvény végrehajt egy műveletet a változási hírcsatorna egyik elemén, megírhatja azt egy másik tárolóba (ugyanezt a tárolóba írhatja, amely valójában egy rekurzív hurok létrehozása). Másik lehetőségként használhat egy Azure Functions triggert is, amellyel a Cosmos DB hatékonyan áttelepítheti az összes módosított elemet egy tárolóból egy másik tárolóba, egy kimeneti kötés használatával.
* A Azure Cosmos DBhoz tartozó bemeneti kötések és kimeneti kötések ugyanabban az Azure-függvényben használhatók. Ez jól működik olyan esetekben, amikor bizonyos adatokat szeretne keresni a bemeneti kötéssel, módosítsa azt az Azure-függvényben, majd mentse azt ugyanabba a tárolóba vagy egy másik tárolóba a módosítás után.
* Az Azure Cosmos-tárolóhoz tartozó bemeneti kötések ugyanabban a függvényben használhatók, mint a Cosmos DB Azure Functions-triggere, és kimeneti kötéssel vagy anélkül is használhatók. Ezt a kombinációt használhatja a legfrissebb valutaváltó-információk (egy Exchange-tárolóba bevitt bemeneti kötés) a bevásárlókocsi-szolgáltatásban lévő új megrendelések változási csatornán való alkalmazásához. A frissített bevásárlókocsi összege a jelenlegi pénznem-konverzióval együtt egy harmadik tárolóba is írható egy kimeneti kötés használatával.

## <a name="use-cases"></a>Használati esetek

A következő használati esetek azt mutatják be, hogy miként hozhatja ki a legtöbbet a Azure Cosmos DB-adataival – az adatai az eseményvezérelt Azure Functionshoz való csatlakoztatásával.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT használati eset – Azure Functions trigger és kimeneti kötés a Cosmos DB

A IoT implementációjában egy függvényt hívhat meg, ha az ellenőrzési motor jelzőfénye megjelenik egy csatlakoztatott autóban.

**Implementáció:** Azure Functions trigger és kimeneti kötés használata Cosmos DB

1. A **Cosmos DB Azure functions triggere** az autókkal kapcsolatos riasztásokhoz kapcsolódó események elindítására szolgál, például egy csatlakoztatott autóban található ellenőrzési motorra.
2. Ha a vizsgálat motorjának fénye bekövetkezik, a rendszer elküldi az érzékelőre vonatkozó adatAzure Cosmos DB.
3. Azure Cosmos DB új érzékelő-adatdokumentumokat hoz létre vagy frissít, ezeket a módosításokat a rendszer a Cosmos DB Azure Functions triggerének továbbítja.
4. Az eseményindító minden adatváltozáskor meghívja az érzékelő adatgyűjtését, mivel az összes változás a változási csatornán keresztül áramlik.
5. A függvényben egy küszöbértéket használunk az érzékelői adat a jótállási osztályba való elküldéséhez.
6. Ha a hőmérséklet egy bizonyos értékre is vonatkozik, a rendszer riasztást is küld a tulajdonosnak.
7. A függvény **kimeneti kötése** frissíti az autótelefon egy másik Azure Cosmos-tárolóban lévő rekordját, hogy adatokat tároljon az ellenőrzési motor eseményéről.

Az alábbi képen az trigger Azure Portal írt kód látható.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Hozzon létre egy Azure Functions triggert a Cosmos DBhoz a Azure Portal":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Pénzügyi használati eset – időzítő trigger és bemeneti kötés

A pénzügyi megvalósításokban meghívhat egy függvényt, ha a bankszámla egyenlege egy bizonyos összeg alá esik.

**Implementáció:** Időzítő-trigger Azure Cosmos DB bemeneti kötéssel

1. Időzítő- [trigger](../azure-functions/functions-bindings-timer.md)használatával lekérheti az Azure Cosmos-tárolóban tárolt bankszámla-egyenleg adatait egy **bemeneti kötés** használatával.
2. Ha az egyenleg nem éri el a felhasználó által beállított alacsony egyenleg küszöbértéket, akkor az Azure-függvény egy műveletét követi nyomon.
3. A kimeneti kötés lehet egy [SendGrid-integráció](../azure-functions/functions-bindings-sendgrid.md) , amely e-mailt küld egy szolgáltatási fiókból az egyes alacsony egyenlegű fiókoknál azonosított e-mail-címekre.

Az alábbi képek a forgatókönyvben szereplő kódot mutatják a Azure Portal.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text=" Egy pénzügyi forgatókönyvhöz tartozó időzítő-triggerIndex.js fájlja":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="A. CSX fájl futtatása időzítő triggerhez pénzügyi forgatókönyv esetén":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Játék használati eset – Azure Functions trigger és kimeneti kötés a Cosmos DB 

A játékokban új felhasználó létrehozásakor megkeresheti azokat a felhasználókat, akik ismerik a [Azure Cosmos db GREMLIN API](graph-introduction.md)használatával. Ezután megírhatja az eredményeket egy Azure Cosmos DB vagy SQL Database-be az egyszerű lekéréshez.

**Implementáció:** Azure Functions trigger és kimeneti kötés használata Cosmos DB

1. Az összes felhasználó tárolásához egy Azure Cosmos DB [gráf-adatbázist](graph-introduction.md) hozhat létre, amely a Cosmos db Azure functions triggerrel is létrehozható. 
2. Új felhasználó beszúrásakor a rendszer meghívja a függvényt, majd az eredményt egy **kimeneti kötés** használatával tárolja.
3. A függvény lekérdezi a Graph-adatbázist, hogy megkeresse az összes olyan felhasználót, aki közvetlenül kapcsolódik az új felhasználóhoz, és visszaadja ezt az adatkészletet a függvénynek.
4. Ezeket az adatkészleteket a rendszer egy Azure Cosmos DB tárolja, amelyet aztán egyszerűen lekérhet bármely előtér-alkalmazás, amely megjeleníti az új felhasználót a csatlakoztatott barátokkal.

### <a name="retail-use-case---multiple-functions"></a>Kiskereskedelmi használati eset – több függvény

A kiskereskedelmi megvalósítások során, amikor egy felhasználó egy elemet helyez el a kosárba, mostantól rugalmasan hozhat létre és hívhat meg függvényeket a választható üzleti folyamat-összetevőkhöz.

**Implementáció:** Több Azure Functions eseményindító, amely egy tárolót figyel Cosmos DB

1. Több Azure Functions is létrehozhat, ha Azure Functions eseményindítókat ad hozzá Cosmos DBekhez, amelyek mindegyike ugyanazt a változási csatornát figyeli, mint a bevásárlókocsi-adatmennyiség. Vegye figyelembe, hogy ha több függvény figyel ugyanarra a változási csatornára, az egyes függvények esetében új címbérleti gyűjteményre van szükség. További információ a bérlet-gyűjteményekről: [a hírcsatorna-feldolgozó függvénytárának módosítása](change-feed-processor.md).
2. Amikor új elemet adnak hozzá egy felhasználói kosárhoz, az egyes függvények egymástól függetlenül meghívhatják a bevásárlókocsi-tároló változási csatornáját.
   * Az egyik függvény az aktuális kosár tartalmát használhatja más olyan elemek megjelenítésének módosításához, amelyekre a felhasználó kíváncsi lehet.
   * Egy másik függvény frissítheti a leltár összegeit.
   * Egy másik függvény információkat küldhet bizonyos termékekről a marketing részlegnek, akik egy promóciós levelezőt küldenek. 

     Bármely részleg létrehozhat egy Azure Functionst a Cosmos DB számára a változási csatornát figyelve, és biztos lehet abban, hogy nem késlelteti a kritikus sorrendben végzett feldolgozási eseményeket a folyamat során.

Az összes ilyen használati esetben, mivel a függvény elválasztta magát az alkalmazást, nem kell minden alkalommal új alkalmazás-példányokat felvennie. Ehelyett Azure Functions az egyes függvényeket a különálló folyamatok igény szerinti elvégzéséhez.

## <a name="tooling"></a>Eszközök

A Azure Cosmos DB és Azure Functions közötti natív integráció a Azure Portalban és a Visual Studio 2019-ben érhető el.

* A Azure Functions-portálon létrehozhat egy triggert. A gyors üzembe helyezési utasításokért lásd: [Azure functions trigger létrehozása Cosmos DBhoz a Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* A Azure Cosmos DB-portálon hozzáadhat egy Azure Functions triggert a Cosmos DBhoz egy meglévő Azure Function-alkalmazáshoz ugyanabban az erőforráscsoporthoz.
* A Visual Studio 2019-es verziójában a triggert a [Azure functions eszközök](../azure-functions/functions-develop-vs.md)használatával hozhatja létre:

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Miért érdemes Azure Functions integrációt választani a kiszolgáló nélküli számítástechnika számára?

A Azure Functions lehetővé teszi, hogy méretezhető munkaegységeket vagy olyan tömör logikát hozzon létre, amely igény szerint futtatható az infrastruktúra kiépítése vagy kezelése nélkül. Azure Functions használatával nem kell teljes körű alkalmazást létrehoznia ahhoz, hogy válaszoljon az Azure Cosmos-adatbázisban bekövetkezett változásokra, így bizonyos feladatokhoz kis mértékben újrafelhasználható függvények hozhatók létre. Emellett Azure Cosmos DB adatokat is használhatja bemenetként vagy kimenetként egy Azure-függvénynek, ha az eseményre, például egy HTTP-kérelemre vagy egy időzített eseményindítóra reagál.

A kiszolgáló nélküli számítási architektúra ajánlott adatbázisa a következő okok miatt Azure Cosmos DB:

* **Azonnali hozzáférés az összes adattal**: minden tárolt értékhez részletesen hozzáférhet, mivel Azure Cosmos db alapértelmezés szerint [automatikusan indexeli](index-policy.md) az összes adatokat, és azonnal elérhetővé teszi ezeket az indexeket. Ez azt jelenti, hogy folyamatosan kérdezheti, frissítheti és új elemeket adhat hozzá az adatbázishoz, és Azure Functionson keresztül azonnal hozzáférhet.

* **Séma** nélküli. A Azure Cosmos DB séma nélküli, így egyedi módon kezelhető az Azure-függvények adatkimenete. Ez a "bármit kezel" megközelítés egyszerűvé teszi a Azure Cosmos DB összes kimenetének számos funkciójának létrehozását.

* **Méretezhető átviteli sebesség**. Az átviteli sebesség azonnal, Azure Cosmos DBban is méretezhető. Ha több száz vagy több ezer függvényt kérdez le és ír ugyanarra a tárolóra, akkor a terhelés kezeléséhez felskálázást végezhet az [ru/s](request-units.md) használatával. Az összes függvény párhuzamosan dolgozhat a lefoglalt RU/s-vel, és az adatai [konzisztensek](consistency-levels.md).

* **Globális replikálás**. A késleltetés csökkentése érdekében replikálhatja Azure Cosmos DB adatait [a világ minden](distribute-data-globally.md) pontján, így a felhasználók a legközelebb eső helyre helyezhetik a helyüket. Ahogy az összes Azure Cosmos DB lekérdezés esetében, az eseményvezérelt eseményindítók adatait a rendszer a felhasználóhoz legközelebb eső Azure Cosmos DB olvassa be.

Ha integrálni szeretné a Azure Functionst az adattároláshoz, és nem kell részletes indexelést végeznie, vagy ha mellékleteket és médiafájlokat kell tárolnia, akkor az [Azure Blob Storage trigger](../azure-functions/functions-bindings-storage-blob.md) jobb megoldás lehet.

A Azure Functions előnyei: 

* **Eseményvezérelt**. Azure Functions az eseményvezérelt, és megfigyelheti a változási csatornát Azure Cosmos DB. Ez azt jelenti, hogy nem kell figyelő logikát létrehoznia, csak figyelje a figyelni kívánt módosításokat. 

* **Nincs korlát**. A függvények párhuzamosan futnak, és a szolgáltatás akár annyit is igénybe vesz, amennyire csak szüksége van. Beállítja a paramétereket.

* **Jó a gyors feladatokhoz**. A szolgáltatás új példányokat hajt végre a függvények során, amikor egy esemény bekövetkezik, és lezárja őket, amint a függvény befejeződik. Csak akkor kell fizetnie, amikor a függvények futnak.

Ha nem biztos abban, hogy a folyamat, a Logic Apps, a Azure Functions vagy a webjobs a legmegfelelőbb a megvalósításhoz, tekintse meg a következőt: [választás a flow, a Logic apps, a functions és a webjobs között](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Következő lépések

Most kapcsolódjon Azure Cosmos DB és Azure Functions a Realhoz: 

* [Hozzon létre egy Azure Functions triggert a Cosmos DBhoz a Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Azure Functions HTTP-trigger létrehozása Azure Cosmos DB bemeneti kötéssel](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Kötések és triggerek Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md)
