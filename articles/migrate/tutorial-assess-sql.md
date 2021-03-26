---
title: Oktatóanyag az SQL-példányok értékeléséhez az Azure SQL felügyelt példányaira és Azure SQL Databasere való áttelepítéshez
description: Ismerje meg, hogyan hozhat létre értékelést az Azure SQL-ben Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: d4078d1403df01475c6055dded2bd012e97af98e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557989"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Oktatóanyag: SQL-példányok felmérése az Azure SQL-re való áttelepítéshez

Az Azure-ba való Migrálás részeként felméri a helyszíni számítási feladatokat a felhő készültségének mérésére, a kockázatok azonosítására, valamint a költségek és a bonyolultság megbecslésére.
Ebből a cikkből megtudhatja, hogyan értékelheti fel a felderített SQL Server példány-adatbázisokat az Azure SQL-re való áttelepítésre való felkészülés során a Azure Migrate: Discovery and Assessment Tool használatával

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Értékelés futtatása a kiszolgáló konfigurációja és a teljesítményadatok alapján.
> * Az Azure SQL Assessment áttekintése 

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges. 


## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

- Mielőtt ezt az oktatóanyagot elvégezte az Azure SQL-re való áttelepítéshez szükséges SQL Server-példányok értékeléséhez, győződjön meg arról, hogy felderítette az Azure Migrate berendezés használatával felmérni kívánt SQL-példányokat, [kövesse az oktatóanyagot](tutorial-discover-vmware.md)
- Ha egy meglévő projektben szeretné kipróbálni ezt a funkciót, győződjön meg arról, hogy végrehajtotta a jelen cikkben ismertetett [előfeltételeket](how-to-discover-sql-existing-project.md) .


## <a name="run-an-assessment"></a>Értékelés futtatása
Az értékelést a következőképpen futtathatja:
1. Az **Áttekintés** oldalon > **Windows, Linux és SQL Server**, kattintson a **kiszolgálók értékelése és migrálása** elemre.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="A Azure Migrate áttekintő lapja":::
2. **Azure Migrate: felderítés és értékelés** **, kattintson az** értékelés lehetőségre, és válassza ki az értékelés típusát **Azure SQL**-ként.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Az értékelési típus kiválasztásának legördülő listája Azure SQL-ként":::
3. A **kiszolgálók értékelése** > láthatja az előre kiválasztott értékelési típust az **Azure SQL** -ben, és a felderítési forrás alapértelmezés szerint a **Azure Migrate berendezésből felderített kiszolgálókon** jelenik meg.

4. Kattintson a **Szerkesztés** elemre az értékelési tulajdonságok áttekintéséhez.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Szerkesztés gomb, ahol az értékelési tulajdonságok testreszabhatók":::
5. Az értékelés tulajdonságai > **cél tulajdonságai**:
    - A **célhely** mezőben válassza ki azt az Azure-régiót, amelyre az áttelepítést szeretné végezni. 
        - Az Azure SQL-konfiguráció és a Cost-javaslatok a megadott helyen alapulnak. 
    - A **cél központi telepítési típusban**
        - Válassza az **ajánlott** lehetőséget, ha azt szeretné, Azure MIGRATE az SQL-példányok készültségének felmérése az Azure SQL mi és az Azure SQL Database szolgáltatásba való áttelepítéshez, valamint a legmegfelelőbb célként megadott üzembe helyezési lehetőség, a célként megadott csomag, az Azure SQL-konfiguráció és a havi becslések ajánlása. [További információ](concepts-azure-sql-assessment-calculation.md)
        - Válassza az **Azure SQL db** lehetőséget, ha az SQL-példányok készültségét szeretné felmérni az Azure SQL Database-adatbázisok áttelepítésére, és tekintse át a célként megadott szintet, az Azure SQL konfigurációját és a havi becsléseket.
        - Válassza az **Azure SQL mi** lehetőséget, ha szeretné felmérni az SQL-példányok készültségét az Azure SQL felügyelt példányra való áttelepítéshez, és tekintse át a célként megadott szintet, az Azure SQL konfigurációját és a havi becsléseket.
    - A **fenntartott kapacitás** területen határozza meg, hogy az SQL Server számára fenntartott kapacitást kíván-e használni az áttelepítés után.
        - Ha a fenntartott kapacitás lehetőséget választja, nem adhatja meg a "kedvezmény (%)" beállítást.

