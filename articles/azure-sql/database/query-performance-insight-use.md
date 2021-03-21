---
title: Lekérdezési terheléselemző
description: A lekérdezés teljesítményének figyelése azonosítja a legtöbb CPU-fogyasztást és hosszan futó lekérdezéseket a Azure SQL Database önálló és készletezett adatbázisaihoz.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: db24f280f66e567572821297cfc9bb9b1e19743b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222343"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Azure SQL Database Lekérdezési terheléselemző
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A Lekérdezési terheléselemző intelligens lekérdezéselemzéseket biztosít az önálló és a rugalmas készletbe helyezett adatbázisokhoz. Ez segít azonosítani a legtöbb erőforrást használó és a hosszú ideig futó lekérdezéseket a számítási feladatban. Így kiszűrhető, hogy mely lekérdezéseket kell optimalizálni a számítási feladat teljesítményének javítása, illetve a kifizetett erőforrások hatékony felhasználása érdekében. A Lekérdezési terheléselemző segítségével kevesebb időt tölthet fel az adatbázis teljesítményének elhárítására azáltal, hogy a következőket biztosítja:

* Mélyebb betekintés az adatbázisok erőforrás-(DTU-) felhasználására
* A legfontosabb adatbázis-lekérdezésekre vonatkozó részletek CPU, időtartam és végrehajtások száma alapján (lehetséges hangolási jelöltek a teljesítmény növeléséhez)
* A lekérdezés részleteinek részletezése az erőforrás-használat lekérdezési szövegének és előzményeinek megtekintéséhez
* Az [adatbázis-tanácsadók](database-advisor-implement-performance-recommendations.md) teljesítményre vonatkozó javaslatait bemutató jegyzetek

