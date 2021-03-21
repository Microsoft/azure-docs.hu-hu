---
title: A Synapse SQL architektúrája
description: Ismerje meg, hogy az Azure szinapszis SQL hogyan ötvözi az elosztott lekérdezés-feldolgozási képességeket az Azure Storage szolgáltatással a nagy teljesítmény és méretezhetőség érdekében.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 28940272d39a08d790fe2cd913df808b02e7f426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441890"
---
# <a name="azure-synapse-sql-architecture"></a>Az Azure Synapse SQL architektúrája 

Ez a cikk a szinapszis SQL architektúrájának összetevőit ismerteti.

## <a name="synapse-sql-architecture-components"></a>Szinapszis SQL Architecture-összetevők

A szinapszis SQL egy kibővíthető architektúrát használ a számítási folyamatok több csomóponton történő elosztásához. A számítás elkülönül a tárterülettől, ami lehetővé teszi a számítások egymástól független skálázását a rendszeren lévő adatoktól függetlenül. 

A dedikált SQL-készlet esetében a skála egysége az [adattárház-egységként](resource-consumption-models.md)ismert számítási teljesítmény absztrakciója. 

A kiszolgáló nélküli SQL-készlet esetén a kiszolgáló nélküli méretezés automatikusan történik a lekérdezési erőforrások követelményeinek kielégítése érdekében. Mivel a topológia az idő múlásával módosul a csomópontok vagy feladatátvételek hozzáadásával, eltávolításával, alkalmazkodik a változásokhoz, és gondoskodik arról, hogy a lekérdezés elegendő erőforrással rendelkezik, és sikeresen befejeződik. Az alábbi képen például egy 4 számítási csomópontot használó kiszolgáló nélküli SQL-készlet látható a lekérdezés végrehajtásához.