6. Az értékelés tulajdonságai > **értékelési feltételek**:
    - A méretezési feltételek alapértelmezett értéke a **teljesítmény-alapú** , ami azt jelenti, hogy az Azure migrálása az SQL-példányokra és az általa kezelt adatbázisokra vonatkozó teljesítménymutatókat gyűjti az optimális méretű Azure SQL Database és/vagy az Azure SQL felügyelt példány-konfigurációjának ajánlása érdekében. Megadhatja a következőket:
        - A **teljesítmény előzményei** az értékelés alapjául szolgáló adatok időtartamának jelzésére. (Az alapértelmezett érték egy nap)
        - **Percentilis kihasználtsága**, hogy jelezze a teljesítmény mintához használni kívánt százalékos értéket. (Az alapértelmezett érték a 95. percentilis)
    - A **Comfort Factor (kényelmi tényező**) mezőben adja meg az értékelés során használni kívánt puffert. Ez olyan problémákhoz vezetett, mint például a szezonális használat, a rövid teljesítménybeli előzmények és a jövőbeli használat várható növekedése. Ha például két kényelmi tényezőt használ: 
        
        **Összetevő** | **Hatékony kihasználtság** | **Komfort tényező hozzáadása (2,0)**
        --- | --- | ---
        Cores | 2  | 4
        Memória | 8 GB | 16 GB
   
7. A **díjszabásban**:
    - Az **ajánlat/licencelés programban** válassza az Azure-ajánlatot, ha regisztrálva van. Jelenleg csak az utólagos elszámolású és az utólagos elszámolású fejlesztési/tesztelési lehetőség közül választhat. 
        - További kedvezményt is igénybe vehet, ha foglalt kapacitást alkalmaz, és az utólagos elszámolású ajánlaton Azure Hybrid Benefit. 
        - Az utólagos elszámolású fejlesztési és tesztelési Azure Hybrid Benefit is alkalmazhatja. Az értékelés jelenleg nem támogatja a fenntartott kapacitás alkalmazását az utólagos elszámolású fejlesztési és tesztelési ajánlaton felül.
    - A **szolgáltatási** szinten válassza a legmegfelelőbb szolgáltatási csomag lehetőséget, hogy megfeleljen az üzleti igényeknek a Azure SQL Database és/vagy az Azure SQL felügyelt példányának áttelepítéséhez: 
        - Válassza a **javasolt** lehetőséget, ha azt szeretné, hogy a Azure Migrate a kiszolgálók számára legmegfelelőbb szolgáltatási szintet javasolja. Ez lehet általános célú vagy üzleti szempontból kritikus. További információ
        - Válassza a **általános célú** lehetőséget, ha egy olyan Azure SQL-konfigurációt szeretne használni, amely költségvetésen alapuló számítási feladatokhoz készült.
        - Válassza a **üzletileg kritikus** lehetőséget, ha olyan Azure SQL-konfigurációt szeretne használni, amely kis késleltetésű számítási feladatokhoz készült, nagy rugalmasságot biztosítva a hibákhoz és a gyors feladatátvételekhez.
    - A **kedvezmény (%)** területen adja meg az Azure-ajánlaton keresztül kapott előfizetés-specifikus kedvezményeket. Az alapértelmezett beállítás 0%.
    - A **Pénznem** területen válassza ki a fiókja számlázási pénznemét.
    - A **Azure Hybrid Benefit** területen határozza meg, hogy már rendelkezik-e SQL Server licenccel. Ha így tesz, és a SQL Server-előfizetések aktív frissítési garanciája van érvényben, akkor a licencek az Azure-ba való beszerzése után a Azure Hybrid Benefit is alkalmazható.
    - Ha módosítja a módosításokat, kattintson a Save (Mentés) gombra.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Mentés gomb az értékelés tulajdonságainál":::
8. A **kiszolgálók értékelése** > kattintson a Tovább gombra.
9.  A **kiszolgálók kiválasztása az**  >  **értékelés nevének** kiértékeléséhez > adja meg az értékelés nevét.
10. A **válasszon ki vagy hozzon létre egy csoportot** > válassza az **új létrehozása** elemet, és adja meg a csoport nevét.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Új csoport gombjának helye":::
11. Válassza ki a készüléket, és válassza ki a csoportba felvenni kívánt kiszolgálókat. Ezután kattintson a Tovább gombra.
12. A **felülvizsgálat** és Értékelés létrehozása lapon tekintse át az értékelés részleteit, majd kattintson az értékelés létrehozása elemre a csoport létrehozásához és az értékelés futtatásához.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="A felülvizsgálat és az értékelés létrehozása gomb.":::
13. Az értékelés létrehozása után nyissa meg a **Windows, a Linux és a SQL Server**  >  **Azure Migrate: felderítés és értékelés** csempét > kattintson az Azure SQL Assessment melletti számra.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Navigáció a létrehozott értékeléshez":::
15. Kattintson a megtekinteni kívánt értékelési névre.

