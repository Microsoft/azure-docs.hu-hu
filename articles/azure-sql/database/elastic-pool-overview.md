---
title: Több adatbázis kezelése rugalmas készletekkel
description: Több adatbázis kezelése és méretezése Azure SQL Database-több száz és ezer – rugalmas készletek használatával. Egy díj az erőforrások számára, amelyekhez szükség esetén terjeszthető.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: c478edf95ae345d64da630400fbf63ac613b73a6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653635"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>A rugalmas készletek segítségével több adatbázist kezelhet és méretezheti Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database rugalmas készletek egy egyszerű, költséghatékony megoldás több olyan adatbázis kezeléséhez és méretezéséhez, amelyek változó és kiszámíthatatlan használati igényekkel rendelkeznek. A rugalmas készletben lévő adatbázisok egyetlen kiszolgálón találhatók, és egy meghatározott számú erőforrást használnak. Az Azure SQL Database rugalmas készleteivel az SaaS-fejlesztők az előre meghatározott költségvetésen belül maradva optimalizálhatják az adatbáziscsoportok ár-teljesítmény arányát, és rugalmas teljesítményt biztosíthatnak az egyes adatbázisokhoz.

## <a name="what-are-sql-elastic-pools"></a>Mik a rugalmas SQL-készletek

A SaaS-fejlesztők több adatbázisból álló nagyméretű adatrétegekre építenek alkalmazásokat. Gyakori alkalmazásminta az önálló adatbázis biztosítása minden egyes ügyfél számára. A különböző ügyfelek azonban gyakran eltérő és kiszámíthatatlan használati mintákkal rendelkeznek, és nehéz megbecsülni az egyes adatbázis-felhasználók erőforrás-követelményeit. Hagyományosan két lehetőség közül választhat:

- Többek között kiépíthető erőforrások a csúcsérték-használat és a fizetések alapján, vagy
- A kiépítés a költségek megtakarítása érdekében, a teljesítmény és az ügyfelek elégedettségének rovására a csúcsok során.

A rugalmas készletek a probléma megoldásához biztosítják, hogy az adatbázisok megkapják a szükséges teljesítménybeli erőforrásokat. Egy egyszerű erőforrás-lefoglalási mechanizmust biztosítanak, kiszámítható költségekkel. A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](saas-tenancy-app-design-patterns.md) című részt.
>
> [!IMPORTANT]
> Rugalmas készletekhez nem tartozik adatbázis-díj. Minden órában, a legmagasabb eDTU vagy virtuális mag, a használattól függetlenül, illetve azt, hogy a készlet egy óránál kevesebb ideig volt-e aktív.

A rugalmas készletek lehetővé teszik, hogy a fejlesztő erőforrásokat vásároljon egy több adatbázis által megosztott készlet számára, hogy az egyes adatbázisok előre nem látható használati időszakait is kielégítse. A készlethez a [DTU-alapú vásárlási modellen](service-tiers-dtu.md) vagy a [virtuális mag-alapú vásárlási modellen](service-tiers-vcore.md)alapuló erőforrásokat is konfigurálhat. A készletre vonatkozó erőforrás-követelményt az adatbázisainak összesített kihasználtsága határozza meg. A készlet számára elérhető erőforrások mennyiségét a fejlesztői költségvetés vezérli. A fejlesztő egyszerűen adatbázisokat ad hozzá a készlethez, opcionálisan beállítja az adatbázisok minimális és maximális erőforrásait (a minimális és a maximális DTU, illetve a minimális vagy maximális virtuális mag, attól függően, hogy melyik újrahasznosítási modellt választja), majd a készlet erőforrásait a költségvetésük alapján állítja be. A készletek segítségével a fejlesztő zökkenőmentesen és fokozatosan növelheti szolgáltatásának teljesítményét a korlátozott erőforrásokkal bíró startupok szintjéről az érett vállalkozások szintjére.

A készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek. A nagy terhelés alatt az adatbázisok több erőforrást is felhasználhatnak az igények kielégítése érdekében. A világos terhelésű adatbázisok kevesebbet használnak, és a terhelés nélküli adatbázisok nem használnak fel erőforrásokat. Az erőforrásoknak az egyes adatbázisok helyett a teljes készlet számára hozzáférhetővé tétele jelentősen leegyszerűsíti a felügyeleti feladatokat. Emellett kiszámítható költségvetést is biztosít a készlethez. A minimális állásidővel rendelkező meglévő készletekhez további erőforrások is hozzáadhatók. Hasonlóképpen, ha már nincs szükség további erőforrásokra, a meglévő készletből bármikor eltávolítható. És hozzáadhat vagy eltávolíthat adatbázisokat a készletből. Ha egy adatbázis kiszámítható módon nem használja ki az erőforrásokat, helyezze át az adatbázist.

