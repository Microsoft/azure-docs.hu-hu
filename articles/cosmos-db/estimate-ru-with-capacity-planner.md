---
title: A költségek becslése a Azure Cosmos DB Capacity Planner használatával
description: A Azure Cosmos DB Capacity Planner segítségével megbecsülheti a szükséges átviteli sebességet (RU/s) és a számítási feladathoz kapcsolódó költségeket. Ez a cikk azt ismerteti, hogyan használható a Capacity Planner új verziója az átviteli sebesség és a szükséges díj megbecsléséhez.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 829fb28577fbb89c031a3b972d3ee5e04c935e9f
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435001"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>RU/s becslése a Azure Cosmos DB Capacity Planner használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Az Azure Cosmos-adatbázisok és-tárolók konfigurálása a számítási feladatokhoz megfelelő mennyiségű kiépített átviteli sebességgel vagy a [kérelmek egységével (ru/s)](request-units.md)a számítási feladatokhoz elengedhetetlen a költséghatékonyság és a teljesítmény optimalizálása. Ez a cikk azt ismerteti, hogyan használható a Azure Cosmos DB [Capacity Planner](https://cosmos.azure.com/capacitycalculator/) a szükséges ru/s-k és a számítási feladatok költséghatékonyságának becsléséhez. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Az átviteli sebesség és a költséghatékonyság becslése Azure Cosmos DB Capacity Planner használatával

A Capacity Planner két módban is használható.

