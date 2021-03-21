---
title: A Azure Cosmos DB díjszabási modellje
description: Ez a cikk ismerteti a Azure Cosmos DB díjszabási modelljét, valamint azt, hogy miként egyszerűsíthető a Cost Management és a Cost-tervezés.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 573fc4fac413ceed50246bc6fb8df1d9db021c94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247454"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Díjszabási modell az Azure Cosmos DB-ben
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure Cosmos DB díjszabási modellje leegyszerűsíti a költségkezelést és -tervezést. A Azure Cosmos DB az adatbázison elvégzett műveletekért és az adatok által felhasznált tárterületért kell fizetnie.

- **Adatbázis-műveletek**: az adatbázis-műveletekért felszámított módszer a használt Azure Cosmos-fiók típusától függ.

  - **Kiépített átviteli sebesség**: a [kiépített átviteli sebesség](set-throughput.md) (más néven fenntartott átviteli sebesség) nagy teljesítményt biztosít bármilyen méretben. Adja meg a [kérési egységek](request-units.md) másodpercenkénti számát (ru/s), és Azure Cosmos db a beállított átviteli sebesség biztosításához szükséges erőforrásokat. [Az átviteli sebességet egy adatbázison vagy tárolón is kiépítheti](set-throughput.md). A számítási feladatok igénye alapján bármikor fel-vagy lekicsinyítheti az átviteli sebességet, vagy használhatja az [autoskálázást](provision-throughput-autoscale.md) (bár az adatbázison vagy tárolón legalább egy minimális átviteli sebesség szükséges). A maximális kiosztott átviteli sebességért óradíjat számolunk fel egy adott órában.

   > [!NOTE]
   > Mivel a kiépített átviteli sebességi modell erőforrásokat rendel a tárolóhoz vagy az adatbázishoz, a kiosztott átviteli sebességért kell fizetnie, akkor is, ha nem futtat semmilyen munkaterhelést.

  - **Kiszolgáló** nélküli: [kiszolgáló](serverless.md) nélküli módban nem szükséges átviteli sebességet kiépíteni az Azure Cosmos-fiókban lévő erőforrások létrehozásakor. A számlázási időszak végén az adatbázis-műveletek által felhasznált kérelmek mennyiségét számoljuk fel.

- **Tárolás**: az adat-és indexek által az adott órában felhasznált teljes tárterület (GB-ban) átalány-díjait számítjuk fel. A tárterületet fogyasztás alapján számítjuk fel, így nem kell előzetesen lefoglalnia a tárterületet. Csak a felhasznált tárterületért kell fizetnie.

A Azure Cosmos DB díjszabási modellje konzisztens az összes API-ban. További információkért tekintse meg a [Azure Cosmos db díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/), amely ismerteti [a Azure Cosmos db számláját](understand-your-bill.md) , valamint [azt, hogy a Azure Cosmos db díjszabási modellje hogyan költséghatékony az ügyfelek számára](total-cost-ownership.md).