> [!NOTE]
> Az adatbázisok rugalmas készletbe vagy onnan történő áthelyezésekor nincs leállás, kivéve a művelet végén lévő rövid időtartamot (másodpercben) az adatbázis-kapcsolatok eldobásakor.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Mikor érdemes figyelembe vennie SQL Database rugalmas készletet

A készleteket kifejezetten a nagy számú, speciális felhasználási mintákkal rendelkező adatbázisokhoz tervezték. Az egyes adatbázisok mintáit átlagosan alacsony, és viszonylag rendszertelen időközönkénti hirtelen megugró kihasználtság jellemzi. Ezzel szemben a tartós közepes kihasználtságú adatbázisok nem helyezhetők ugyanahhoz a rugalmas készletbe.

Minél több adatbázist tud hozzáadni egy készlethez, annál többet takaríthat meg. Az alkalmazás kihasználtsági mintája alapján megtekintheti a megtakarítást akár két S3-adatbázis között.

A következő szakaszokból megtudhatja, hogyan mérje fel, hogy előnyös-e, ha egy adott adatbázis-gyűjtemény egy készlethez tartozik. A példákban Standard készletek szerepelnek, de ugyanezen elvek alkalmazhatók az Alapszintű és a Premium készletekre is.

### <a name="assessing-database-utilization-patterns"></a>Az adatbázis felhasználási mintáinak felmérése

Az alábbi ábra egy olyan adatbázist mutat be, amely sok időt tölt tétlenül, miközben időszakosan hirtelen megugró kihasználtság jellemzi. Az ilyen kihasználtsági minta esetén használhatók készletek:

   ![egy készletbe illő önálló adatbázis](./media/elastic-pool-overview/one-database.png)

A diagram a DTU használatát mutatja be egy 1 órás időszakban, 12:00 és 1:00 között, ahol minden egyes adatpontnak 1 perces részletessége van. 12:10 DB1 legfeljebb 90 DTU, de a teljes átlagos használat kevesebb, mint öt DTU. Ezen munkaterhelés egyetlen adatbázisban való futtatásához S3 számítási méret szükséges, azonban ez a művelet az alacsony tevékenységű időszakok során nem használt erőforrások többségét elhagyja.

Egy készletben ezek a használaton kívüli DTU-k több adatbázis között lehetnek megosztva, így a szükséges DTU-k száma és az összköltség is csökken.

Az előző példa mentén továbbhaladva tegyük fel, hogy további adatbázisokkal rendelkezünk, amelyeket DB1-hez hasonló felhasználási minták jellemeznek. Az alábbi két ábrán a négy adatbázis és 20 adatbázis kihasználtsága ugyanarra a gráfra van felhasználva, hogy a DTU-alapú vásárlási modell használatával ábrázolja a kihasználtsága nem átfedéses jellegét az idő múlásával:

   ![négy adatbázis egy készletbe illő kihasználtsági mintával](./media/elastic-pool-overview/four-databases.png)

   ![húsz adatbázis egy készletbe illő kihasználtsági mintával](./media/elastic-pool-overview/twenty-databases.png)

A DTU-k mind a 20 adatbázisra vonatkozó összesített kihasználtságát a fekete vonal jelzi az előző ábrán. Ez alapján a teljes DTU-használat soha nem lépi túl a 100 DTU értéket, és az időtartam során a 20 adatbázis 100 eDTU-t használhat közösen. Ez a DTU 20x-csökkenést eredményez, amely az egyes adatbázisok S3 számítási méretekben történő elhelyezésével összehasonlítható a 13x.

Ez a példa az alábbi okokból ideális:

- Nagy különbségek vannak az adatbázisok átlagos és kiugró mértékű kihasználtsága között.
- Az egyes adatbázisok kiugró mértékű kihasználtsága különböző időpontokban jelentkezik.
- Az eDTU-k több adatbázis között vannak megosztva.

A DTU beszerzési modelljében a készlet ára a készlet Edtu függvénye. A készlethez tartozó eDTU-k egységára egy önálló adatbázis DTU-egységárának másfélszerese, azonban **a készlethez tartozó eDTU-kat sok adatbázis használhatja, így kevesebb eDTU-ra van szükség**. Ezek a díjszabásban és eDTU-megosztásban jelentkező különbségek adják a készletekkel elérhető megtakarítás alapját.

A virtuális mag beszerzési modelljében a rugalmas készletek virtuális mag egységének díja megegyezik az önálló adatbázisok virtuális mag egységének árával.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Hogyan a megfelelő készlet méretének kiválasztása

