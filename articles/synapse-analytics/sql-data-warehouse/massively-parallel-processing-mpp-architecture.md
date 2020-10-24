---
title: Azure szinapszis Analytics (korábban SQL DW) architektúrája
description: Ismerje meg, hogy az Azure szinapszis Analytics (korábbi nevén SQL DW) hogyan ötvözi az elosztott lekérdezés-feldolgozási képességeket az Azure Storage szolgáltatással a nagy teljesítmény és méretezhetőség érdekében.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1cb49fc33567b13065351a28a557232212c6adc4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92479340"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure szinapszis Analytics (korábban SQL DW) architektúrája

Az Azure Synapse egy korlátok nélküli elemzőszolgáltatás, amely egyesíti a vállalati adattárházakat és a Big Data-elemzéseket. Lehetővé teszi, hogy saját tetszőleges módon kérje le az adatokat, kiszolgáló nélküli igény szerinti vagy kiosztott erőforrásokkal, nagy mennyiségben. Az Azure Synapse egységes felületen egyesíti ezt a két területet az adatok betöltéséhez, előkészítéséhez, kezeléséhez és azonnali szolgáltatásához az üzleti intelligencia és gépi tanulási igények szerint.

 Az Azure szinapszis négy összetevőből áll:

- Szinapszis SQL: teljes T-SQL-alapú elemzés

  - SQL-készlet (fizetés/DWU kiépítve) – általánosan elérhető
  - Igény szerinti SQL-szolgáltatás (fizetés/TB feldolgozott) – (előzetes verzió)
- Spark: mélyen integrált Apache Spark (előzetes verzió)
- Adatintegráció: hibrid Adatintegráció (előzetes verzió)
- Studio: egyesített felhasználói élmény.  (Előzetes verzió)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Szinapszis SQL Architecture-összetevők

A [SZINAPSZIS SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) egy kibővített architektúrát használ a számítási folyamatok több csomóponton történő elosztására. A skála egysége az [adattárház-egységként](what-is-a-data-warehouse-unit-dwu-cdwu.md)ismert számítási teljesítmény absztrakciója. A számítás elkülönül a tárterülettől, ami lehetővé teszi a számítások egymástól független skálázását a rendszeren lévő adatoktól függetlenül.

