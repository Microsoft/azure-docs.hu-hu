---
title: A Synapse SQL architektúrája
description: Megtudhatja, Azure Synapse SQL hogyan kombinálja az elosztott lekérdezésfeldolgozási képességeket az Azure Storage-ral a nagy teljesítmény és a méretezhetőség érdekében.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f342f39b62956cd85f269918e8e1ef1a2478a3d8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566358"
---
# <a name="azure-synapse-sql-architecture"></a>Az Azure Synapse SQL architektúrája 

Ez a cikk a Synapse SQL.

## <a name="synapse-sql-architecture-components"></a>Synapse SQL architektúra összetevői

Synapse SQL horizontális felskálás architektúrával osztja el az adatok számítási feldolgozását több csomópont között. A számítás elkülönül a tárolástól, ami lehetővé teszi a számítás skálázható a rendszerben található adatoktól függetlenül. 

Dedikált SQL-készlet esetén a skálázás egy számítási teljesítmény absztrakciója, más néven [adattárházegység.](resource-consumption-models.md) 

Kiszolgáló nélküli SQL-készlet esetén a kiszolgáló nélküli méretezés automatikusan történik a lekérdezési erőforrások követelményeinek megfelelően. Ahogy a topológia idővel változik csomópontok hozzáadásával, eltávolításával vagy feladatátvételével, alkalmazkodik a változásokhoz, és a lekérdezés elegendő erőforrással rendelkezik, és sikeresen befejeződik. Az alábbi képen például a kiszolgáló nélküli SQL-készlet látható, amely 4 számítási csomópontot használ egy lekérdezés végrehajtásához.