A készletek legjobb mérete a készletben lévő összes adatbázishoz szükséges összesített erőforrásoktól függ. Ez a következők meghatározását foglalja magában:

- A készletben található összes adatbázis által használt számítási erőforrások maximális száma.  A számítási erőforrásokat a Edtu vagy a virtuális mag szerint indexeli a rendszer a választott vásárlási modelltől függően.
- A készletben szereplő összes adatbázis által használt maximális tárterület (bájtban).

A szolgáltatási szintek és az egyes beszerzési modellek erőforrás-korlátai esetében tekintse meg a [DTU-alapú vásárlási modellt](service-tiers-dtu.md) vagy a [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md).

Az alábbi lépések segítségével megbecsülheti, hogy a készlet költséghatékonyabb-e az önálló adatbázisok esetében:

1. Becsülje meg a készlethez szükséges Edtu vagy virtuális mag a következőképpen:
   - A DTU-alapú vásárlási modell esetében:
     - Max ( *az adatbázisok* &times; *átlagos DTU-kihasználtságának* maximális száma (<db->, <*a párhuzamosan futó adatbázisok* maximális &times; *DTU-kihasználtságának száma db*>)
   - A virtuális mag-alapú vásárlási modell esetében:
     - Max ( *az adatbázisok* &times; *átlagos virtuális mag-kihasználtságának* maximális száma (<db->, <*a párhuzamosan futó adatbázisok* maximális &times; *virtuális mag-kihasználtságának száma db*>)
2. Becsülje meg a készlethez szükséges teljes tárterületet a készletben lévő összes adatbázishoz szükséges adatméret hozzáadásával. A DTU-vásárlási modell esetében határozza meg a eDTU-készlet méretét, amely biztosítja ezt a tárterületet.
3. A DTU-alapú vásárlási modell esetében az 1. és a 2. lépésben szereplő eDTU-becslések nagyobb mennyiségét kell megbecsülni. A virtuális mag-alapú vásárlási modell esetében az 1. lépés alapján végezze el a virtuális mag becslését.
4. Tekintse meg a [SQL Database díjszabási oldalát](https://azure.microsoft.com/pricing/details/sql-database/) , és keresse meg a 3. lépésben megbecsülhető legkisebb méretű készletet.
5. Hasonlítsa össze a készlet árát a 4. lépésből az önálló adatbázisok megfelelő számítási méreteinek árával.

> [!IMPORTANT]
> Ha a készletben lévő adatbázisok száma megközelíti a támogatott maximális értéket, ügyeljen arra, hogy az [erőforrás-kezelést sűrű rugalmas készletekben](elastic-pool-resource-management.md)vegye figyelembe.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Más SQL Database funkciók használata rugalmas készletekkel

### <a name="elastic-jobs-and-elastic-pools"></a>Rugalmas feladatok és rugalmas készletek

A készletek használata leegyszerűsíti a felügyeleti feladatokat, mivel a szkriptek **[rugalmas feladatokban](elastic-jobs-overview.md)** futtathatók. A rugalmas feladatok használatával kiküszöbölhető a nagy számú adatbázis kezelésével járó monotonitás.

További információk a több adatbázissal dolgozó további adatbázis-eszközökről: [Horizontális felskálázás az Azure SQL Database-ben](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Rugalmas készletben lévő adatbázisok üzleti folytonossági lehetőségei

A rugalmas készletbe helyezett adatbázisok általánosságban ugyanazokat [az üzletmenet-folytonossági funkciókat](business-continuity-high-availability-disaster-recover-hadr-overview.md) támogatják, amelyek az önálló adatbázisokhoz is elérhetők.

- **Adott időpontnak megfelelő helyreállítás**

  Az időponthoz való visszaállítás az adatbázis automatikus biztonsági mentését használja a készletben lévő adatbázisok egy adott időpontra történő helyreállításához. Lásd: [Időponthoz kötött visszaállítás](recovery-using-backups.md#point-in-time-restore)

- **Georedundáns visszaállítás**

  A Geo-visszaállítás az alapértelmezett helyreállítási beállítást adja meg, ha egy adatbázis nem érhető el, mert az adatbázist futtató régióban egy incidens található. Lásd: [Azure SQL Database vagy feladatátvétel visszaállítása másodlagosra](disaster-recovery-guidance.md)

- **Aktív georeplikáció**

  Az agresszív helyreállítási követelményekkel rendelkező alkalmazások esetében, mint a Geo-visszaállítás, konfigurálhatja az [aktív földrajzi replikálást](active-geo-replication-overview.md) vagy egy [automatikus feladatátvételi csoportot](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Új SQL Database rugalmas készlet létrehozása a Azure Portal használatával

Kétféle módon hozhat létre rugalmas készletet a Azure Portal.

1. Rugalmas készlet létrehozásához nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki az **Azure SQL** elemet.
2. Válassza a **+ Hozzáadás** lehetőséget az **SQL-telepítés kiválasztása** lap megnyitásához. A rugalmas készletekre vonatkozó további információkat az **adatbázisok** csempén látható **Részletek megjelenítése** lehetőség kiválasztásával tekintheti meg.
3. Az **adatbázisok** csempén válassza a **rugalmas készlet** lehetőséget az **Erőforrás típusa** legördülő menüben, majd válassza a **Létrehozás** lehetőséget:

   ![Rugalmas készlet létrehozása](./media/elastic-pool-overview/create-elastic-pool.png)

4. Vagy létrehozhat egy rugalmas készletet egy meglévő kiszolgálóhoz való navigálással, majd az **+ új készlet** lehetőségre kattintva közvetlenül a kiszolgálóra hozhat létre készletet.

> [!NOTE]
> Egy kiszolgálón több készlet is létrehozható, de nem adhat hozzá különböző kiszolgálókon lévő adatbázisokat ugyanahhoz a készlethez.

A készlet szolgáltatási szintje határozza meg a készletben lévő rugalmas készletek elérhető funkcióit, valamint az egyes adatbázisok számára elérhető erőforrások maximális mennyiségét. Részletekért lásd: rugalmas készletek erőforrás-korlátai a [DTU-modellben](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). A rugalmas készletek virtuális mag-alapú erőforrás-korlátaival kapcsolatban lásd: [virtuális mag-alapú erőforrás-korlátok – rugalmas készletek](resource-limits-vcore-elastic-pools.md).

A készlet erőforrásainak és díjszabásának konfigurálásához kattintson a **készlet konfigurálása** elemre. Ezután válasszon ki egy szolgáltatási szintet, adja hozzá az adatbázisokat a készlethez, és konfigurálja az erőforrás-korlátokat a készlethez és az adatbázisaihoz.

Ha befejezte a készlet konfigurálását, kattintson az Alkalmaz gombra, nevezze el a készletet, majd kattintson az OK gombra a készlet létrehozásához.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Rugalmas készlet és adatbázisainak figyelése

A Azure Portal egy rugalmas készlet és a készleten belüli adatbázisok kihasználtságának figyelésére van lehetőség. A rugalmas készlet módosításait is elvégezheti, és egyszerre is elküldheti az összes módosítást. Ezek a változások közé tartoznak az adatbázisok hozzáadása vagy eltávolítása, a rugalmas készlet beállításainak módosítása vagy az adatbázis beállításainak módosítása.

A beépített [Teljesítményfigyelő](./performance-guidance.md) és [riasztási eszközöket](./alerts-insights-configure-portal.md)a teljesítmény-minősítésekkel kombinálva is használhatja.  Emellett a SQL Database a könnyebb monitorozás érdekében [mérőszámokat és erőforrás-naplókat is képes kibocsátani](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) .

## <a name="customer-case-studies"></a>Ügyféleset-tanulmányok

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  A SnelStart rugalmas készleteket használt Azure SQL Databaseekkel, hogy havonta gyorsan bővítse üzleti szolgáltatásait, 1 000 új Azure SQL Database-adatbázist.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  A árnyék rugalmas készleteket használ a Azure SQL Database segítségével, amelyekkel gyorsan kiépítheti és méretezheti a szolgáltatásokat több ezer bérlő számára a felhőben.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   A Daxko/CSI rugalmas készleteket használ Azure SQL Database a fejlesztési ciklus felgyorsításához, valamint az ügyfelek szolgáltatásainak és teljesítményének növeléséhez.

## <a name="next-steps"></a>Következő lépések

- A díjszabással kapcsolatos információkért lásd a [rugalmas készlet díjszabását](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Rugalmas készletek méretezése: [rugalmas készletek skálázása](elastic-pool-scale.md) és [rugalmas készlet méretezése – mintakód](scripts/monitor-and-scale-pool-powershell.md)
- A rugalmas készleteket használó SaaS-alkalmazások szerkezeti kialakításainak alaposabb megismeréséhez olvassa el a [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](saas-tenancy-app-design-patterns.md) című részt.
- A rugalmas készleteket használó SaaS-oktatóanyagért tekintse [meg a Wingtip SaaS-alkalmazás bemutatása](saas-dbpertenant-wingtip-app-overview.md)című témakört.
- A sok adatbázissal rendelkező rugalmas készletek erőforrás-kezelésével kapcsolatos további információkért lásd: [Erőforrás-kezelés sűrű rugalmas készletekben](elastic-pool-resource-management.md).
