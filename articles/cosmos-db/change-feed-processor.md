---
title: A változáscsatorna feldolgozói kódtára az Azure Cosmos DB használata
description: Használatával az Azure Cosmos DB-módosítási hírcsatorna feldolgozói kódtára.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 9cf9e1aabc0898ef025c7c2f517e631a812e67d7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969022"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Az Azure Cosmos DB processzor csatorna módosítása 

A [Azure Cosmos DB-módosítási hírcsatorna feldolgozói kódtára](sql-api-sdk-dotnet-changefeed.md) segít eseményfeldolgozás elosztása több ügyfél között. Ebben a könyvtárban olvasási módosítások egyszerűbbé teszi a partíciók és több szál párhuzamos használata.

Változáscsatorna feldolgozói könyvtárával fő előnye, hogy nem kell kezelni az egyes partíciók és a folytatási tokent, és nem rendelkezik a tárolók manuális lekérdezésére.

A változáscsatorna feldolgozói könyvtárával olvasási módosítások egyszerűbbé teszi a partíciók és több szál párhuzamos használata. A bérlet mechanizmussal partíciójára olvasási módosítások automatikusan kezeli. Ha két a változáscsatorna feldolgozói kódtára használó ügyfelek az alábbi képen, láthatjuk, ahogy azok a munkát egymás között osztja fel. Továbbra is növelheti az ügyfelek számát, mert a munkát egymás között, tartsa osztásával.

![Feldolgozói kódtára segítségével Azure Cosmos DB-módosítási hírcsatorna](./media/change-feed-processor/change-feed-output.png)

A bal oldali ügyfél első lett elindítva, és indulása, figyelés, az összes partíciót, majd a második ügyfél lett elindítva, majd az első lehetővé teszik a bérletek második ügyfélnek némelyikének nyissa meg. Ez az egy hatékony módszer, amelyek különböző gépek és az ügyfelek a munkát.

Ha két kiszolgáló nélküli Azure functions monitorozási ugyanazt a tárolót, és az azonos bérleti használatával, a két függvényt kaphat attól függően, hogy hogyan a feldolgozói kódtára úgy dönt, hogy a partíciók feldolgozása különböző dokumentumokon.

## <a name="implementing-the-change-feed-processor-library"></a>A változások hírcsatorna feldolgozói kódtára

A változáscsatorna feldolgozói kódtára végrehajtási négy fő összetevőből áll: 

1. **A figyelt tároló:** A figyelt tároló rendelkezik, amelyről a módosítási hírcsatorna jön létre, az adatokat. Bármely beszúrások és a figyelt tároló módosításait a módosítási hírcsatorna a tároló is megjelennek.

1. **A bérlet tároló:** A bérlet tároló koordinálja a változáscsatorna beolvasását feldolgozó módosítás feldolgozása. Egy külön tárolót a partíciónként egy bérlet a bérletek tárolásához használni kívánt szolgál. Célszerű a bérlet tároló tárolja az írási régió közelebb hol futnak a változáscsatorna processzor egy másik fiókot. A bérlet objektum tartalmazza a következő attribútumokat:

   * Tulajdonos: Adja meg a gazdagépet, amely a bérlet tulajdonosa.

   * Folytatási: Megadja a pozíció (folytatási kód) egy adott partíció a változáscsatorna.

   * Időbélyeg: Utoljára frissítve lett a bérlet; az időbélyeg segítségével ellenőrizze, hogy a bérlet lejárt számít.

1. **A processzor-gazdagép:** Minden gazdagépen határozza meg, hány partíciók feldolgozása alapján gazdagépek hány példánya van aktív bérleteket.

   * A gazdagép indításakor az összes gazdagép a terhelés elosztása érdekében a bérletek szerez be. Egy gazdagép rendszeres időközönként megújítja bérleteket, a bérletek aktív marad, így.

   * Egy gazdagép ellenőrzőpontokat olvassa el a legutóbbi folytatási token az egyes bérletét. A párhuzamosság biztonság érdekében, egy gazdagépet ellenőrzi az ETag bérleti frissítése. Ellenőrzőpont biztosító egyéb stratégiák is támogatottak.

   * Leállásakor egy gazdagép összes címbérlet-kiadások, de megőrzi a folytatási adatokat, így, folytathatja a tárolt ellenőrzőpont később olvasásakor.

   Jelenleg nem lehet nagyobb, mint a partíciók (bérleteket) gazdagépek száma.

1. **A fogyasztók számára:** A fogyasztók vagy dolgozó a változáscsatorna feldolgozást egyes állomások által indított hozzászóláslánc. Minden egyes processzor gazdagép több fogyasztó is rendelkezhet. Összes fogyasztóvédelmi olvassa be, a módosítás hírcsatorna oszlopát a partícióból hozzá van rendelve, és értesíti a változások gazdagépe és bérleteket lejárt.

Így jobban megismerheti az ezen négy módosítási hírcsatorna elemeinek processzor munkahelyi együtt, nézzük meg az alábbi ábrán egy példa. A figyelt gyűjteményhez tárolja a dokumentumokat, és használja a "City" partíciókulcsként. Láthatjuk, hogy a kék partíció "A-E" a "City" mezőt a dokumentumok tartalmazza, és így tovább. Nincsenek a négy partíció párhuzamosan olvasásakor két fogyasztóval rendelkező két gazdagép. A nyilak a fogyasztók a csatorna módosítása egy adott helyszínen olvasásakor. Az első partícióban a sötétebb kék olvasatlan módosítások jelöli, a világoskék jelöli a módosítási hírcsatorna megváltozik a már olvasása közben. A gazdagépek a bérletek gyűjteményének használni egy "folytatási" érték az aktuális olvasó pozíció minden felhasználóhoz nyomon követéséhez.

![Processzor példa csatorna módosítása](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Módosítási hírcsatorna és a létesített átviteli sebesség

Fogyasztott, mivel Cosmos tárolók kimenő és bemenő mindig Kérelemegységet fogyaszt díjkötelesek. A bérlet tároló által felhasznált Kérelemegységek díjkötelesek.

## <a name="additional-resources"></a>További források

* [Az Azure Cosmos DB változáscsatorna feldolgozói könyvtárával](sql-api-sdk-dotnet-changefeed.md)
* [Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [További példák a Githubon](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [Módosítási hírcsatorna áttekintése](change-feed.md)
* [A módosítási hírcsatornáról olvasási módjai](read-change-feed.md)
* [Az Azure Functions használatával módosítási hírcsatorna](change-feed-functions.md)