![A Synapse SQL architektúrája](./media//overview-architecture/sql-architecture.png)

A szinapszis SQL egy node-alapú architektúrát használ. Az alkalmazások a T-SQL-parancsokat egy vezérlő csomóponthoz csatlakoznak, amely a szinapszis SQL beléptetésének egyetlen pontja. 

Az Azure szinapszis SQL-vezérlő csomópontja elosztott lekérdezési motort használ a párhuzamos feldolgozásra irányuló lekérdezések optimalizálásához, majd a műveleteket a számítási csomópontok számára továbbítja a munkájukat párhuzamosan. 

A kiszolgáló nélküli SQL-készlet vezérlési csomópontja elosztott lekérdezés-feldolgozási (DQP) motort használ a felhasználói lekérdezések elosztott végrehajtásának optimalizálására és összehangolására azáltal, hogy a számítási csomópontokon végrehajtandó kisebb lekérdezésekre osztja fel azokat. A rendszer minden kis lekérdezést feladatnak nevez, és elosztott végrehajtási egységet jelöl. Beolvassa a fájl (oka) t a tárolóból, összekapcsolja a más feladatokból beolvasott más tevékenységekkel, csoportokkal vagy rendelésekkel kapcsolatos adatokat. 

A számítási csomópontok az összes felhasználói adatot az Microsoft Azure Storage-ban tárolják, és futtatják a párhuzamos lekérdezéseket. Az adatáthelyezési szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely szükség szerint áthelyezi az adatokat a csomópontok között a lekérdezések párhuzamos futtatásához és pontos eredmények visszaadásához. 

A leválasztott tárolással és számítással a szinapszis SQL egyik használata a számítási kapacitás független méretezését is kihasználhatja a tárolási igényektől függetlenül. A kiszolgáló nélküli SQL-készlet skálázása automatikusan történik, míg a dedikált SQL-készlethez a következőket teheti:

* Az adatok áthelyezése nélkül növelheti vagy csökkentheti a számítási teljesítményt egy dedikált SQL-készleten belül.
* Szüneteltetheti a számítási kapacitást az adatok megőrzésével, hogy csak a tárterületért kelljen fizetnie.
* Működési időben újra aktiválhatja a számítási kapacitást.

## <a name="azure-storage"></a>Azure Storage

A szinapszis SQL kihasználja az Azure Storage-t a felhasználói adatai biztonságának megőrzése érdekében. Mivel az Azure Storage tárolja és kezeli az adatait, külön díjat számítunk fel a tárterület-felhasználásért. 

A kiszolgáló nélküli SQL-készlet lehetővé teszi, hogy lekérdezze a tárolt adatokról a Lake-fájlokat, míg a dedikált SQL-készlet lehetővé teszi az adatok lekérdezését és betöltését a Microsoft-fájlokba. Ha az adat betöltése dedikált SQL-készletbe történik, az adat **elosztásra** kerül a rendszer teljesítményének optimalizálása érdekében. Hogy melyik horizontális skálázási mintát szeretné használni az adatok elosztásához, azt a tábla definiálásakor döntheti el. Ezek a horizontális skálázási minták támogatottak:

* Kivonat
* Ciklikus időszeletelés
* Replikálás

## <a name="control-node"></a>Vezérlő csomópont

A vezérlő csomópont az architektúra agya. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. 

A szinapszis SQL-ben az elosztott lekérdezési motor a vezérlő csomóponton fut a párhuzamos lekérdezések optimalizálása és koordinálása érdekében. Ha T-SQL-lekérdezést küld a dedikált SQL-készletnek, a vezérlési csomópont átalakítja azokat a lekérdezéseket, amelyek párhuzamosan futnak az egyes eloszlásokon.

A kiszolgáló nélküli SQL-készletben a DQP motor a vezérlési csomóponton futtatja a felhasználói lekérdezés elosztott végrehajtásának optimalizálását és koordinálását úgy, hogy a számítási csomópontokon végrehajtandó kisebb lekérdezésekre bontja. Emellett az egyes csomópontok által feldolgozandó fájlok készleteit is hozzárendeli.

## <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok biztosítják a számítási teljesítményt. 

A dedikált SQL-készletben a disztribúciók leképezik a számítási csomópontokat a feldolgozáshoz. A további számítási erőforrásokért a készlet újraképezi a disztribúciókat a rendelkezésre álló számítási csomópontokra. A számítási csomópontok száma 1 és 60 közötti tartományba esik, és a dedikált SQL-készlet szolgáltatási szintje határozza meg. Minden számítási csomóponthoz tartozik egy csomópont-azonosító, amely a rendszernézetekben látható. A számítási csomópont AZONOSÍTÓját úgy tekintheti meg, hogy megkeresi a node_id oszlopot a rendszernézetekben, amelyek nevei a sys.pdw_nodeskal kezdődnek. A rendszernézetek listáját a következő témakörben tekintheti meg: [SZINAPSZIS SQL rendszer nézetei](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true).

A kiszolgáló nélküli SQL-készletben minden számítási csomóponthoz hozzá kell rendelni egy feladatot, valamint a feladat végrehajtásához szükséges fájlokat. A feladat elosztott lekérdezés-végrehajtási egység, amely ténylegesen a lekérdezés felhasználója számára van elküldve. Az automatikus skálázás érvényes annak biztosítására, hogy elegendő számítási csomópont legyen kihasználva a felhasználói lekérdezés végrehajtásához.

## <a name="data-movement-service"></a>Adatáthelyezési szolgáltatás

Az adatátviteli szolgáltatás (DMS) a dedikált SQL-készlet adatátviteli technológiája, amely a számítási csomópontok közötti adatáthelyezést koordinálja. Egyes lekérdezések adatáthelyezést igényelnek annak biztosításához, hogy a párhuzamos lekérdezések pontos eredményeket állítsanak vissza. Ha adatáthelyezésre van szükség, a DMS biztosítja a megfelelő adatelérést a megfelelő helyre.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Disztribúciók

A terjesztés a dedikált SQL-készletben elosztott adatokon futó párhuzamos lekérdezések tárolásának és feldolgozásának alapvető egysége. Ha a dedikált SQL-készlet egy lekérdezést futtat, a munka 60 kisebb, párhuzamosan futó lekérdezésekre van osztva. 

Az 60-es kisebb lekérdezések az egyik adateloszláson futnak. Minden számítási csomópont egy vagy több 60-disztribúciót kezel. A maximális számítási erőforrásokkal rendelkező dedikált SQL-készlethez számítási csomópontok egyetlen eloszlása tartozik. A minimális számítási erőforrásokkal rendelkező dedikált SQL-készlet minden eloszlása egy számítási csomóponton található. 

## <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák
A kivonat alapján elosztott tábla nyújtja a legnagyobb lekérdezési teljesítményt az összekapcsolásoknál és aggregációknál nagy táblák esetén. 

Az adatszegmensek kivonatos elosztott táblába való felosztásához a dedikált SQL-készlet kivonatoló függvényt használ az egyes sorok egyetlen eloszláshoz való hozzárendeléséhez determinisztikus módon. A tábla definíciójában az oszlopok egyike elosztási oszlopként van megjelölve. A kivonatolási függvény az elosztási oszlop értékeit használja az egyes sorok elosztáshoz rendeléséhez.

Az alábbi ábra azt szemlélteti, hogyan történik a teljes (nem elosztott) táblázat tárolása kivonatként elosztott táblaként. 

![Elosztott tábla](media//overview-architecture/hash-distributed-table.png "Elosztott tábla") 

* Minden sor egy eloszláshoz tartozik. 
* A determinisztikus kivonatoló algoritmus az egyes sorokat egyetlen eloszláshoz rendeli. 
* A tábla sorainak száma eloszlása a különböző méretű táblákban látható módon változik.

A terjesztési oszlop kiválasztásához teljesítménnyel kapcsolatos szempontokat kell figyelembe venni, például a különbségtételt, az adattorzítást, valamint a rendszeren futó lekérdezések típusait.

## <a name="round-robin-distributed-tables"></a>Ciklikus időszeleteléssel elosztott táblák

A betöltések előkészítési táblája, amely gyors teljesítményt nyújt, és rövid idő alatt készíti el a táblázatot.

Ciklikus időszeleteléses elosztott tábla egyenletesen osztja el az adatokat a táblázatban, de minden további optimalizálás nélkül. A rendszer először véletlenszerűen választja ki a eloszlást, majd a sorok puffereit egymás utáni eloszláshoz rendeli. Az adatok a ciklikus időszeleteléses táblába gyorsan betölthetők, de a lekérdezési teljesítmény gyakran jobb a kivonatelosztott táblák esetében. A ciklikus időszeletelésű táblákhoz való csatlakozáshoz szükség van az adatelemzésre, ami további időt vesz igénybe.

## <a name="replicated-tables"></a>Replikált táblák
A kisméretű tábláknál a replikált táblák nyújtják a leggyorsabb lekérdezési teljesítményt.

A replikált tábla a tábla teljes másolatát gyorsítótárazza az egyes számítási csomópontokon. Így a táblázatok replikálásával megszűnik az adatok átvitele a számítási csomópontok között a csatlakozás vagy összesítés előtt. A replikált táblákat legjobban kisméretű táblákkal lehet kihasználni. További tárterületre van szükség, és az adatírás során felmerülő további terhelés, amely a nagyméretű táblákat nem praktikus formában hajtja végre. 

Az alábbi ábrán egy olyan replikált tábla látható, amely az első eloszlásban van gyorsítótárazva az egyes számítási csomópontokon. 

![Replikált tábla](media/overview-architecture/replicated-table.png "Replikált tábla") 

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a szinapszis SQL-t, ismerkedjen meg [a DEDIKÁLT SQL-készlet gyors létrehozásával](../quickstart-create-sql-pool-portal.md) és a [mintaadatok betöltésével](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-Load-Sample-Databases.MD). Vagy kezdjen el [használni a kiszolgáló nélküli SQL-készletet](../quickstart-sql-on-demand.md). Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét](../../azure-glossary-cloud-terminology.md), amikor az új fogalmakkal ismerkedik. 
