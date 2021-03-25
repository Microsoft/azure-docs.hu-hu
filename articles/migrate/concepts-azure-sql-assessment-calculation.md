---
title: Azure SQL-értékelések Azure Migrate felderítési és értékelési eszközben
description: Tudnivalók az Azure SQL-értékelésekről Azure Migrate felderítési és értékelési eszközben
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: 2bb68c8c183e3de132bc7cf4504714a260ea2683
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025118"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Értékelés áttekintése (Migrálás az Azure SQL-be)

Ez a cikk áttekintést nyújt a helyszíni SQL Server-példányok VMware-környezetből Azure SQL Database-be vagy felügyelt példányokra való áttelepítésének értékeléséről a [Azure Migrate: Discovery and Assessment Tool](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool)használatával.

## <a name="whats-an-assessment"></a>Mi az értékelés?
A felderítési és értékelési eszközzel végzett értékelés az adatok időbeli pillanatfelvétele, amely a felkészültséget méri, és a helyszíni kiszolgálók Azure-ba történő áttelepítésének hatását becsüli.

## <a name="types-of-assessments"></a>Az értékelések típusai

A Azure Migrate: Discovery and Assessment Tool használatával Háromféle értékelést hozhat létre.

**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> A helyszíni kiszolgálókat felhasználhatja [VMware](how-to-set-up-appliance-vmware.md) -és [Hyper-V](how-to-set-up-appliance-hyper-v.md) környezetben, valamint [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) az Azure-beli virtuális gépekre való áttelepítéshez ezzel az értékelési típussal.
**Azure SQL** | A helyszíni SQL Server-kiszolgálók VMware-környezetből Azure SQL Database vagy az Azure SQL felügyelt példányba való átköltöztetésének értékelése.
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> A helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md) az értékelés típusának használatával értékelheti az Azure VMware-megoldásba (AVS) való áttelepítésre. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)

Az Azure SQL Assessment egy méretezési feltételt biztosít:

**Méretezési feltételek** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A gyűjtött teljesítményalapok alapján javaslatot tevő kiértékelések | Az Azure SQL-konfiguráció az SQL-példányok és-adatbázisok teljesítményadatait tartalmazza, beleértve a CPU-kihasználtságot, a memória kihasználtságát, a IOPS (adatok és naplófájlok), az átviteli sebességet és az IO-műveletek késését.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Hogyan értékelni a helyszíni SQL Server-kiszolgálókat?