> [!NOTE]
> Mivel az Azure SQL-értékelések teljesítmény-alapú felmérések, javasoljuk, hogy várjon legalább egy napot a felderítés megkezdése után az értékelés létrehozása előtt. Ezzel a teljesítménnyel kapcsolatos adatok nagyobb megbízhatósággal gyűjthetők. Ha a felderítés még folyamatban van, az SQL-példányok készültsége **ismeretlenként** lesz megjelölve. Ideális esetben a felderítés megkezdése után **várjon a megadott teljesítményi időtartamra (nap/hét/hónap)** a magas megbízhatóságú minősítés értékelésének létrehozásához vagy újraszámításához. 

## <a name="review-an-assessment"></a>Értékelés áttekintése

**Értékelés megtekintése**:

1. **Windows, Linux és SQL Server**  >  **Azure Migrate: felderítés és értékelés** > kattintson az Azure SQL Assessment melletti számra.
2. Kattintson a megtekinteni kívánt értékelési névre. Példa (csak becslések és költségek): az :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="SQL Assessment áttekintése":::
3. Tekintse át az értékelés összegzését. Szerkesztheti az értékelési tulajdonságokat is, vagy újraszámíthatja az értékelést.

#### <a name="discovered-items"></a>Felderített elemek

Ez az értékelésben értékelt SQL-kiszolgálók,-példányok és-adatbázisok számát jelzi.
    
#### <a name="azure-readiness"></a>Az Azure használatához szükséges állapot

Ez a vizsgált SQL-példányok eloszlását jelzi: 
    