![Lekérdezési terheléselemző](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Előfeltételek

Lekérdezési terheléselemző megköveteli, hogy a [lekérdezési tároló](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) aktív legyen az adatbázisban. Alapértelmezés szerint automatikusan engedélyezve van a Azure SQL Database összes adatbázisa. Ha a lekérdezési tároló nem fut, a Azure Portal kérni fogja, hogy engedélyezze.

> [!NOTE]
> Ha a "lekérdezési tároló nincs megfelelően konfigurálva ezen az adatbázisban" üzenet jelenik meg a portálon, tekintse meg [a lekérdezés-tároló konfigurációjának optimalizálása](#optimize-the-query-store-configuration)című témakört.

## <a name="permissions"></a>Engedélyek

A Lekérdezési terheléselemző használatához a következő [Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC)](../../role-based-access-control/overview.md) engedélyek szükségesek:

* **Olvasó**, **tulajdonos**, **közreműködő**, **SQL db közreműködő** vagy **SQL Server közreműködő** engedély szükséges a legfelső szintű erőforrás-felhasználású lekérdezések és diagramok megtekintéséhez.
* A lekérdezés szövegének megtekintéséhez **tulajdonos**, **közreműködő**, **SQL-adatbázis közreműködői** vagy **SQL Server közreműködői** engedélyek szükségesek.

## <a name="use-query-performance-insight"></a>A Lekérdezési terheléselemző használata

A Lekérdezési terheléselemző könnyen használható:

1. Nyissa meg a [Azure Portal](https://portal.azure.com/) , és keresse meg a vizsgálni kívánt adatbázist.
2. A bal oldali menüben nyissa meg az **intelligens teljesítmény**  >  **lekérdezési terheléselemző**.
  
   ![Lekérdezési terheléselemző a menüben](./media/query-performance-insight-use/tile.png)

3. Az első lapon tekintse át a leggyakoribb erőforrás-felhasználású lekérdezések listáját.
4. Válasszon ki egy egyéni lekérdezést a részleteinek megtekintéséhez.
5. Nyissa meg az **intelligens teljesítménnyel** kapcsolatos  >  **teljesítménnyel kapcsolatos javaslatokat** , és ellenőrizze, hogy van-e elérhető teljesítményre vonatkozó javaslat. További információ a beépített teljesítménnyel kapcsolatos javaslatokról: [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md).
6. A megfigyelt intervallum módosításához használjon csúszkákat vagy nagyítási ikonokat.

   ![Teljesítmény-irányítópult](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Ahhoz Azure SQL Database, hogy a lekérdezésben szereplő információk Lekérdezési terheléselemzőban legyenek megjelenítve, a Query Store-nak néhány órányi adatot kell rögzítenie. Ha az adatbázis nem rendelkezik tevékenységgel, vagy ha a lekérdezési tár egy adott időszakban nem volt aktív, a diagramok üresek lesznek, ha Lekérdezési terheléselemző megjeleníti az adott időtartományt. A lekérdezési tárolót bármikor engedélyezheti, ha a szolgáltatás nem fut. További információ: [ajánlott eljárások a lekérdezési tárolóval](/sql/relational-databases/performance/best-practice-with-the-query-store).
>

Az adatbázis-teljesítménnyel kapcsolatos ajánlásokat a Lekérdezési terheléselemző navigációs paneljén válassza a [javaslatok](database-advisor-implement-performance-recommendations.md) elemet.

![A javaslatok lap](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>A PROCESSZORt használó leggyakoribb lekérdezések áttekintése

Alapértelmezés szerint a Lekérdezési terheléselemző megjeleníti az első öt CPU-fogyasztási lekérdezést, amikor először nyitja meg.

1. Jelölje be vagy törölje azokat a lekérdezéseket, amelyekkel belefoglalhatja vagy kizárhatja őket a diagramból jelölőnégyzetek használatával.

   A felső sorban az adatbázis összesített DTU százaléka látható. A sávok azt a CPU-százalékot mutatják, amelyet a kijelölt lekérdezések a kiválasztott intervallum során felhasználtak. Ha például a **múlt hét** van kiválasztva, az egyes sávok egyetlen napot jelölnek.

   ![Leggyakoribb lekérdezések](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > A megjelenített DTU-vonal az egyórás időszakokban a maximális fogyasztási értékre van összesítve. Ez azt jelenti, hogy csak a lekérdezés-végrehajtási statisztikákkal rendelkező, magas szintű összehasonlítást kell végrehajtani. Bizonyos esetekben előfordulhat, hogy a DTU kihasználtsága túl magas, mint a futtatott lekérdezésekhez képest, de ez nem feltétlenül igaz.
   >
   > Ha például egy lekérdezés csak néhány percre 100%-ra maxed, akkor a Lekérdezési terheléselemző DTU sor a teljes órányi felhasználást (100%) jeleníti meg (a maximális összesített érték következménye).
   >
   > A finomabb összehasonlításhoz (legfeljebb egy percig) érdemes lehet egyéni DTU-kihasználtsági diagramot létrehozni:
   >
   > 1. A Azure Portal válassza a **Azure SQL Database**  >  **figyelés** lehetőséget.
   > 2. Válassza a **Metrikák** lehetőséget.
   > 3. Válassza a **+ diagram hozzáadása** elemet.
   > 4. Válassza ki a DTU százalékos arányát a diagramon.
   > 5. Emellett válassza az **elmúlt 24 óra** lehetőséget a bal felső menüben, és módosítsa egy percre.
   >
   > A lekérdezés-végrehajtási diagram összehasonlításához használja az egyéni DTU diagramot a finomabb részletességgel.

   Az alsó rács a látható lekérdezések összesített információit jeleníti meg:

   * A lekérdezés azonosítója, amely a lekérdezés egyedi azonosítója az adatbázisban.
   * CPU/lekérdezés egy megfigyelhető intervallumban, amely az összesítési függvénytől függ.
   * A lekérdezés időtartama, amely az összesítési függvénytől is függ.
   * Egy adott lekérdezés végrehajtásának teljes száma.

2. Ha az adatai elavulttá válnak, kattintson a **frissítés** gombra.

3. A csúszkák és a nagyítás gombokkal módosíthatja a megfigyelési időközt, és megvizsgálhatja a felhasználási tüskéket:

   ![Csúszkák és nagyítási gombok az intervallum módosításához](./media/query-performance-insight-use/zoom.png)

4. Kiválaszthatja az **Egyéni** fület is a nézet testreszabásához:

   * Metrika (CPU, időtartam, végrehajtás száma).
   * Időtartam (az elmúlt 24 óra, az elmúlt hét vagy az elmúlt hónap).
   * Lekérdezések száma.
   * Összesítési függvény.
  
   ![Egyéni lap](./media/query-performance-insight-use/custom-tab.png)
  
5. Kattintson a **Go >** gombra a testreszabott nézet megjelenítéséhez.

   > [!IMPORTANT]
   > Lekérdezési terheléselemző a választástól függően a legfontosabb 5-20-nél több lekérdezés jelenik meg. Az adatbázis több lekérdezést is futtathat a fent láthatók felett, és ezek a lekérdezések nem lesznek feltüntetve a diagramon.
   >
   > Előfordulhat, hogy létezik egy adatbázis-munkaterhelés-típus, amelyben sok kisebb lekérdezés található, a fent láthatók szerint, gyakran futnak, és a DTU többségét használják. Ezek a lekérdezések nem jelennek meg a teljesítmény diagramon.
   >
   > Előfordulhat például, hogy egy lekérdezés jelentős mennyiségű DTU használ egy ideig, bár a megfigyelt időszakon belüli teljes felhasználás kevesebb, mint a többi legfelső szintű lekérdezés. Ilyen esetben a lekérdezés Erőforrás-kihasználtsága nem jelenik meg a diagramon.
   >
   > Ha meg kell értenie az Lekérdezési terheléselemző korlátain túli legfelső szintű lekérdezés-végrehajtást, érdemes lehet [Azure SQL Analyticst](../../azure-monitor/insights/azure-sql.md) használni a speciális adatbázis-teljesítmény figyeléséhez és hibaelhárításához.
   >

## <a name="view-individual-query-details"></a>Egyéni lekérdezés részleteinek megtekintése

A lekérdezés részleteinek megtekintése:

1. Válassza ki bármelyik lekérdezést a leggyakoribb lekérdezések listájában.

    ![Leggyakoribb lekérdezések listája](./media/query-performance-insight-use/details.png)

   Megnyílik egy részletes nézet. Itt látható a CPU-használat, az időtartam és a végrehajtás száma az idő múlásával.

2. A részletekért válassza a diagram funkcióit.

   * A felső diagram egy olyan sort mutat be, amely a teljes adatbázis DTU százalékát tartalmazza. A sávok a kiválasztott lekérdezés által felhasznált CPU-százalékarányok.
   * A második diagram a kijelölt lekérdezés teljes időtartamát mutatja.
   * Az alsó diagram a kijelölt lekérdezés végrehajtásának teljes számát jeleníti meg.

   ![Lekérdezés részletei](./media/query-performance-insight-use/query-details.png)

3. Használhatja a csúszkákat, a nagyítási gombokat is használhatja, vagy a **Beállítások** lehetőség kiválasztásával testreszabhatja a lekérdezési adatmegjelenítés módját, vagy választhat másik időtartományt.

   > [!IMPORTANT]
   > A Lekérdezési terheléselemző nem rögzít DDL-lekérdezéseket. Bizonyos esetekben előfordulhat, hogy nem rögzíti az összes ad hoc lekérdezést.
   >

## <a name="review-top-queries-per-duration"></a>Leggyakoribb lekérdezések áttekintése időtartam szerint

A Lekérdezési terheléselemző két mérőszáma segíthet megtalálni a potenciális szűk keresztmetszeteket: időtartam és végrehajtások száma.

A hosszan futó lekérdezések a lehető legnagyobb potenciállal rendelkeznek az erőforrások hosszabb zárolásához, más felhasználók blokkolásához és a méretezhetőség korlátozásához. Ők is a legjobb választás az optimalizáláshoz. További információ: az [Azure SQL-blokkoló problémáinak megismerése és megoldása](understand-resolve-blocking.md).

A hosszú ideig futó lekérdezések azonosítása:

1. Nyissa meg Lekérdezési terheléselemző **Egyéni** lapját a kiválasztott adatbázishoz.
2. Módosítsa a mérőszámok **időtartamát**.
3. Válassza ki a lekérdezések számát és a megfigyelési időközt.
4. Válassza ki az összesítési függvényt:

   * A **Sum** a teljes megfigyelési időszakra vonatkozó összes lekérdezés végrehajtási idejét hozzáadja.
   * A **maximálisan megkeresi** azokat a lekérdezéseket, amelyekben a végrehajtási idő elérte a teljes megfigyelési időközt.
   * Az **AVG** megkeresi az összes lekérdezés végrehajtásának átlagos végrehajtási időpontját, és megjeleníti a fenti átlagok legfontosabb értékeit.

   ![Lekérdezés időtartama](./media/query-performance-insight-use/top-duration.png)

5. Kattintson a **Go >** gombra a testreszabott nézet megjelenítéséhez.

   > [!IMPORTANT]
   > A lekérdezés nézet módosítása nem frissíti a DTU vonalat. Az DTU-vonal mindig az intervallum maximális felhasználási értékét jeleníti meg.
   >
   > Az adatbázis DTU-felhasználásának részletesebb megismeréséhez (legfeljebb egy percig) érdemes lehet egyéni diagramot létrehozni a Azure Portalban:
   >
   > 1. Válassza **Azure SQL Database**  >  **figyelés** lehetőséget.
   > 2. Válassza a **Metrikák** lehetőséget.
   > 3. Válassza a **+ diagram hozzáadása** elemet.
   > 4. Válassza ki a DTU százalékos arányát a diagramon.
   > 5. Emellett válassza az **elmúlt 24 óra** lehetőséget a bal felső menüben, és módosítsa egy percre.
   >
   > Javasoljuk, hogy az egyéni DTU diagram használatával hasonlítsa össze a lekérdezési teljesítmény diagramot.
   >

## <a name="review-top-queries-per-execution-count"></a>Leggyakoribb lekérdezések áttekintése végrehajtások száma alapján

Előfordulhat, hogy az adatbázist használó felhasználói alkalmazás lassú lesz, bár a végrehajtások nagy száma nem befolyásolja magát az adatbázist, és az erőforrások használata alacsony.

Bizonyos esetekben a magas szintű végrehajtások száma több hálózati menethez vezethet. A ciklikus kirándulások hatással vannak a teljesítményre. Ezek a hálózati késés és az alsóbb rétegbeli kiszolgáló késése alá esnek.

Például számos adatvezérelt webhely minden felhasználói kérelem esetében jelentősen hozzáfér az adatbázishoz. Bár a kapcsolatok készletezése segít, a megnövekedett hálózati forgalom és a kiszolgáló terhelése is csökkentheti a teljesítményt. Általánosságban a ciklikus utakat minimálisra kell tartani.

A gyakran végrehajtott ("Csevegő") lekérdezések azonosítása:

1. Nyissa meg Lekérdezési terheléselemző **Egyéni** lapját a kiválasztott adatbázishoz.
2. Módosítsa a mérőszámokat a **végrehajtások számára**.
3. Válassza ki a lekérdezések számát és a megfigyelési időközt.
4. Kattintson a **Go >** gombra a testreszabott nézet megjelenítéséhez.

   ![Lekérdezési végrehajtások száma](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>A teljesítmény-finomhangolási megjegyzések ismertetése

A számítási feladatok Lekérdezési terheléselemzőban való megismerése során előfordulhat, hogy a diagram tetején függőleges vonallal rendelkező ikonokat észlel.

Ezek az ikonok megjegyzések. [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md)teljesítményre vonatkozó javaslatokat jelenítenek meg. Ha egy jegyzet fölé viszi a mutatót, összefoglaló információkat kaphat a teljesítménnyel kapcsolatos javaslatokról.

   ![Lekérdezési jegyzet](./media/query-performance-insight-use/annotation.png)

Ha többet szeretne megtudni, vagy alkalmazni kívánja az Advisor javaslatát, válassza az ikont a javasolt művelet részleteinek megnyitásához. Ha ez egy aktív javaslat, akkor azonnal alkalmazhatja a portálról.

   ![Lekérdezési Megjegyzés részletei](./media/query-performance-insight-use/annotation-details.png)

Bizonyos esetekben a nagyítási szint miatt előfordulhat, hogy az egymáshoz közeledő jegyzetek egyetlen jegyzetbe vannak összecsukva. Lekérdezési terheléselemző ezt jelöli a csoport megjegyzése ikonként. A csoport megjegyzésének ikon kiválasztásakor megnyílik egy új panel, amely felsorolja a jegyzeteket.

A lekérdezések és a teljesítmény-hangolási műveletek korrelációja segíthet a számítási feladatok jobb megismerésében.

## <a name="optimize-the-query-store-configuration"></a>A lekérdezési tároló konfigurációjának optimalizálása

A Lekérdezési terheléselemző használata során előfordulhat, hogy a következő lekérdezési tár hibaüzenetei jelenhetnek meg:

* "A lekérdezési tároló nincs megfelelően konfigurálva ezen az adatbázison. További információért kattintson ide. "
* "A lekérdezési tároló nincs megfelelően konfigurálva ezen az adatbázison. Kattintson ide a beállítások módosításához.

Ezek az üzenetek általában akkor jelennek meg, ha a Query Store nem tud új adatokat gyűjteni.

Az első eset akkor történik meg, ha a lekérdezési tár írásvédett állapotban van, és a paraméterek beállítása optimális. Ezt az adattár méretének növelésével vagy a lekérdezési tároló törlésével javíthatja. (Ha törli a lekérdezési tárolót, az összes korábban összegyűjtött telemetria el fog veszni.)

   ![Lekérdezési tár részletei](./media/query-performance-insight-use/qds-off.png)

A második eset akkor fordul elő, ha a lekérdezési tár nincs engedélyezve, vagy a paraméterek nincsenek optimálisan beállítva. Megváltoztathatja az adatmegőrzési és rögzítési házirendet, valamint engedélyezheti a lekérdezési tárolót is, ha a következő parancsokat futtatja [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) vagy a Azure Portal.

### <a name="recommended-retention-and-capture-policy"></a>Ajánlott megőrzési és rögzítési szabályzat

Az adatmegőrzési szabályzatoknak két típusa van:

* **Méret-alapú**: Ha a házirend **automatikus** értékre van beállítva, akkor a rendszer automatikusan törli az adatmennyiséget, amikor a maximális méret elérte a szinteket.
* **Időalapú**: alapértelmezés szerint ez a házirend 30 napra van állítva. Ha a lekérdezési tároló nem áll rendelkezésre, a 30 napnál régebbi lekérdezési adatokat fogja törölni.

Beállíthatja a rögzítési szabályzatot a következőre:

* **Összes**: a lekérdezési tároló rögzíti az összes lekérdezést.
* **Automatikus**: a lekérdezési tároló figyelmen kívül hagyja a ritka lekérdezéseket és a nem jelentős fordítási és végrehajtási időtartamú lekérdezéseket. A végrehajtások száma, a fordítás időtartama és a futásidejű időtartam küszöbértékei belsőleg vannak meghatározva. Ez az alapértelmezett beállítás.
* **Nincs**: a lekérdezési tároló leállítja az új lekérdezések rögzítését, de a már rögzített lekérdezésekhez tartozó futásidejű statisztikák továbbra is begyűjthetők.

Javasoljuk, hogy a [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) vagy a Azure Portal az alábbi parancsok végrehajtásával állítsa be az összes szabályzatot **automatikusra** és a tisztítási házirendre. (Cserélje le `YourDB` az adatbázis nevére.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Növelje a lekérdezési tároló méretét úgy, hogy a [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) vagy a Azure Portal használatával csatlakozik egy adatbázishoz, és futtatja a következő lekérdezést. (Cserélje le `YourDB` az adatbázis nevére.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Ezen beállítások alkalmazása végül a Query Store telemetria gyűjti az új lekérdezéseket. Ha a Query Store-nak azonnal működőképesnek kell lennie, dönthet úgy, hogy törli a lekérdezési tárolót úgy, hogy a következő lekérdezést futtatja a SSMS vagy a Azure Portal használatával. (Cserélje le `YourDB` az adatbázis nevére.)

> [!NOTE]
> A következő lekérdezés futtatása törli a lekérdezési tárolóban korábban összegyűjtött figyelt telemetria.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Következő lépések

Érdemes [Azure SQL Analyticst](../../azure-monitor/insights/azure-sql.md) használni az önálló és készletezett adatbázisok, rugalmas készletek, felügyelt példányok és példány-adatbázisok nagy flottájának speciális teljesítményének figyelésére.