![A Synapse SQL architektúrája](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

A szinapszis SQL egy node-alapú architektúrát használ. Az alkalmazások a T-SQL-parancsokat egy vezérlő csomóponthoz csatlakoznak, amely a szinapszis SQL beléptetésének egyetlen pontja. A vezérlő csomópont futtatja az elosztott lekérdezési motort, amely optimalizálja a párhuzamos feldolgozásra irányuló lekérdezéseket, majd a műveleteket a számítási csomópontokon továbbítja a munkájukat párhuzamosan.

A számítási csomópontok az összes felhasználói adatot az Microsoft Azure Storage-ban tárolják, és futtatják a párhuzamos lekérdezéseket. Az adatáthelyezési szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely szükség szerint áthelyezi az adatokat a csomópontok között a lekérdezések párhuzamos futtatásához és pontos eredmények visszaadásához.

A leválasztott tárolással és számítással a szinapszis SQL-készlet használatakor a következőket teheti:

- A tárolási igényektől függetlenül a számítási kapacitás egymástól függetlenül méretezhető.
- Az adatok áthelyezése nélkül növelheti vagy csökkentheti a számítási teljesítményt egy SQL-készleten (adatraktáron) belül.
- Szüneteltetheti a számítási kapacitást az adatok megőrzésével, hogy csak a tárterületért kelljen fizetnie.
- Működési időben újra aktiválhatja a számítási kapacitást.

### <a name="azure-storage"></a>Azure Storage

A szinapszis SQL kihasználja az Azure Storage-t a felhasználói adatai biztonságának megőrzése érdekében.  Mivel az Azure Storage tárolja és kezeli az adatait, külön díjat számítunk fel a tárterület-felhasználásért. Az adat felosztása a rendszer teljesítményének **optimalizálása érdekében történik** . Kiválaszthatja, hogy melyik horizontális Felskálázási mintát kell használnia az adatterjesztéshez a tábla meghatározásakor. Ezek a horizontális skálázási minták támogatottak:

- Kivonat
- Ciklikus időszeletelés
- Replikálás

### <a name="control-node"></a>Vezérlő csomópont

A vezérlő csomópont az architektúra agya. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az elosztott lekérdezési motor a vezérlő csomóponton fut a párhuzamos lekérdezések optimalizálása és koordinálása érdekében. Ha T-SQL-lekérdezést küld be, a vezérlő csomópont átalakítja azokat a lekérdezéseket, amelyek párhuzamosan futnak az egyes eloszlásokon.

### <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok biztosítják a számítási teljesítményt. A disztribúciók a számítási csomópontokat dolgozzák fel feldolgozásra. A további számítási erőforrásokért a disztribúciók újraképezhetők a rendelkezésre álló számítási csomópontokra. A számítási csomópontok száma 1 és 60 közötti tartományba esik, és a szinapszis SQL szolgáltatási szintje határozza meg.

Minden számítási csomóponthoz tartozik egy csomópont-azonosító, amely a rendszernézetekben látható. A számítási csomópont AZONOSÍTÓját úgy tekintheti meg, hogy megkeresi a node_id oszlopot a rendszernézetekben, amelyek nevei a sys.pdw_nodeskal kezdődnek. A rendszernézetek listáját a következő témakörben tekintheti meg: [SZINAPSZIS SQL rendszer nézetei](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="data-movement-service"></a>Adatáthelyezési szolgáltatás

Az adatátviteli szolgáltatás (DMS) az adatátviteli technológia, amely koordinálja az adatátvitelt a számítási csomópontok között. Egyes lekérdezések adatáthelyezést igényelnek annak biztosításához, hogy a párhuzamos lekérdezések pontos eredményeket állítsanak vissza. Ha adatáthelyezésre van szükség, a DMS biztosítja a megfelelő adatelérést a megfelelő helyre.

## <a name="distributions"></a>Disztribúciók

Az elosztás a tárolás és az elosztott adatokon futtatott párhuzamos lekérdezések feldolgozásának alapegysége. Ha a szinapszis SQL egy lekérdezést futtat, a munka 60 kisebb, párhuzamosan futó lekérdezésekre oszlik.

Az 60-es kisebb lekérdezések az egyik adateloszláson futnak. Minden számítási csomópont egy vagy több 60-disztribúciót kezel. A maximális számítási erőforrásokkal rendelkező SQL-készletekhez számítási csomópontok egyetlen eloszlással rendelkeznek. A minimális számítási erőforrásokkal rendelkező SQL-készletek egy számítási csomóponton lévő összes disztribúcióval rendelkeznek.  

## <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák

A kivonat alapján elosztott tábla nyújtja a legnagyobb lekérdezési teljesítményt az összekapcsolásoknál és aggregációknál nagy táblák esetén.

Ahhoz, hogy az adatokat egy kivonattal elosztott táblába lehessen osztani, a rendszer kivonatoló függvényt használ az egyes sorok egyetlen eloszláshoz való hozzárendeléséhez determinisztikus módon. A tábla definíciójában az oszlopok egyike elosztási oszlopként van megjelölve. A kivonatolási függvény az elosztási oszlop értékeit használja az egyes sorok elosztáshoz rendeléséhez.

Az alábbi ábra azt szemlélteti, hogyan történik a teljes (nem elosztott) táblázat tárolása kivonatként elosztott táblaként.

![Elosztott tábla](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Elosztott tábla")  

- Minden sor egy eloszláshoz tartozik.  
- A determinisztikus kivonatoló algoritmus az egyes sorokat egyetlen eloszláshoz rendeli.  
- A tábla sorainak száma eloszlása a különböző méretű táblákban látható módon változik.

A terjesztési oszlop kiválasztásához teljesítménnyel kapcsolatos szempontokat kell figyelembe venni, például a különbségtételt, az adattorzítást, valamint a rendszeren futó lekérdezések típusait.

## <a name="round-robin-distributed-tables"></a>Ciklikus időszeleteléssel elosztott táblák

A betöltések előkészítési táblája, amely gyors teljesítményt nyújt, és rövid idő alatt készíti el a táblázatot.

Ciklikus időszeleteléses elosztott tábla egyenletesen osztja el az adatokat a táblázatban, de minden további optimalizálás nélkül. A rendszer először véletlenszerűen választja ki a eloszlást, majd a sorok puffereit egymás utáni eloszláshoz rendeli. Az adatok a ciklikus időszeleteléses táblába gyorsan betölthetők, de a lekérdezési teljesítmény gyakran jobb a kivonatelosztott táblák esetében. A ciklikus időszeletelésű táblákhoz való csatlakozáshoz szükség van az adatelemzésre, ami további időt vesz igénybe.

## <a name="replicated-tables"></a>Replikált táblák

A kisméretű tábláknál a replikált táblák nyújtják a leggyorsabb lekérdezési teljesítményt.

A replikált tábla a tábla teljes másolatát gyorsítótárazza az egyes számítási csomópontokon. Ebből következően a replikál tábla esetében nincs szükség adatadásra a számítási csomópontok között az összekapcsolási vagy aggregációs művelet előtt. A replikált táblákat legjobban kisméretű táblákkal lehet kihasználni. További tárterületre van szükség, és az adatírás során felmerülő további terhelés, amely a nagyméretű táblákat nem praktikus formában hajtja végre.  

Az alábbi ábrán egy olyan replikált tábla látható, amely az első eloszlásban van gyorsítótárazva az egyes számítási csomópontokon.  

![Replikált tábla](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Replikált tábla")

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az Azure Szinapszisot, megtudhatja, hogyan [hozhat létre gyorsan SQL-készletet](create-data-warehouse-portal.md) , és hogyan [tölthető be a mintaadatok](load-data-from-azure-blob-storage-using-polybase.md). Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), amikor az új fogalmakkal ismerkedik. Vagy tekintse meg a többi Azure szinapszis-erőforrást.  

- [Ügyfelek sikertörténetei](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Funkciókérések](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
- [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html)
- [Stack Overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)