Ha az Egyesült államokbeli nem kormányzati régióba helyezi üzembe a Azure Cosmos DB-fiókját, akkor az adatbázis és a tároló-alapú átviteli sebesség minimális ára a kiépített átviteli sebesség. A kiszolgáló nélküli módban nincs minimális díj. A díjszabás a használt régiótól függ, a legfrissebb díjszabási információkért tekintse meg a [Azure Cosmos db díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="try-azure-cosmos-db-for-free"></a>Az Azure Cosmos DB ingyenes kipróbálása

A Azure Cosmos DB számos lehetőséget kínál a fejlesztők számára ingyenesen elérhetővé. Ezek a lehetőségek a következők:

* **Azure Cosmos db ingyenes** szinten: a Azure Cosmos db ingyenes szintje megkönnyíti az alkalmazások megkezdését, fejlesztését és tesztelését, vagy akár kisebb éles számítási feladatokat is futtathat ingyenesen. Ha az ingyenes szintet engedélyezi egy fiókon, az első 400 RU/s és 5 GB tárterület ingyenesen elérhető a fiók élettartama alatt. Az Azure-előfizetések esetében akár egy ingyenes szintű fiókot is beállíthat, és a fiók létrehozásakor be kell jelentkeznie. Első lépésként [hozzon létre egy új fiókot Azure Portal az ingyenes szintet engedélyezve](create-cosmosdb-resources-portal.md) , vagy használjon [ARM-sablont](./manage-with-templates.md#free-tier).

* **Ingyenes Azure-fiók**: az Azure [ingyenes szintet](https://azure.microsoft.com/free/) kínál, amely az első 30 nap során $200 az Azure-kreditekben, és korlátozott mennyiségű ingyenes szolgáltatást biztosít 12 hónapig. További információkért lásd az [ingyenes Azure-fiókot](../cost-management-billing/manage/avoid-charges-free-account.md) ismertető cikket. Azure Cosmos DB az ingyenes Azure-fiók része. Különösen Azure Cosmos DB esetében ez az ingyenes fiók 25 GB tárhelyet és 400 RU/s-t biztosít az egész évre kiosztott átviteli sebességhez.

* **Próbálja ki a Azure Cosmos db** ingyen: Azure Cosmos db az ingyenes fiókok kipróbálása Azure Cosmos db használatával korlátozott időt kínál. Létrehozhat egy Azure Cosmos DB fiókot, létrehozhat adatbázisokat és gyűjteményeket, és futtathat egy minta alkalmazást a rövid útmutatók és oktatóanyagok segítségével. A minta alkalmazást futtathatja egy Azure-fiókra való feliratkozás vagy a bankkártya használata nélkül is. [Próbálja ki a Azure Cosmos db ingyenes](https://azure.microsoft.com/try/cosmosdb/) ajánlatokat egy hónapig Azure Cosmos db a fiókját tetszőleges számú alkalommal megújíthatja.

* **Azure Cosmos db emulátor**: a Azure Cosmos db Emulator egy helyi környezetet biztosít, amely a Azure Cosmos db szolgáltatás fejlesztésére szolgál. Az emulátor díjmentesen és magas szintű megbízhatósággal érhető el a felhőalapú szolgáltatás számára. Az Azure Cosmos DB Emulator használatával helyileg fejlesztheti és tesztelheti alkalmazásait anélkül, hogy Azure-előfizetést hozna létre, vagy költségekkel kellene számolni. Alkalmazásait az emulátor helyi használatával fejlesztheti az éles környezetbe való bevezetésük előtt. Miután elégedett az alkalmazás működésével az emulátoron, átválthat a felhőben lévő Azure Cosmos DB-fiók használatára, és jelentősen megtakaríthatja a költségeket. További információ az emulátorról: [Azure Cosmos db használata fejlesztési és tesztelési](local-emulator.md) célból a további részletekért.

## <a name="pricing-with-reserved-capacity"></a>Díjszabás a fenntartott kapacitással

Azure Cosmos DB [fenntartott kapacitással](cosmos-db-reserved-capacity.md) pénzt takaríthat meg, ha a kiépített átviteli módot a Azure Cosmos db-erőforrások előfizetésével, akár egy évig, akár három évig is elvégezheti. Jelentős mértékben csökkentheti a költségeket egy-vagy hároméves előzetes kötelezettségvállalásokkal, és a normál díjszabáshoz képest 20-65%-os kedvezményt takaríthat meg. Azure Cosmos DB fenntartott kapacitással csökkentheti a költségeket a kiosztott átviteli sebesség (RU/s) előre fizetve egy évig vagy három évig, és kedvezményt kap a kiosztott átviteli sebességhez. 

A fenntartott kapacitás számlázási kedvezményt nyújt, és nincs hatással az Azure Cosmos DB-erőforrások futtatási állapotára. A fenntartott kapacitás minden API-hoz elérhető, amely magában foglalja az MongoDB, a Cassandra, az SQL, a Gremlin és az Azure-táblákat, valamint a világ minden régióját. A fenntartott kapacitást előre is megtudhatja, [ha Azure Cosmos db erőforrásokhoz fenntartott](cosmos-db-reserved-capacity.md) kapacitással rendelkezik, és fenntartott kapacitást vásárol a [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB-erőforrások költségeinek optimalizálásáról a következő cikkekben tájékozódhat:

* Tudnivalók a [fejlesztés és a tesztelés optimalizálásáról](optimize-dev-test.md)
* További információ [a Azure Cosmos db-számla megismeréséről](understand-your-bill.md)
* További információ az [átviteli sebesség optimalizálásáról](optimize-cost-throughput.md)
* További információ a [tárolási díjak optimalizálásáról](optimize-cost-storage.md)
* További információ [az olvasási és írási díjak optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések díjszabásának optimalizálásáról](./optimize-cost-reads-writes.md)
* További információ [a több régióból álló Cosmos-fiókok díjainak optimalizálásáról](optimize-cost-regions.md)
* További információ a [Azure Cosmos db fenntartott kapacitásról](cosmos-db-reserved-capacity.md)
* Tudnivalók a [Azure Cosmos db emulatorról](local-emulator.md)