|**Mód**  |**Leírás**  |
|---------|---------|
|Alapszintű|Gyors, magas szintű RU/s-t és költségbecslést biztosít. Ez a mód feltételezi, hogy az indexelési házirend, a konzisztencia és az egyéb paraméterek alapértelmezett Azure Cosmos DB beállításai. <br/><br/>Az alapszintű mód gyors, magas szintű becslést is használhat, ha Azure Cosmos DBon futó lehetséges számítási feladatot értékel ki.|
|Felsőfokú|Részletesebb RU/s és költségbecslés megadását teszi lehetővé a további beállítások finomhangolása érdekében – az indexelési házirend, a konzisztencia szintje és egyéb, a költségeket és az átviteli sebességet érintő paraméterek. <br/><br/>Ha új projektre vonatkozó RU/s-t becsül fel, vagy részletesebb becslést szeretne, használjon speciális módot. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Kiépített átviteli sebesség és a Cost alapszintű módban való kibecslése
Ha az alapszintű módban gyors becslést szeretne kapni a számítási feladathoz, navigáljon a [Capacity plannerhez](https://cosmos.azure.com/capacitycalculator/). Adja meg az alábbi paramétereket a munkaterhelés alapján: 

|**Bevitel**  |**Leírás**  |
|---------|---------|
|Régiók száma|Azure Cosmos DB az összes Azure-régióban elérhető. Válassza ki a munkaterheléshez szükséges régiók számát. A Cosmos-fiókkal tetszőleges számú régiót rendelhet hozzá. További részletekért tekintse meg a Azure Cosmos DB [globális terjesztését](distribute-data-globally.md) ismertető témakört.|
|Több régióba írt írások|Ha engedélyezi a [többrégiós írásokat](distribute-data-globally.md#key-benefits-of-global-distribution), az alkalmazás bármely Azure-régióba képes olvasni és írni. Ha letiltja a többrégiós írásokat, az alkalmazás egyetlen régióba írhat adatot. <br/><br/> Engedélyezze a többrégiós írásokat, ha olyan aktív-aktív számítási feladatra van szüksége, amely kis késleltetésű írásokat igényel különböző régiókban. Például egy olyan IOT számítási feladat, amely különböző régiókban lévő nagy mennyiségű adatbázisba ír adatot. <br/><br/> A többrégiós írások 99,999%-os olvasási és írási rendelkezésre állást garantálnak. A többrégiós írásokhoz több átviteli sebesség szükséges az egyetlen írási régióhoz képest. További információkért lásd: [az egyes és a többszörös írási régiókról szóló cikk, hogyan különböznek az RUs](optimize-cost-regions.md) .|
|Összes tárolt adatmennyiség (régiónként)|A GB-ban tárolt összes becsült adat egyetlen régióban.|
|Elem mérete|Az adatelem (például dokumentum) becsült mérete 1 KB-ról 2 MB-ra. |
|Olvasási gyakoriság/mp régiónként|A várt olvasási műveletek száma másodpercenként. |
|Írás/s régiónként|A várt írási műveletek száma másodpercenként. |

A szükséges adatok kitöltése után válassza a **számítás** lehetőséget. A **Költségbecslés** lapon a tárterület és a kiépített átviteli sebesség teljes költsége látható. A lap **Részletek megjelenítése** hivatkozását kiterjesztve megtekintheti az olvasási és írási kérelmekhez szükséges átviteli sebesség részletezését. Minden egyes mező értékének módosításakor válassza a **számítás** lehetőséget a becsült költségek újbóli kiszámításához. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="A Capacity Planner alapszintű üzemmódja":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Kiépített átviteli sebesség és a költségcsökkentés speciális mód használatával

A speciális mód lehetővé teszi további beállítások megadását, amelyek hatással vannak az RU/s becslésre. A beállítás használatához navigáljon a [Capacity plannerhöz](https://cosmos.azure.com/capacitycalculator/) , és jelentkezzen be az eszközre az Azure-hoz használt fiókkal. A bejelentkezési lehetőség a jobb oldali sarokban érhető el. 

A bejelentkezést követően további mezőket is láthat a mezőkhöz képest alapszintű módban. Adja meg a további paramétereket a munkaterhelés alapján. 

|**Bevitel**  |**Leírás**  |
|---------|---------|
|API|Azure Cosmos DB egy többmodelles és többplatformos szolgáltatás. Új munkaterhelések esetén válassza az SQL (mag) API elemet. |
|Régiók száma|Azure Cosmos DB az összes Azure-régióban elérhető. Válassza ki a munkaterheléshez szükséges régiók számát. A Cosmos-fiókkal tetszőleges számú régiót rendelhet hozzá. További részletekért tekintse meg a Azure Cosmos DB [globális terjesztését](distribute-data-globally.md) ismertető témakört.|
|Több régióba írt írások|Ha engedélyezi a [többrégiós írásokat](distribute-data-globally.md#key-benefits-of-global-distribution), az alkalmazás bármely Azure-régióba képes olvasni és írni. Ha letiltja a többrégiós írásokat, az alkalmazás egyetlen régióba írhat adatot. <br/><br/> Engedélyezze a többrégiós írásokat, ha olyan aktív-aktív számítási feladatra van szüksége, amely kis késleltetésű írásokat igényel különböző régiókban. Például egy olyan IOT számítási feladat, amely különböző régiókban lévő nagy mennyiségű adatbázisba ír adatot. <br/><br/> A többrégiós írások 99,999%-os olvasási és írási rendelkezésre állást garantálnak. A többrégiós írásokhoz több átviteli sebesség szükséges az egyetlen írási régióhoz képest. További információkért lásd: [az egyes és a többszörös írási régiókról szóló cikk, hogyan különböznek az RUs](optimize-cost-regions.md) .|
|Alapértelmezett konzisztencia|A Azure Cosmos DB 5 konzisztencia-szintet támogat, hogy a fejlesztők egyensúlyt tegyenek a konzisztencia, a rendelkezésre állás és a késések közötti kompromisszumok között. További információt a [konzisztencia-szintek](consistency-levels.md) című cikkben talál. <br/><br/> Alapértelmezés szerint a Azure Cosmos DB a munkamenet konzisztenciáját használja, ami garantálja a saját írások olvasását egy munkamenetben. <br/><br/> Az erős vagy határos elavulás kiválasztásához a szükséges RU/s-k megadására van szükség a munkamenethez, a konzisztens előtaghoz és a végleges konzisztenciahez képest. A többrégiós írásokkal való erős konzisztencia nem támogatott, és a rendszer automatikusan az egyrégiós írásokat erős konzisztencia használatával veszi alapul. |
|Indexelési szabályzat|Alapértelmezés szerint a Azure Cosmos DB [indexeli az összes tulajdonságot](index-policy.md) a rugalmas és hatékony lekérdezésekhez (az **automatikus** indexelési házirendhez tartozó térképeket). <br/><br/> Ha a **ki** lehetőséget választja, a tulajdonságok egyike sem indexelt. Ennek eredményeképpen a legalacsonyabb RU díjat számítjuk fel az írásokhoz. Válassza **ki a szabályzatot** , ha csak a [pontok olvasását](/dotnet/api/microsoft.azure.cosmos.container.readitemasync) (kulcs értékének keresése) és/vagy írásokat kívánja végrehajtani, és nincsenek lekérdezések. <br/><br/> Az egyéni indexelési házirend lehetővé teszi, hogy az indexből bizonyos tulajdonságokat tartalmazzon vagy kizárjon az alacsonyabb írási sebesség és a tárterület számára. További információ: az [indexelési házirend](index-overview.md) és a [minta indexelési szabályzatok](how-to-manage-indexing-policy.md#indexing-policy-examples) cikkei.|
|Összes tárolt adatmennyiség (régiónként)|A GB-ban tárolt összes becsült adat egyetlen régióban.|
|Munkaterhelés mód|Válassza a **stabil** lehetőséget, ha a munkaterhelés mennyisége állandó. <br/><br/> Válassza a **változó** lehetőséget, ha a munkaterhelés mennyisége az idő múlásával változik.  Például egy adott nap vagy egy hónap során. <br/><br/> A következő beállítások érhetők el, ha a változó munkaterhelés beállítást választja:<ul><li>Az idő százalékos aránya: a hónap azon hányada, amelyben a számítási feladathoz a csúcs (legmagasabb) átviteli sebesség szükséges. <br/><br/> Ha például olyan számítási feladattal rendelkezik, amely magas aktivitású munkaidőben, 9 – 6 napos munkaidő alatt van, akkor a csúcson az idő százalékos aránya: 45 óra a csúcs/730 órában/hónapban = ~ 6%.<br/><br/></li><li>A másodpercenkénti olvasási sebesség (olvasás/mp) a várhatóan másodpercenkénti olvasási értéknél.</li><li>Írás/mp régiónként – a várható írási idő másodpercenkénti száma.</li><li>Beolvasások másodpercenkénti száma/s/mp – az elvárt olvasási sebesség másodpercenként a kiinduló csúcson.</li><li>Írás/mp/régió – kikapcsolt érték – a várhatóan másodpercenkénti írási idő.</li></ul>A csúcs-és az off-Peak intervallumokkal optimalizálhatja a költségeket a [kiépített átviteli sebesség programozott módon történő méretezésével](set-throughput.md#update-throughput-on-a-database-or-a-container) .|
|Elem mérete|Az adatelem (például dokumentum) mérete 1 KB és 2 MB között lehet. <br/><br/>A **minta-(JSON-)** dokumentumok pontosabb becslést is feltölthetnek.<br/><br/>Ha a munkaterhelés több típusú elemet tartalmaz (különböző JSON-tartalommal) ugyanabban a tárolóban, több JSON-dokumentumot is feltölthet, és lekérheti a becslést. Az **új elem hozzáadása** gomb használatával több minta JSON-dokumentumot is hozzáadhat.|

Az aktuális becslést tartalmazó CSV-fájl letöltéséhez használhatja a **becslés mentése** gombot is. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="A Capacity Planner speciális módja":::

A Azure Cosmos DB Capacity Planner által megjelenő díjak becslése az átviteli sebesség és a tárterület nyilvános díjszabási díja alapján történik. Az összes ár az USA dollárban jelenik meg. Az összes díjszabás régiónként való megjelenítéséhez tekintse meg a [Azure Cosmos db díjszabási oldalát](https://azure.microsoft.com/pricing/details/cosmos-db/) .  

## <a name="estimating-throughput-for-queries"></a>A lekérdezések teljesítményének becslése

Az Azure Cosmos Capacity kalkulátor feltételezi a pontok olvasását (egyetlen elem olvasása, például dokumentum, azonosító és partíciós kulcs értéke alapján), valamint a számítási feladatra vonatkozó írások. A lekérdezésekhez szükséges átviteli sebesség kiszámításához futtassa a lekérdezést egy Cosmos-tárolóban lévő reprezentatív adatkészleten, és [szerezze be az ru díját](find-request-unit-charge.md). Szorozza meg az RU-díjat a másodpercenként futtatandó lekérdezések számával, hogy megkapja a teljes RU/mp-t. 

Ha például a számítási feladathoz lekérdezésre van szükség, ``SELECT * FROM c WHERE c.id = 'Alice'`` amely másodpercenként 100-szor fut, és a lekérdezés ru-díja 10 RUs, akkor a kérelmek kiszolgálásához a 100 lekérdezési/mp * 10 ru/Query = 1000 ru/s érték szükséges. Adja hozzá ezeket az RU/s-ket a számítási feladathoz tartozó olvasások vagy írások esetében szükséges RU/s értékhez.

## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Cosmos db díjszabási modelljéről](how-pricing-works.md).
* Hozzon létre egy új [Cosmos-fiókot,-adatbázist és-tárolót](create-cosmosdb-resources-portal.md).
* Útmutató a [kiépített átviteli sebesség optimalizálásához](optimize-cost-throughput.md).
* Megtudhatja, hogyan [optimalizálhatja a költségeket a fenntartott kapacitással](cosmos-db-reserved-capacity.md).