![A Synapse SQL architektúrája](./media//overview-architecture/sql-architecture.png)

Synapse SQL csomópontalapú architektúrát használ. Az alkalmazások csatlakoznak, és T-SQL-parancsokat adnak ki egy vezérlő csomópontnak, amely az egyetlen belépési pont a Synapse SQL. 

Az Azure Synapse SQL-vezérlő csomópont egy elosztott lekérdezési motort használ a lekérdezések párhuzamos feldolgozásra való optimalizálásához, majd átadja a műveleteket a számítási csomópontoknak a munkájuk párhuzamos végrehajtásához. 

A kiszolgáló nélküli SQL-készlet vezérlő csomópontja elosztott lekérdezésfeldolgozási (DQP) motort használ a felhasználói lekérdezések elosztott végrehajtásának optimalizálásához és vezényléhez a számítási csomópontokon végrehajtandó kisebb lekérdezésekre való felosztással. Minden kis méretű lekérdezés neve tevékenység, és az elosztott végrehajtási egységet jelöli. Beolvassa a fájlokat a tárolóból, csatlakozik más feladatok, csoportok vagy a más tevékenységekből lekért adatok megrendeléséhez. 

A számítási csomópontok az összes felhasználói adatot az Microsoft Azure Storage-ban tárolják, és futtatják a párhuzamos lekérdezéseket. Az adatáthelyezési szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely szükség szerint áthelyezi az adatokat a csomópontok között a lekérdezések párhuzamos futtatásához és pontos eredmények visszaadásához. 

Leválasztott tárolás és számítás esetén a Synapse SQL a számítási teljesítmény független méretezése a tárolási igényektől függetlenül előnyös lehet. A kiszolgáló nélküli SQL-készlet skálázása automatikusan történik, dedikált SQL-készlet esetén pedig a következőt lehet:

* A számítási teljesítmény egy dedikált SQL-készleten belül, adatok mozgatása nélkül növekedhet vagy csökkenhet.
* Szüneteltetheti a számítási kapacitást az adatok megőrzésével, hogy csak a tárterületért kelljen fizetnie.
* Működési időben újra aktiválhatja a számítási kapacitást.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL Azure Storage-et használja a felhasználói adatok biztonságos tárolásához. Mivel az adatokat az Azure Storage tárolja és kezeli, külön díjat kell fizetni a tárhelyfelhasználásért. 

A kiszolgáló nélküli SQL-készlet lehetővé teszi a Data Lake-fájlok lekérdezését, míg a dedikált SQL-készlet lehetővé teszi az adatok data lake-fájlokból való lekérdezését és beozását. Ha az adatokat dedikált SQL-készletbe van bezsúfolta, az adatokat a rendszer teljesítményének optimalizálása érdekében disztribúciókra daraboltuk.  Hogy melyik horizontális skálázási mintát szeretné használni az adatok elosztásához, azt a tábla definiálásakor döntheti el. Ezek a horizontális skálázású minták támogatottak:

* Kivonat
* Ciklikus időszeletelés
* Replikálás

## <a name="control-node"></a>Vezérlő csomópont

A vezérlő csomópont az architektúra agya. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. 

Ebben Synapse SQL elosztott lekérdezési motor a Vezérlő csomóponton fut a párhuzamos lekérdezések optimalizálása és koordinálása érdekében. Amikor T-SQL-lekérdezést küld egy dedikált SQL-készletbe, a vezérlő csomópont olyan lekérdezésekké alakítja azt, amelyek párhuzamosan futnak az egyes elosztások között.

A kiszolgáló nélküli SQL-készletben a DQP-motor a Vezérlő csomóponton fut a felhasználói lekérdezések elosztott végrehajtásának optimalizálásához és koordinálása érdekében a számítási csomópontokon végrehajtandó kisebb lekérdezésekre való felosztással. Emellett hozzárendeli az egyes csomópontok által feldolgozható fájlkészleteket is.

## <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok biztosítják a számítási teljesítményt. 

A dedikált SQL-készletben az elosztások számítási csomópontokra vannak leképezve feldolgozásra. Ahogy több számítási erőforrásért fizet, a készlet újra leképezi az eloszlásokat az elérhető számítási csomópontokra. A számítási csomópontok száma 1 és 60 között lehet, és ezt a dedikált SQL-készlet szolgáltatási szintje határozza meg. Minden számítási csomópont rendelkezik egy csomópont-azonosítóval, amely látható a rendszernézetek között. A Számítási csomópont azonosítóját a rendszernézetek node_id, amelynek a neve a következővel kezdődik: sys.pdw_nodes. A rendszernézetek listájáért lásd: Synapse SQL [megtekintése.](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true)

A kiszolgáló nélküli SQL-készletben minden számítási csomóponthoz hozzá van rendelve a tevékenység végrehajtásához szükséges feladatok és fájlkészletek. A feladat egy elosztott lekérdezés-végrehajtási egység, amely valójában része a felhasználó által elküldött lekérdezésnek. Az automatikus skálázás azért van érvényben, hogy elegendő számítási csomópont legyen felhasználva a felhasználói lekérdezések végrehajtásához.

## <a name="data-movement-service"></a>Adatáthelyezési szolgáltatás

Az adatátviteli szolgáltatás (DMS) a dedikált SQL-készlet adatátviteli technológiája, amely koordinálja a számítási csomópontok közötti adatmozgatást. Egyes lekérdezések adatátmozgatást igényelnek, hogy a párhuzamos lekérdezések pontos eredményeket adjanak vissza. Ha adatáthelyre van szükség, a DMS gondoskodik arról, hogy a megfelelő adatok a megfelelő helyre jutnak.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Disztribúciók

Az elosztás a dedikált SQL-készletben elosztott adatokon futó párhuzamos lekérdezések tárolási és feldolgozási alapegysége. Amikor egy dedikált SQL-készlet lekérdezést futtat, a rendszer 60 kisebb, párhuzamosan futó lekérdezésre bontja a munkát. 

A 60 kisebb lekérdezés mindegyikét az egyik adatelosztáson futtatja. Minden számítási csomópont a 60 disztribúció egy vagy többét kezeli. A maximális számítási erőforrásokkal rendelkezik dedikált SQL-készlet számítási csomópontonként egy elosztással. A minimális számítási erőforrásokkal és dedikált SQL-készletekkel az összes elosztás egy számítási csomóponton található. 

## <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák
A kivonat alapján elosztott tábla nyújtja a legnagyobb lekérdezési teljesítményt az összekapcsolásoknál és aggregációknál nagy táblák esetén. 

Ha kivonat alapján elosztott táblába szeretne adatokat horizontálisan skálázni, a dedikált SQL-készlet kivonatoló függvény használatával rendeli hozzá determinisztikusan az egyes sorokat egy elosztáshoz. A tábla definíciójában az oszlopok egyike elosztási oszlopként van megjelölve. A kivonatolási függvény az elosztási oszlop értékeit használja az egyes sorok elosztáshoz rendeléséhez.

Az alábbi ábra egy teljes (nem elosztott) tábla kivonat alapján elosztott táblaként való tárolását mutatja be. 

![Elosztott tábla](media//overview-architecture/hash-distributed-table.png "Elosztott tábla") 

* Minden sor egy elosztáshoz tartozik. 
* A determinisztikus kivonat algoritmus minden sort egy eloszláshoz rendel. 
* A tábla sorai eloszlásonkénti száma a különböző táblák méretének megfelelő módon változik.

Az elosztási oszlop kiválasztásához teljesítménybeli szempontokat kell figyelembe venni, például az eltérőséget, az adateloszlást és a rendszeren futó lekérdezések típusait.

## <a name="round-robin-distributed-tables"></a>Ciklikus időszeleteléssel elosztott táblák

A round-robin tábla a legegyszerűbb tábla, amely gyors teljesítményt hoz létre és biztosít, ha előkészítési táblaként használja a terhelésekhez.

Ciklikus időszeleteléses elosztott tábla egyenletesen osztja el az adatokat a táblázatban, de minden további optimalizálás nélkül. Az elosztást először véletlenszerűen választjuk ki, majd sorpuffereket rendelünk egymás után az eloszláshoz. Az adatok a ciklikus időszeleteléses táblába gyorsan betölthetők, de a lekérdezési teljesítmény gyakran jobb a kivonatelosztott táblák esetében. A round-robin táblák illesztéséhez újratitkosító adatokra van szükség, ami további időt vesz igénybe.

## <a name="replicated-tables"></a>Replikált táblák
A kisméretű tábláknál a replikált táblák nyújtják a leggyorsabb lekérdezési teljesítményt.

A replikált tábla gyorsítótárazza a tábla teljes másolatát minden számítási csomóponton. Így a tábla replikálása nem szükséges adatokat átvenni a számítási csomópontok között az illesztés vagy összesítés előtt. A replikált táblákat legjobban kisméretű táblákkal lehet kihasználni. Extra tárterületre van szükség, és további többletterhelés keletkezik az adatok írásakor, ami a nagy táblákat nem praktikusá teszi. 

Az alábbi ábrán egy replikált tábla látható, amely az egyes számítási csomópontok első elosztásán van gyorsítótárazva. 

![Replikált tábla](media/overview-architecture/replicated-table.png "Replikált tábla") 

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a Synapse SQL, megtudhatja, hogyan hozhat létre gyorsan dedikált SQL-készletet, és hogyan töltsön be mintaadatokat (./sql-data-warehouse-load-sample-databases.md). [](../quickstart-create-sql-pool-portal.md) [](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) Vagy kezdje [el használni a kiszolgáló nélküli SQL-készletet.](../quickstart-sql-on-demand.md) Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét](../../azure-glossary-cloud-terminology.md), amikor az új fogalmakkal ismerkedik. 