A helyszíni SQL Server példányokat egy egyszerűsített Azure Migrate berendezés által gyűjtött konfigurációs és kihasználtsági adatok használatával értékelheti ki. A készülék felfedi a helyszíni SQL Server-példányokat és-adatbázisokat, és elküldi a konfigurációs és teljesítményadatokat Azure Migrate. [További információ](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Hogyan az értékelést a berendezéssel?
Ha Azure Migrate berendezést helyez üzembe a helyszíni kiszolgálók felderítése érdekében, hajtsa végre a következő lépéseket:
1.  Az Azure és a helyszíni környezet beállítása a Azure Migrate való együttműködéshez.
2.  Az első értékeléshez hozzon létre egy Azure Migrate projektet, és adja hozzá a Azure Migrate: Discovery and Assessment eszközt.
3.  Egyszerűsített Azure Migrate berendezés üzembe helyezése. A készülék folyamatosan felfedi a helyszíni kiszolgálókat, és a konfigurációs és teljesítményadatokat Azure Migrate küldi. Telepítse a készüléket virtuális gépre vagy fizikai kiszolgálóra. Semmit nem kell telepítenie az értékelni kívánt kiszolgálókon.

Miután a berendezés megkezdte a felderítést, összegyűjthet olyan kiszolgálókat, amelyeket fel szeretne mérni egy csoportba, és értékelést futtathat a csoportnak az **Azure SQL** felmérési típussal.

Kövesse az oktatóanyagot a [SQL Server-példányok](tutorial-assess-sql.md) kiértékeléséhez, hogy kipróbálja ezeket a lépéseket.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Hogyan számítja ki a készülék az SQL-példányok és-adatbázisok teljesítményadatait?

A berendezés teljesítményadatokat gyűjt a számítási beállításokhoz a következő lépésekkel:
1. A készülék valós idejű mintavételi pontot gyűjt. Az SQL-kiszolgálók esetében a rendszer 30 másodpercenként gyűjt egy mintavételi pontot.
2. A készülék 10 percen belül összesíti az összegyűjtött mintavételezési pontokat. Az adatpont létrehozásához a készülék kiválasztja az összes minta csúcsérték-értékeit. Az Azure minden számlálójának maximális, középértékét és eltérését küldi el.
3. A Azure Migrate az elmúlt hónap 10 percenkénti adatpontjait tárolja.
4. Értékelés létrehozásakor Azure Migrate azonosítja a megadásában használandó megfelelő adatpontot. Az azonosítás a teljesítmény előzményeinek és a percentilis kihasználtságának százalékos értékein alapul.
    - Ha például a teljesítmény előzményei egy hét, a percentilis kihasználtsága pedig a 95. percentilis, az értékelés az elmúlt hét 10 perces mintavételi pontjait rendezi. Növekvő sorrendben rendezi őket, és kiválasztja a 95. percentilis értékét a megadásában.
    - A 95. percentilis értéke biztosítja, hogy figyelmen kívül hagyja a kiugró adatokat, amelyek akkor szerepelhetnek, ha kiválasztotta a esetek 99% percentilis értékét.
    - Ha ki szeretné választani az időszakhoz tartozó csúcsérték-használatot, és nem szeretne kiugró értékeket kihagyni, válassza a esetek 99% percentilis értéket a percentilis kihasználtsága mezőben.
5. Ennek az értéknek a megszorozza a komforttal, hogy a készülék által összegyűjtött mérőszámok tényleges teljesítmény-kihasználtsági adatai meglegyenek:
    - CPU-kihasználtság (%)
    - Memória kihasználtsága (%)
    - IO/s olvasása és írási IO/s (adatfájlok és naplófájlok)
    - Olvasási MB/s és írási MB/s (átviteli sebesség)
    - I/o-műveletek késése

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Milyen tulajdonságokat kell használni az Azure SQL-felmérések létrehozásához és testreszabásához?

Itt látható az Azure SQL Assessment tulajdonságai:

**Tulajdonság** | **Részletek**
--- | ---
**Célhely** | Az az Azure-régió, amelyre az áttelepíteni kívánja. Az Azure SQL-konfiguráció és a Cost-javaslatok a megadott helyen alapulnak.
**Cél központi telepítési típus** | A cél központi telepítési típus, amelynek értékelését futtatni szeretné: <br/><br/> Válassza az **ajánlott** lehetőséget, ha azt szeretné, hogy a Azure Migrate felmérje az SQL-kiszolgálók készültségét az Azure SQL mi és az Azure SQL Database szolgáltatásba való áttelepítéshez, és javasolja a legmegfelelőbb célként megadott üzembe helyezési lehetőséget, a célként megadott szintet, az Azure SQL-konfigurációt és a havi becsléseket.<br/><br/>Válassza az **Azure SQL db** lehetőséget, ha az SQL-kiszolgálókat csak az Azure SQL Database-adatbázisok áttelepítésére szeretné felmérni, és tekintse át a célként megadott szintet, az Azure SQL db konfigurációját és a havi becsléseket.<br/><br/>Válassza az **Azure SQL mi** lehetőséget, ha szeretné felmérni az SQL-kiszolgálókat csak az Azure SQL Database-adatbázisok áttelepítésére, és tekintse át a célként megadott szintet, az Azure SQL mi konfigurációját és a havi becsléseket.
**Fenntartott kapacitás** | Meghatározza a fenntartott kapacitást, hogy az értékelésben szereplő költségbecslés figyelembe vegye azokat.<br/><br/> Ha a fenntartott kapacitás lehetőséget választja, nem adhatja meg a "kedvezmény (%)" beállítást.
**Méretezési feltételek** | Ez a tulajdonság az Azure SQL konfigurációjának jobb méretezésére szolgál. <br/><br/> Alapértelmezés szerint a **teljesítmény-alapú** , ami azt jelenti, hogy az értékelés összegyűjti a SQL Server példányok és adatbázisok teljesítménymutatóit, hogy az optimális méretű Azure SQL felügyelt példányt és/vagy Azure SQL Database réteg/konfiguráció javaslatot javasolja.
**Teljesítményelőzmények** | A teljesítmény előzményei a teljesítményadatok kiértékeléséhez használt időtartamot határozzák meg.
**Százalékos kihasználtság** | A percentilis kihasználtsága meghatározza a megadásában használt teljesítmény mintájának százalékos értékét.
**Kényelmi faktor** | Az értékelés során használt puffer. A szolgáltatás olyan problémákhoz vezetett, mint például a szezonális használat, a rövid teljesítménybeli előzmények és a jövőbeli használat valószínű növekedése.<br/><br/> Például a 20%-os kihasználtságú 10 Magos példány általában egy kétmagos példányt eredményez. A 2,0-es kényelmi tényezővel az eredmény egy négy Magos példány.
**Ajánlat/licencelési program** | Az [Azure-ajánlat](https://azure.microsoft.com/support/legal/offer-details/) , amelyben regisztrálva van. Jelenleg csak az utólagos elszámolású és az utólagos elszámolású fejlesztési/tesztelési lehetőség közül választhat. Vegye figyelembe, hogy további kedvezményt is igénybe vehet, ha foglalt kapacitást alkalmaz, és az utólagos elszámolású ajánlaton Azure Hybrid Benefit.
**Szolgáltatási szint** | A legmegfelelőbb szolgáltatási szintű lehetőség a Azure SQL Database és/vagy az Azure SQL felügyelt példányának áttelepítéséhez szükséges üzleti igények kielégítésére:<br/><br/>**Ajánlott** , ha azt szeretné, hogy a Azure Migrate javasolja a kiszolgálók számára legmegfelelőbb szolgáltatási szintet. Ez lehet általános célú vagy üzleti szempontból kritikus. <br/><br/> **Általános célú** Ha olyan Azure SQL-konfigurációt szeretne, amely költségvetés-alapú számítási feladatokhoz készült. [További információ](https://docs.microsoft.com/azure/azure-sql/database/service-tier-general-purpose) <br/><br/> **Üzletileg kritikus** Ha olyan Azure SQL-konfigurációt szeretne használni, amely kis késleltetésű számítási feladatokhoz készült, nagy rugalmassággal és gyors feladatátvételsel. [További információ](https://docs.microsoft.com/azure/azure-sql/database/service-tier-business-critical)
**Pénznem** | A fiók számlázási pénzneme.
**Kedvezmény (%)** | Az Azure-ajánlaton felül kapott előfizetés-specifikus kedvezmények. Az alapértelmezett beállítás 0%.
**Azure Hybrid Benefit** | Megadja, hogy már rendelkezik-e SQL Server licenccel. <br/><br/> Ha így tesz, és a SQL Server-előfizetések aktív frissítési garanciája van érvényben, akkor a licencek az Azure-ba való beszerzése után a Azure Hybrid Benefit is alkalmazható.

[Tekintse át az ajánlott eljárásokat](best-practices-assessment.md) az értékelés létrehozásához Azure Migratesal.

## <a name="calculate-readiness"></a>Készültség kiszámítása

> [!NOTE]
Az értékelés csak online állapotú adatbázisokat tartalmaz. Ha az adatbázisok bármilyen más állapotban vannak, az értékelés figyelmen kívül hagyja a kompatibilitás-, méretezési és költségszámítását az ilyen adatbázisokra vonatkozóan. Ha szeretné értékelni ezeket az adatbázisokat, módosítsa az adatbázis állapotát, és számítsa újra az értékelést kis idő elteltével.

### <a name="azure-sql-readiness"></a>Azure SQL-felkészültség

Az SQL-példányokhoz és-adatbázisokhoz készült Azure SQL-készültség a Azure SQL Database és az Azure SQL felügyelt példányának szolgáltatás-kompatibilitási ellenőrzése alapján történik:
1. Az Azure SQL Assessment a kompatibilitási problémák azonosításához a forrás SQL Server munkaterhelések (SQL-ügynök feladatai, csatolt kiszolgálók stb.) és a felhasználói adatbázisok sémái (táblák, nézetek, triggerek, tárolt eljárások stb.) által jelenleg használt SQL Server példány-funkciókat veszi figyelembe.
1. Ha nem találhatók kompatibilitási problémák, a készültség a cél központi telepítési típusra (Azure SQL Database vagy az Azure SQL felügyelt példányára) **kész állapotban** van megjelölve.
1. Ha vannak olyan nem kritikus kompatibilitási problémák, mint például a csökkentett teljesítményű vagy nem támogatott funkciók, amelyek nem gátolják meg az áttelepítést egy adott cél központi telepítési típusra, **a készenléti** állapot a **Figyelmeztetési** részletekkel és a javasolt szervizelési útmutatással van megjelölve (hiperhivatkozásos és kék információs ikon).
1. Ha vannak olyan kompatibilitási problémák, amelyek letiltják az áttelepítést egy adott cél központi telepítési típusra, a készültség **nem áll készen** a **probléma** részleteivel és a javasolt szervizelési útmutatással.
    - Ha egy SQL-példányban még egy adatbázis van, amely nem áll készen egy adott cél központi telepítési típusra, a példány **nem áll készen** a központi telepítési típusra.
1. Ha a felderítés még folyamatban van, vagy bármilyen felderítési probléma van egy SQL-példány vagy-adatbázis esetében, a készenléti állapot **ismeretlenként** van megjelölve, mivel az értékelés nem tudja kiszámítani az adott SQL-példány készültségét.

### <a name="recommended-deployment-type"></a>Ajánlott központi telepítési típus

Ha a cél központi telepítési típust az Azure SQL Assessment tulajdonságaiban **javasolt** módon választja, Azure MIGRATE az SQL-példánnyal kompatibilis Azure SQL-telepítési típust javasol. A Microsoft által ajánlott célra való Migrálás csökkenti a teljes áttelepítési erőfeszítést. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Ajánlott központi telepítési típus az Azure SQL Readiness alapján

 **Azure SQL DB-készültség** | **Azure SQL MI készültség** | **Ajánlott központi telepítési típus** | **Kiszámítja az Azure SQL konfigurációját és becsült költségeit?**
 --- | --- | --- | --- |
 Kész | Kész | Azure SQL DB vagy <br/>Azure SQL MI | Igen
 Kész | Nem áll készen, vagy<br/> Ismeretlen | Azure SQL DB | Igen
 Nem áll készen, vagy<br/>Ismeretlen | Kész | Azure SQL MI | Igen
 Nem áll készen | Nem áll készen | Potenciálisan készen áll az Azure-beli virtuális gépekre | Nem
 Nem áll készen, vagy<br/>Ismeretlen | Nem áll készen, vagy<br/>Ismeretlen | Ismeretlen | Nem

> [!NOTE]
> Ha az ajánlott központi telepítési típus az értékelés tulajdonságainál **javasolt** , és ha a forrás SQL Server jól illeszkedik az Azure SQL db önálló adatbázisához és az Azure SQL felügyelt példányaihoz, az értékelés egy adott lehetőséget javasol, amely optimalizálja a költségeket, és a méret és a teljesítmény határain belül elfér.

#### <a name="potentially-ready-for-azure-vm"></a>Potenciálisan készen áll az Azure-beli virtuális gépekre

Ha az SQL-példány nem áll készen a Azure SQL Database és az Azure SQL felügyelt példányára, az ajánlott központi telepítési típus az Azure-beli *virtuális gép számára potenciálisan készként* van megjelölve.
- A felhasználónak javasoljuk, hogy hozzon létre egy értékelést Azure Migrate az "Azure VM" kifejezéssel, amely meghatározza, hogy a példányt futtató kiszolgáló készen áll-e az Azure-beli virtuális gépekre való áttelepítésre. Vegye figyelembe:
    - Az Azure-beli virtuális gépek felmérései a Azure Migrate jelenleg nem összpontosítanak, és nem fogják figyelembe venni az SQL-példányok és-adatbázisok Azure-beli virtuális gépen való futtatásának adott teljesítmény-mérőszámait. 
    - Amikor egy Azure-beli virtuális gépre irányuló értékelését egy kiszolgálón futtat, az ajánlott méret és a költségbecslések a kiszolgálón futó összes példányra vonatkoznak, és a példányok a Kiszolgáló migrálása eszközzel migrálhatók Azure-beli virtuális gépre. A migrálás előtt [tekintse át a teljesítményre vonatkozó irányelveket](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/performance-guidelines-best-practices) az Azure-beli virtuális gépeken futó SQL Serverhez kapcsolódóan.


## <a name="calculate-sizing"></a>Méretezés kiszámítása

### <a name="azure-sql-configuration"></a>Azure SQL-konfiguráció

Miután az értékelés meghatározza a készültséget és az ajánlott Azure SQL-telepítési típust, kiszámítja az adott szolgáltatási szintet és az Azure SQL konfigurációját (SKU-méret), amely képes megfelelni vagy meghaladni a helyszíni SQL-példány teljesítményét:
1. A felderítési folyamat során a Azure Migrate a következőket gyűjti az SQL-példányok konfigurációját és teljesítményét:
    - Virtuális mag (lefoglalt) és CPU-kihasználtság (%)
        - Az SQL-példányok CPU-kihasználtsága az SQL Server-példány által használt lefoglalt CPU százalékaránya.
        - Az adatbázis CPU-kihasználtsága az SQL-példány adatbázisa által felhasznált lefoglalt CPU százalékaránya.
    - Memória (lefoglalt) és memória kihasználtsága (%)
    - IO/s olvasása és írási IO/s (adatfájlok és naplófájlok)
        - Az i/o-k és az írási IO/s beolvasása az SQL-példány szintjén történik úgy, hogy hozzáadja az olvasási IO/s-t, és az adott példányban felderített összes adatbázis írási IO/mp-t.
    - Olvasási MB/s és írási MB/s (átviteli sebesség)
    - I/o-műveletek késése
    - Adatbázis teljes mérete és adatbázisfájlok szervezetei
        - Az adatbázis méretének kiszámítása az összes adatfájl és naplófájl hozzáadásával történik.
1. Az értékelés összefoglalja az összes konfigurációs és teljesítményadatokat, és megpróbálja megkeresni a legjobb egyezést a különböző Azure SQL-szolgáltatási szinteken és konfigurációkban, és olyan konfigurációt választ, amely megfelel az SQL-példány teljesítményére vonatkozó követelményeknek, és optimalizálja a költségeket.

### <a name="confidence-ratings"></a>Megbízhatósági minősítések 
Mindegyik Azure SQL-értékelés megbízhatósági minősítéssel van társítva. A minősítés az egyik (legalacsonyabb) és az öt (a legmagasabb) csillag közötti tartományba esik. A megbízhatósági minősítés segít megbecsülni a Azure Migrate által biztosított méretre vonatkozó ajánlások megbízhatóságát.
- A megbízhatósági minősítés egy értékeléshez van rendelve. A minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.
- A teljesítmény-alapú méretezéshez az értékelés az összes SQL-példány és-adatbázis teljesítményadatait gyűjti, többek között a következőket:
    - CPU-kihasználtság (%)
    - Memória kihasználtsága (%)
    - IO/s olvasása és írási IO/s (adatfájlok és naplófájlok)
    - Olvasási MB/s és írási MB/s (átviteli sebesség)
    - I/o-műveletek késése
    
Ha ezek a kihasználtsági számok nem érhetők el, a méretre vonatkozó javaslatok megbízhatatlanok lehetnek.
Ez a táblázat a kiértékelési megbízhatósági minősítéseket mutatja be, amelyek a rendelkezésre álló adatpontok százalékos arányán alapulnak:

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

#### <a name="low-confidence-ratings"></a>Alacsony megbízhatósági minősítések
Íme néhány ok, amiért egy értékelés alacsony megbízhatósági minősítést kaphat:
- Nem készítette el a környezet profilját abban az időtartamban, amelyre az értékelést létrehozta. Ha például az értékelést egy napra állítja be, akkor az összes adatpont felderítésének megkezdése után legalább egy napot várnia kell a begyűjtéshez.
- Az értékelés nem tud teljesítményadatokat gyűjteni az értékelési időszakban néhány kiszolgálóról vagy egyik kiszolgálóról sem. Magas megbízhatósági minősítés esetén ügyeljen a következőre:
    - A kiszolgálók az értékelés időtartamára vannak bekapcsolva
    - A 443-es portokon engedélyezett kimenő kapcsolatok engedélyezettek
    - Ha Azure Migrate a Azure Migrate SQL-ügynökének kapcsolati állapota "Connected", és ellenőrizze az utolsó szívverést 
    - Az Azure Migrate kapcsolati állapota mindegyik SQL-példány esetén „Csatlakozva” a felderített SQL-példány panelén

    Számítsa újra az értékelést, hogy tükrözze a megbízhatósági minősítés legújabb módosításait.
- Bizonyos adatbázisok vagy példányok az értékelés kiszámításának ideje alatt lettek létrehozva. Tegyük fel például, hogy létrehozta az előző hónap teljesítmény-előzményeinek értékelését, de néhány adatbázis vagy példány csak egy hetet hozott létre. Ebben az esetben az új kiszolgálókhoz tartozó teljesítményadatok nem lesznek elérhetők a teljes időtartamra, és a megbízhatósági minősítés alacsony lenne.

> [!NOTE]
> Mivel az Azure SQL-értékelések teljesítmény-alapú felmérések, ha az értékelések megbízhatósági minősítése kevesebb, mint öt csillag, javasoljuk, hogy várjon legalább egy napot, amíg a készülék felveszi a környezetet, majd újraszámítja az értékelést. Ellenkező esetben a teljesítmény-alapú méretezés megbízhatatlan lehet.

## <a name="calculate-monthly-costs"></a>Havi költségek kiszámítása
A méretezési javaslatok befejezése után az Azure SQL Assessment a belső díjszabási API használatával kiszámítja az ajánlott Azure SQL-konfigurációk számítási és tárolási költségeit. A teljes havi számítási költség kiszámításához összesíti a számítási és tárolási költségeket az összes példány között. 
### <a name="compute-cost"></a>Számítási díj
- Az Azure SQL-konfiguráció számítási költségeit az értékelés a következő tulajdonságokat veszi figyelembe:
    - SQL-licencek Azure Hybrid Benefit
    - Fenntartott kapacitás
    - Azure-beli cél helye
    - Pénznem
    - Ajánlat/licencelési program
    - Kedvezmény (%)

### <a name="storage-cost"></a>Tárolási költség
- A tárolási díjak becslése csak az adatfájlokat és a nem naplófájlokat tartalmazza. 
- Az Azure SQL-konfiguráció tárolási díjainak kiszámításához az értékelés a következő tulajdonságokat veszi figyelembe:
    - Azure-beli cél helye
    - Pénznem
    - Ajánlat/licencelési program
    - Kedvezmény (%)
- Az értékelés nem tartalmazza a biztonsági mentési tár költségeit.
- **Azure SQL Database**
    - Az 1 GB-os tárolási költségeket a költségbecslés során adja hozzá a rendszer a tárterülethez, és további tárhelyet is hozzáad a tárhelyhez. [További információ](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Felügyelt Azure SQL-példány**
    - Az első 32 GB/példány/hónap tárterülethez nem lett hozzáadva tárolási díj, és a tárhelyhez további tárhelyek is hozzáadódnak a 32 GB-os növekményekben. [További információ](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Következő lépések
- [Tekintse át](best-practices-assessment.md) az értékelések létrehozásával kapcsolatos ajánlott eljárásokat. 
- Ismerje meg, hogyan futtathat [Azure SQL-értékeléseket](how-to-create-azure-sql-assessment.md).