**Cél központi telepítési típus (az értékelés tulajdonságainál)** | **Készenlét**   
--- | --- |
**Ajánlott** |  Készen áll a Azure SQL Databasere, és készen áll a felügyelt Azure SQL-példányra, akár az Azure-beli virtuális gépekre, akár a készültségre (ha a felderítés folyamatban van, vagy bizonyos felderítési problémák megoldódott
**Azure SQL db** vagy **Azure SQL mi** | Készen áll a Azure SQL Database vagy az Azure SQL felügyelt példányára, nem áll készen a Azure SQL Database vagy az Azure SQL felügyelt példányára, a készültség ismeretlen (ha a felderítés folyamatban van, vagy bizonyos felderítési problémák vannak kijavítva)
     
Az Azure SQL-re való áttelepítés előtt megtekintheti az áttelepítési problémák/figyelmeztetések részletes ismertetését. [További információ](concepts-azure-sql-assessment-calculation.md) Az Azure SQL Database-adatbázisok és/vagy a felügyelt példányok áttelepítéséhez ajánlott Azure SQL-konfigurációkat is áttekintheti.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Azure SQL Database és felügyelt példányok részletei

A havi költségbecslés magában foglalja az ajánlott Azure SQL Database és/vagy az Azure SQL felügyelt példány központi telepítési típusának megfelelő Azure SQL-konfigurációk számítási és tárolási költségeit. [További információ](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Készültség áttekintése

1. Kattintson az **Azure SQL-készültség** elemre.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Azure SQL-készültség részletei":::
1. Az Azure SQL Readiness szolgáltatásban tekintse át az **Azure SQL db Readiness** és az **Azure SQL mi készültségét** az értékelt SQL-példányok esetében:
    - **Készen** áll: a példány készen áll arra, hogy áttelepítési problémák vagy figyelmeztetések nélkül áttelepítse az Azure SQL db/mi-ra. 
        - Ready (hiperhivatkozással ellátott és kék információs ikon): a példány készen áll az Azure SQL DB/MI áttelepítésre az áttelepítés során felmerülő problémák nélkül, de áttelepítési figyelmeztetéseket kell áttekintenie. A hivatkozásra kattintva áttekintheti az áttelepítési figyelmeztetéseket és az ajánlott szervizelési útmutatót: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="értékelés üzemkész állapottal":::       
    - **Nem áll készen**: a példánynak egy vagy több áttelepítési problémája van az Azure SQL db/mi szolgáltatásba való Migrálás során. Kattintson a hivatkozásra, és tekintse át az áttelepítési problémákat és a javasolt szervizelési útmutatást.
    - **Ismeretlen**: Azure Migrate nem tudja felmérni a felkészültséget, mert a felderítés folyamatban van, vagy a felderítés során problémák merülnek fel, amelyeket az értesítések panelen kell kijavítani. If the issue persists, please contact Microsoft support. (Az Azure Key Vault-művelet meghiúsult. Próbálja meg újból végrehajtani a műveletet. Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.) 
1. Tekintse át a javasolt központi telepítési típust az SQL-példányhoz, amely az alábbi mátrix alapján van meghatározva:

    - **Cél központi telepítési típus** (az értékelés tulajdonságaiban kiválasztva): **ajánlott**

        **Azure SQL DB-készültség** | **Azure SQL MI készültség** | **Ajánlott központi telepítési típus** | **Kiszámítja az Azure SQL konfigurációját és becsült költségeit?**
         --- | --- | --- | --- |
        Kész | Kész | [További információ](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) az Azure SQL dB-ben vagy az Azure SQL-ben | Yes
        Kész | Nem üzemkész vagy ismeretlen | Azure SQL DB | Yes
        Nem üzemkész vagy ismeretlen | Kész | Azure SQL MI | Yes
        Nem áll készen | Nem áll készen | Lehetséges, hogy az Azure-beli virtuális gép [még többet szeretne megtudni](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        Nem üzemkész vagy ismeretlen | Nem üzemkész vagy ismeretlen | Ismeretlen | No
    
    - **Cél központi telepítési típus** (az értékelés tulajdonságaiban kiválasztva): **Azure SQL db**
    
        **Azure SQL DB-készültség** | **Kiszámítja az Azure SQL konfigurációját és becsült költségeit?**
        --- | --- |
        Kész | Yes
        Nem áll készen | Nem
        Ismeretlen | No
    
    - **Cél központi telepítési típus** (az értékelés tulajdonságaiban kiválasztva): **Azure SQL mi**
    
        **Azure SQL MI készültség** | **Kiszámítja az Azure SQL konfigurációját és becsült költségeit?**
         --- | --- |
        Kész | Yes
        Nem áll készen | Nem
        Ismeretlen | No

4. A példány neve részletezés elemre kattintva megtekintheti a felhasználói adatbázisok számát, a példányok részleteit, például a példány tulajdonságait, a számítást (a példány hatókörét) és a forrás-adatbázis tárolási részleteit.
5. Kattintson a felhasználói adatbázisok számára az adatbázisok listájának és a hozzájuk tartozó adatok áttekintéséhez. Példa (csak becslések és költségek például): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="SQL-példány részletei":::
5. Az áttelepítési problémák oszlopban kattintson a részletek áttekintése elemre, és tekintse át az áttelepítési problémákat és az adott cél központi telepítési típusra vonatkozó figyelmeztetéseket. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="ADATBÁZIS-áttelepítési problémák és figyelmeztetések":::

### <a name="review-cost-estimates"></a>Költségbecslések áttekintése
Az értékelés összegzése az ajánlott Azure SQL-adatbázisok és/vagy a felügyelt példányok központi telepítési típusának megfelelő Azure SQL-konfigurációk becsült havi számítási és tárolási költségeit mutatja be.

1. Tekintse át a havi teljes költséget. A költségek összesítése az összes SQL-példány esetében történik a vizsgált csoportban.
    - A díjak becslése egy példányhoz tartozó ajánlott Azure SQL-konfiguráción alapul. 
    - A becsült havi számítási és tárolási költségek jelennek meg. Példa (csak becslések és költségek például):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Cost details":::

1. Egy példány szintjén részletezheti az Azure SQL-konfigurációt és a költségbecslést a példány szintjén.  
1. Az adatbázisok listájának részletezésével is megtekintheti az Azure SQL-konfigurációt és a költségbecslést egy adatbázison, ha Azure SQL Database konfiguráció javasolt.

### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése
Azure Migrate megbízhatósági minősítést rendel az összes Azure SQL-értékeléshez az összes értékelt SQL-példány és-adatbázis értékelésének kiszámításához szükséges teljesítmény-/kihasználtsági adatpontok rendelkezésre állása alapján. Az értékelés az egyik csillagból (legalacsonyabb) és öt csillagra (a legmagasabbra) mutat.
A megbízhatósági minősítés segít megbecsülni a méretre vonatkozó ajánlások megbízhatóságát az értékelés során. A megbízhatósági minősítések a következők:

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További](concepts-azure-sql-assessment-calculation.md#confidence-ratings) információ a megbízhatósági minősítésekről.


## <a name="next-steps"></a>Következő lépések

- [További](concepts-azure-sql-assessment-calculation.md) információ az Azure SQL-értékelések kiszámításáról.
- Megkezdheti az SQL-példányok és-adatbázisok áttelepítését [Azure Database Migration Service](../dms/dms-overview.md)használatával.