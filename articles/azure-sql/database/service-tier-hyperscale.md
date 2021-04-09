---
title: Mi a nagy kapacitású szolgáltatási szintje?
description: Ez a cikk a nagy kapacitású szolgáltatási rétegét ismerteti a Azure SQL Database virtuális mag-alapú vásárlási modellben, és ismerteti, hogy miben különbözik a általános célú és a üzletileg kritikus szolgáltatási rétegtől.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: a167fedcb42560dec55cdbce40e36180d65e0179
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951797"
---
# <a name="hyperscale-service-tier"></a>Rugalmas skálázás szolgáltatási szint

A Azure SQL Database a felhőalapú környezethez igazított SQL Server adatbázismotor-architektúrán alapul, hogy az infrastruktúra meghibásodása esetén is biztosítson 99,99%-os rendelkezésre állást. A Azure SQL Database három építészeti modellt használ:

- Általános célú/standard
- Rugalmas skálázás
- Üzletileg kritikus/prémium

A Azure SQL Database nagy kapacitású szolgáltatási szintje a legújabb szolgáltatási réteg a virtuális mag-alapú vásárlási modellben. Ez a szolgáltatási réteg egy rugalmasan méretezhető tárolási és számítási teljesítményi réteg, amely az Azure-architektúrát használja a általános célú és üzletileg kritikus szolgáltatási szinten elérhető korlátokon túlmenően egy Azure SQL Database számára.

> [!NOTE]
>
> - A virtuális mag-alapú vásárlási modell általános célú és üzletileg kritikus szolgáltatási szintjeivel kapcsolatos részletekért lásd: [általános célú](service-tier-general-purpose.md) és [üzletileg kritikus](service-tier-business-critical.md) szolgáltatási szintek. A virtuális mag-alapú vásárlási modellnek a DTU-alapú vásárlási modellel való összehasonlítását lásd: [Azure SQL Database vásárlási modellek és erőforrások](purchasing-models.md).
> - A nagy kapacitású szolgáltatási szintje jelenleg csak Azure SQL Database esetén érhető el, és nem felügyelt Azure SQL-példány.

## <a name="what-are-the-hyperscale-capabilities"></a>A nagy kapacitású képességei

A Azure SQL Database nagy kapacitású szolgáltatási szintje a következő kiegészítő képességeket nyújtja:

- Akár 100 TB-os adatbázis-méret támogatása
- Szinte azonnali adatbázis-biztonsági másolatok (az Azure Blob Storage-ban tárolt fájl-Pillanatképek alapján), függetlenül a számítási erőforrásokra vonatkozó IO-hatás nélküli mérettől  
- Gyors adatbázis-visszaállítások (fájl-Pillanatképek alapján) percek és napok helyett (nem az adatműveletek mérete)
- Nagyobb általános teljesítmény a nagyobb naplózási átviteli sebesség és a gyorsabb tranzakció-végrehajtási idő miatt, függetlenül az adatmennyiségtől
- Gyors kibővítés – kiépíthető egy vagy több írásvédett csomópont az olvasási számítási feladat kiszervezéséhez és a meleg készenléti használatra
- Gyors vertikális felskálázás – állandó időben, a számítási erőforrások vertikális felskálázásával nagy mennyiségű számítási feladatra lehet szükség, és a számítási erőforrások nem szükségesek.

A nagy kapacitású szolgáltatási szintje eltávolítja a Felhőbeli adatbázisokban hagyományosan látható gyakorlati korlátokat. Ahol a legtöbb más adatbázist az egyetlen csomópontban elérhető erőforrások korlátozzák, a nagy kapacitású szolgáltatási rétegében lévő adatbázisoknak nincsenek ilyen korlátai. Rugalmas tárolási architektúrája esetén a tárterület igény szerint növekszik. Valójában a nagy kapacitású-adatbázisok nem a megadott maximális mérettel jönnek létre. A nagy kapacitású-adatbázis szükség szerint növekszik – és csak a használt kapacitásért kell fizetnie. Az olvasási igényű számítási feladatokhoz a nagy kapacitású szolgáltatási réteg gyors méretezést biztosít az olvasási feladatok kiszervezéséhez szükséges további olvasási replikák kiosztásával.

Emellett az adatbázis biztonsági másolatainak létrehozásához vagy a vertikális felskálázáshoz szükséges idő már nem kötődik az adatbázisban lévő adatmennyiséghez. A nagy kapacitású-adatbázisok biztonsági mentése gyakorlatilag azonnal elvégezhető. Percek alatt akár több, akár több mint terabájtos méretű adatbázis is méretezhető. Ez a funkció felkínálja a kezdeti konfigurációs döntésekből származó, a bekeretezett funkciókkal kapcsolatos problémákat.

A nagy kapacitású szolgáltatási szintjéhez tartozó számítási méretekről további információt a [szolgáltatási szintek jellemzői](service-tiers-vcore.md#service-tiers)című témakörben talál.

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kinek érdemes figyelembe vennie a nagy kapacitású szolgáltatási szintet

A nagy kapacitású szolgáltatási szintje a legtöbb üzleti számítási feladathoz készült, mivel ez nagy rugalmasságot és nagy teljesítményt biztosít a egymástól függetlenül skálázható számítási és tárolási erőforrások esetében. A tárterület akár 100 TB-ig történő autoskálázásának lehetősége kiváló választás az olyan ügyfelek számára, akik:

- Nagyméretű adatbázisok vannak a helyszínen, és szeretnék modernizálni alkalmazásaikat a felhőre való áttéréssel
- Már a felhőben vannak, és az adatbázis maximális méretére vonatkozó korlátozások korlátozzák a többi szolgáltatási szintet (1-4 TB)
- Kisebb adatbázisok szükségesek, de gyors vertikális és horizontális számítási skálázásra, nagy teljesítményre, azonnali biztonsági mentésre és gyors adatbázis-visszaállításra van szükség.

A nagy kapacitású szolgáltatási szint számos SQL Server számítási feladatot támogat, a tiszta OLTP és a tiszta elemzésig, de elsődlegesen a OLTP, a hibrid tranzakciós és az analitikus feldolgozási (HTAP) számítási feladatokhoz van optimalizálva.

> [!IMPORTANT]
> A rugalmas készletek nem támogatják a nagy kapacitású szolgáltatási szintet.

## <a name="hyperscale-pricing-model"></a>Nagy kapacitású díjszabási modellje

A nagy kapacitású szolgáltatási szintje csak a [virtuális mag modellben](service-tiers-vcore.md)érhető el. Az új architektúrához való igazításhoz a díjszabási modell némileg eltér általános célú vagy üzletileg kritikus szolgáltatási szintjeitől:

- **Számítás**:

  A nagy kapacitású számítási egységének díja replika. A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) árát a rendszer automatikusan beolvassa a skálázási replikákat. Alapértelmezés szerint egy elsődleges replikát és egy írásvédett replikát hozunk létre nagy kapacitású-adatbázisban.  A felhasználók a replikák teljes számát módosíthatják, beleértve az elsődleges 1-5-as értéket is.

- **Tárterület**:

  A nagy kapacitású-adatbázis konfigurálásakor nem kell megadnia a maximális adatméretet. A nagy kapacitású szinten a tényleges foglalás alapján kell fizetnie az adatbázis tárolásához. A tárterületet a rendszer automatikusan 40 GB és 100 TB között osztja el 10 GB-os növekményekben. Szükség esetén egyszerre több adatfájl is növekedhet. A nagy kapacitású-adatbázis 10 GB-os kezdő mérettel jön létre, és 10 percenként 10 GB-onként növekszik, amíg el nem éri a 40 GB-os méretet.

További információ a nagy kapacitású díjszabásáról: [Azure SQL Database díjszabása](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Elosztott függvények architektúrája

Az egy helyen/folyamatban lévő összes adatkezelési funkciót központosító hagyományos adatbázis-kezelővel ellentétben (még az éles környezetben is megjelenő elosztott adatbázisok több példányban rendelkeznek egy monolitikus adatmotorral), a nagy kapacitású-adatbázis elválasztja a lekérdezés-feldolgozó motort, ahol a különböző adatmotorok szemantikaa az adatok hosszú távú tárolását és tartósságát biztosító összetevőktől eltér. Így a tárolási kapacitás igény szerint zökkenőmentesen méretezhető (a kezdeti cél 100 TB). Az írásvédett replikák ugyanazokat a tárolási összetevőket használják, ezért nincs szükség adatmásolásra egy új, olvasható replika kipörgetéséhez.

A következő ábra a nagy kapacitású-adatbázisok különböző típusait szemlélteti:

![architektúra](./media/service-tier-hyperscale/hyperscale-architecture.png)

A nagy kapacitású-adatbázisok a következő különböző típusú összetevőket tartalmazzák:

### <a name="compute"></a>Compute

A számítási csomópont az a hely, ahol a viszonyítási motor él. A nyelv, a lekérdezés és a tranzakció feldolgozása zajlik. A nagy kapacitású-adatbázissal folytatott összes felhasználói interakció ezen számítási csomópontokon keresztül történik. A számítási csomópontok SSD-alapú gyorsítótárral rendelkeznek (az előző ábrán megcímkézett RBPEX-rugalmasságú puffer-bővítmény), így minimálisra csökkenthető az adatoldalak lekéréséhez szükséges hálózati kör-átutazások száma. Létezik egy elsődleges számítási csomópont, amelyben az összes írási és olvasási feladat feldolgozásra kerül. Van egy vagy több olyan másodlagos számítási csomópont, amely gyors készenléti csomópontként működik feladatátvételi célokra, valamint írásvédett számítási csomópontként működik az olvasási feladatok kiszervezéséhez (ha ez a funkció szükséges).

A nagy kapacitású számítási csomópontjain futó adatbázismotor ugyanaz, mint a többi Azure SQL Database szolgáltatási szinten. Ha a felhasználók a nagy kapacitású számítási csomópontjain kommunikálnak az adatbázis-motorral, akkor a támogatott felszíni terület és a motor működése azonos a többi szolgáltatási szinten, az [ismert korlátozások](#known-limitations)kivételével.

### <a name="page-server"></a>Lapozófájl

A kiszolgálóoldali kiszolgálók egy kibővített tárolási motort jelképező rendszerek.  Az egyes lapozófájlok felelősek az adatbázis lapjainak egy részhalmaza számára.  Névlegesen az egyes kiszolgálók legfeljebb 128 GB-ot vagy akár 1 TB-ot is szabályoznak. A rendszer nem osztja meg az adatmegosztást egynél több kiszolgálóoldali kiszolgálón (a lapozófájl-replikán kívül, amelyet a redundancia és a rendelkezésre állás érdekében tart fenn). Az oldal-kiszolgáló feladata az, hogy az adatbázis-lapokat a számítási csomópontok igény szerint kiszolgálják, és hogy a lapok frissítve legyenek a tranzakciók frissítési adataiként. Az oldal-kiszolgálók naprakészek maradnak a log szolgáltatásban található rekordok lejátszásával. Az oldal-kiszolgálók a teljesítmény növelése érdekében az SSD-alapú gyorsítótárak karbantartását is tartalmazzák. Az adatlapok hosszú távú tárolása az Azure Storage-ban további megbízhatóságot biztosít.

### <a name="log-service"></a>Naplózási szolgáltatás

A naplózási szolgáltatás fogadja az elsődleges számítási replika naplófájljait, tartós gyorsítótárban tárolja azokat, és továbbítja a napló rekordjait a többi számítási replikához (a gyorsítótárak frissítéséhez), valamint a kapcsolódó oldal-kiszolgáló (ka) t, hogy az adatok ott is frissíthetők legyenek. Így az elsődleges számítási replika összes adatváltozása a naplózási szolgáltatáson keresztül továbbítódik a másodlagos számítási replikák és a lapok kiszolgálójára. Végül a naplóbejegyzések a hosszú távú tárolásra kerülnek az Azure Storage-ban, ami gyakorlatilag végtelen tárolóhely. Ez a mechanizmus eltávolítja a gyakori naplózási csonkítás szükségességét. A log szolgáltatás helyi memóriával és SSD-gyorsítótárral is rendelkezik a naplókhoz való hozzáférés felgyorsításához.

### <a name="azure-storage"></a>Azure Storage tárterület

Az Azure Storage egy adatbázis összes adatfájlját tartalmazza. Az oldal-kiszolgálók naprakészen tárolják az adatfájlokat az Azure Storage szolgáltatásban. Ez a tároló biztonsági mentési célokra, valamint az Azure-régiók közötti replikációra szolgál. A biztonsági mentések az adatfájlok tárolási pillanatképei használatával valósíthatók meg. A pillanatképeket használó visszaállítási műveletek gyorsak, függetlenül az adatok méretétől. Az adatok bármikor visszaállíthatók az adatbázis biztonsági másolatának megőrzési időszakán belül bármely időpontra.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A biztonsági másolatok a fájl-pillanatkép-alapúak, és így szinte azonnal megtalálhatók. A tárolási és a számítási elkülönítés lehetővé teszi a biztonsági mentési/visszaállítási művelet lenyomását a tárolási rétegre, hogy csökkentse az elsődleges számítási replika feldolgozási terhelését. Ennek eredményeképpen az adatbázis biztonsági mentése nem befolyásolja az elsődleges számítási csomópont teljesítményét. Hasonlóképpen, az időponthoz való helyreállítás (PITR) a fájl-Pillanatképek visszaállításával történik, és ez nem az adatműveletek mérete. Az ugyanabban az Azure-régióban található nagy kapacitású-adatbázisok visszaállítása állandó művelet, és akár több terabájtos adatbázis is visszaállítható óra vagy nap helyett percek alatt. Új adatbázisok létrehozása egy meglévő biztonsági másolat visszaállításával is kihasználhatja ezt a funkciót: adatbázis-másolatok létrehozása fejlesztési vagy tesztelési célból, akár több terabájtos adatbázis esetén is, percek alatt megvalósítható.

A nagy kapacitású adatbázisok geo-visszaállításával kapcsolatban lásd: [nagy kapacitású-adatbázis visszaállítása egy másik régióba](#restoring-a-hyperscale-database-to-a-different-region).

## <a name="scale-and-performance-advantages"></a>Méretezési és teljesítménybeli előnyök

A további írásvédett számítási csomópontok gyors üzembe helyezésének képessége lehetővé teszi, hogy a nagy kapacitású architektúra jelentős olvasási méretezési képességeket biztosítson, és felszabadítsa az elsődleges számítási csomópontot a további írási kérelmek kiszolgálásához. A számítási csomópontokat a nagy kapacitású architektúra megosztott tárolási architektúrája miatt gyorsan fel-és leskálázással is elvégezheti.

## <a name="create-a-hyperscale-database"></a>Nagy kapacitású-adatbázis létrehozása

Nagy kapacitású-adatbázis hozható létre a [Azure Portal](https://portal.azure.com), a [T-SQL](/sql/t-sql/statements/create-database-transact-sql), a [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)vagy a [parancssori](/cli/azure/sql/db#az-sql-db-create)felület használatával. A nagy kapacitású-adatbázisok csak a [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md)használatával érhetők el.

A következő T-SQL-parancs egy nagy kapacitású-adatbázist hoz létre. Az utasításban meg kell adni a kiadás és a szolgáltatás célját is `CREATE DATABASE` . Tekintse át az érvényes szolgáltatási célkitűzések listáját az [erőforrás-korlátok között](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) .

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Ez egy nagy kapacitású-adatbázist hoz létre a Gen5 hardveren négy maggal.

## <a name="upgrade-existing-database-to-hyperscale"></a>Meglévő adatbázis frissítése nagy kapacitású

A meglévő adatbázisokat Azure SQL Database nagy kapacitású helyezheti át a [Azure Portal](https://portal.azure.com), a [T-SQL](/sql/t-sql/statements/alter-database-transact-sql), a [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)vagy a [CLI](/cli/azure/sql/db#az-sql-db-update)használatával. Ebben az esetben ez egy egyirányú áttelepítés. Az adatbázisok nem helyezhetők át a nagy kapacitású egy másik szolgáltatási szintjére, kivéve az adatok exportálását és importálását. A koncepciók (Pócs) igazolására azt javasoljuk, hogy készítsen másolatot az éles adatbázisokról, és a másolást a nagy kapacitású-be. Ha egy meglévő adatbázist Azure SQL Database a nagy kapacitású-csomagba telepít át, az adatművelet mérete.

A következő T-SQL parancs egy adatbázist helyez át a nagy kapacitású szolgáltatási szintjére. Az utasításban meg kell adni a kiadás és a szolgáltatás célját is `ALTER DATABASE` .

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Kapcsolódás nagy kapacitású-adatbázis egy olvasási léptékű replikához

A nagy kapacitású-adatbázisokban az `ApplicationIntent` ügyfél által megadott kapcsolódási karakterlánc argumentuma azt határozza meg, hogy a Kapcsolódás az írási replikához vagy egy írásvédett másodlagos replikához van-e irányítva. Ha a `ApplicationIntent` beállítása `READONLY` és az adatbázis nem rendelkezik másodlagos replikával, a rendszer a kapcsolódást az elsődleges replikához irányítja, és az alapértelmezett viselkedést fogja megadni `ReadWrite` .

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

A nagy kapacitású másodlagos replikái azonosak az elsődleges replikával megegyező szolgáltatási szintű célkitűzés használatával. Ha egynél több másodlagos replika van jelen, a munkaterhelés az összes rendelkezésre álló formátumú másodlagos zónák el lesz osztva. Minden másodlagos replika egymástól függetlenül frissül. Így a különböző replikák eltérő adatkéséssel rendelkezhetnek az elsődleges replikához képest.

## <a name="database-high-availability-in-hyperscale"></a>Adatbázis magas rendelkezésre állása a nagy kapacitású-ben

Ahogy az összes többi szolgáltatási szinten is, a nagy kapacitású garantálja az adatok tartósságát a Véglegesített tranzakciók esetében, a számítási replika rendelkezésre állása nélkül. A elérhetetlenné váló elsődleges replika miatti leállások mértéke a feladatátvétel típusától (tervezett vagy nem tervezett) függ, és legalább egy másodlagos replika jelenléte. Tervezett feladatátvétel (azaz karbantartási esemény) esetén a rendszer vagy az új elsődleges replikát hozza létre a feladatátvétel kezdeményezése előtt, vagy egy meglévő másodlagos replikát használ feladatátvételi célként. Nem tervezett feladatátvétel esetén (például hardverhiba az elsődleges replikán) a rendszer másodlagos replikát használ feladatátvételi célként, ha van ilyen, vagy létrehoz egy új elsődleges replikát a rendelkezésre álló számítási kapacitás készletből. Az utóbbi esetben a leállás időtartama hosszabb az új elsődleges replika létrehozásához szükséges további lépések miatt.

A nagy kapacitású SLA esetében lásd: [Azure SQL Database SLA](https://azure.microsoft.com/support/legal/sla/sql-database/)-ja.

## <a name="disaster-recovery-for-hyperscale-databases"></a>Vész-helyreállítási nagy kapacitású-adatbázisokhoz

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Nagy kapacitású-adatbázis visszaállítása egy másik régióba

Ha Azure SQL Database egy nagy kapacitású-adatbázist kell visszaállítani egy olyan régióba, amely nem a jelenleg üzemeltetett, akkor a vész-helyreállítási művelet vagy a részletezés, az áthelyezés vagy más ok miatt az elsődleges módszer az adatbázis geo-visszaállításának elvégzése. Ez pontosan ugyanazokat a lépéseket foglalja magában, mint amelyeket a SQL Database más adatbázisainak egy másik régióba való visszaállításához használ:

1. Ha még nem rendelkezik megfelelő kiszolgálóval, hozzon létre egy [kiszolgálót](logical-servers.md) a célként megadott régióban.  Ennek a kiszolgálónak ugyanahhoz az előfizetéshez kell tartoznia, mint az eredeti (forrás) kiszolgálónak.
2. Kövesse az oldal [geo-visszaállítási](./recovery-using-backups.md#geo-restore) témakörében található utasításokat a Azure SQL Database-adatbázisok automatikus biztonsági mentésből történő visszaállításához.

> [!NOTE]
> Mivel a forrás és a cél különálló régiókban található, az adatbázis nem tudja megosztani a pillanatkép-tárolót a forrás-adatbázissal a nem geo-visszaállítások esetében, ami az adatbázis méretétől függetlenül gyorsan elvégezhető. Ha a nagy kapacitású-adatbázis geo-visszaállítást végez, akkor akkor is adatmennyiség-adatművelet lesz, ha a cél a földrajzilag replikált tároló párosított régiójában található. Ezért a Geo-visszaállítás a visszaállítani kívánt adatbázis méretével arányos időt vesz igénybe. Ha a cél a párosított régióban van, az adatátvitel egy régión belül történik, ami jelentősen gyorsabb lesz, mint a régiók közötti adatátvitel, de továbbra is adatmennyiség-adatátviteli művelet marad.

## <a name="available-regions"></a><a name=regions></a>Elérhető régiók

Az Azure SQL Database nagy kapacitású szinten minden régióban elérhető, de alapértelmezés szerint engedélyezve van az alábbi régiókban. Ha nagy kapacitású-adatbázist szeretne létrehozni egy olyan régióban, ahol a nagy kapacitású alapértelmezés szerint nincs engedélyezve, a Azure Portalon keresztül küldhet bevezetési kérést. Útmutatásért lásd a [kérelmekre vonatkozó kvóta növelését Azure SQL Database](quota-increase-request.md) útmutatásért. A kérelem elküldésekor kövesse az alábbi irányelveket:

- Használja a [régió-hozzáférés](quota-increase-request.md#region) SQL Database kvóta típusát.
- A leírásban adja hozzá a számítási SKU/Total magokat, beleértve az olvasható replikákat, és jelezze, hogy nagy kapacitású-kapacitást kér.
- Adja meg az összes adatbázis teljes méretének kivetítését is a TB-ban.

Engedélyezett régiók:
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Ausztrália középső régiója
- Dél-Brazília
- Közép-Kanada
- Az USA középső régiója
- Kelet-Kína 2
- Észak-Kína 2
- Kelet-Ázsia
- USA keleti régiója
- USA 2. keleti régiója
- Közép-Franciaország
- Középnyugat-Németország
- Kelet-Japán
- Nyugat-Japán
- Dél-Korea középső régiója
- Dél-Korea déli régiója
- USA északi középső régiója
- Észak-Európa
- Kelet-Norvégia
- Norvégia nyugati régiója
- Dél-Afrika északi régiója
- USA déli középső régiója
- Délkelet-Ázsia
- Nyugat-Svájc
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója
- US DoD – Középső régió
- US DoD – Kelet
- USA-beli kormányok és Arizona
- USA-beli kormányok Texas
- USA nyugati középső régiója
- Nyugat-Európa
- USA nyugati régiója
- USA 2. nyugati régiója

## <a name="known-limitations"></a>Ismert korlátozások

Ezek a nagy kapacitású szolgáltatási szintjére vonatkozó jelenlegi korlátozások a GA-ban.  Aktívan dolgozunk azon, hogy minél több korlátozást távolítson el.

| Probléma | Leírás |
| :---- | :--------- |
| A kiszolgálók biztonsági mentések panelje nem jeleníti meg a nagy kapacitású-adatbázisokat. Ezek a nézet alapján lesznek szűrve.  | A nagy kapacitású külön módszert biztosít a biztonsági mentések kezeléséhez, így a Long-Term megőrzési és időponthoz tartozó biztonsági mentési adatmegőrzési beállítások nem érvényesek. Ennek megfelelően a nagy kapacitású-adatbázisok nem jelennek meg a biztonsági mentés kezelése ablaktáblán.<br><br>A más Azure SQL Database nagy kapacitású áttelepített adatbázisok esetében a rendszer megőrzi az áttelepítés előtti biztonsági mentéseket a forrásadatbázis [biztonsági mentési megőrzési](automated-backups-overview.md#backup-retention) idejének időtartamára. Ezek a biztonsági másolatok a forrásadatbázis az áttelepítés előtti időpontra történő [visszaállítására](recovery-using-backups.md#programmatic-recovery-using-automated-backups) használhatók.|
| Adott időpontnak megfelelő helyreállítás | Nem nagy kapacitású adatbázis nem állítható vissza nagy kapacitású-adatbázisként, és a nagy kapacitású-adatbázis nem állítható vissza nem nagy kapacitású-adatbázisként. Egy olyan nem nagy kapacitású adatbázis esetében, amely a nagy kapacitású való áttelepítéssel lett áttelepítve, és az adatbázis biztonsági mentésének megőrzési időszakán belül egy adott időpontra van állítva, a rendszer [programozott](recovery-using-backups.md#programmatic-recovery-using-automated-backups)módon támogatja a visszaállítást. A visszaállított adatbázis nem nagy kapacitású lesz. |
| Azure SQL Database szolgáltatási réteg nagy kapacitású való módosításakor a művelet meghiúsul, ha az adatbázisnak 1 TB-nál nagyobb adatfájlja van | Bizonyos esetekben előfordulhat, hogy a problémát úgy lehet megkerülni, hogy [a nagyméretű](file-space-manage.md#shrinking-data-files) fájlokat kevesebb mint 1 TB-ra csökkenti, mielőtt nagy kapacitású a szolgáltatási szintet. Az adatbázisfájlok aktuális méretének meghatározásához használja az alábbi lekérdezést. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL Managed Instance | Az Azure SQL felügyelt példánya jelenleg nem támogatott a nagy kapacitású-adatbázisokban. |
| Rugalmas készletek |  A rugalmas készletek jelenleg nem támogatottak a nagy kapacitású.|
| A nagy kapacitású-re történő áttelepítés jelenleg egyirányú művelet | Miután az adatbázist áttelepítette a nagy kapacitású, az nem telepíthető át közvetlenül egy nem nagy kapacitású szolgáltatási rétegre. Jelenleg az adatbázisnak a nagy kapacitású-ből a nem nagy kapacitású-be való migrálása a bacpac-fájl vagy más adatáthelyezési technológiák (tömeges másolás, Azure Data Factory, Azure Databricks, SSIS stb.) használatával történik. Bacpac exportálás/Azure Portal Importálás a PowerShellből a [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) vagy a [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport)használatával az Azure CLI-ből az [az SQL db export](/cli/azure/sql/db#az-sql-db-export) és [az sql db import](/cli/azure/sql/db#az-sql-db-import)használatával, és a [REST API](/rest/api/sql/) nem támogatott. A kisebb nagy kapacitású-adatbázisok (akár 200 GB) Bacpac importálását és exportálását a SSMS és a [SqlPackage](/sql/tools/sqlpackage) 18,4-es vagy újabb verziója támogatja. Nagyobb adatbázisok esetén a bacpac-Exportálás/-Importálás hosszú időt vehet igénybe, és különböző okok miatt sikertelen lehet.|
| Adatbázisok áttelepítése In-Memory OLTP-objektumokkal | A nagy kapacitású támogatja In-Memory OLTP objektumok egy részhalmazát, beleértve a memóriára optimalizált táblák típusát, a táblázat változóit és a natív módon lefordított modulokat. Ha azonban bármilyen In-Memory OLTP-objektum szerepel az áttelepített adatbázisban, a prémium és a üzletileg kritikus szolgáltatási szintjeiről a nagy kapacitású-re történő áttelepítés nem támogatott. Ha egy ilyen adatbázist át szeretne telepíteni a nagy kapacitású-be, az összes In-Memory OLTP objektumot és a hozzájuk tartozó függőségeket el kell dobni. Az adatbázis migrálása után ezek az objektumok újra létrehozhatók. A tartós és nem tartós, memóriára optimalizált táblák jelenleg nem támogatottak a nagy kapacitású-ben, és a lemezes táblákra kell módosítani.|
| Georeplikáció  | Azure SQL Database nagy kapacitású esetében még nem konfigurálhatja a Geo-replikációt. |
| Adatbázis másolása | A nagy kapacitású adatbázis-másolata már nyilvános előzetes verzióban érhető el. |
| Intelligens adatbázis-funkciók | A "kényszerített terv" lehetőség kivételével az összes többi Automatikus hangolási beállítás még nem támogatott a nagy kapacitású: a beállítások engedélyezhetők, de nem lesznek javaslatok vagy műveletek. |
| Lekérdezési teljesítmény elemzése | A nagy kapacitású adatbázisok esetében jelenleg nem támogatott a lekérdezési teljesítmény. |
| Adatbázis zsugorítása | A DBCC SHRINKDATABASE vagy DBCC SHRINKFILE jelenleg nem támogatott nagy kapacitású-adatbázisok esetén. |
| Adatbázis integritásának ellenőrzése | A DBCC CHECKDB UTASÍTÁST jelenleg nem támogatott nagy kapacitású-adatbázisok esetén. A DBCC CHECKFILEGROUP és a DBCC CHECKTABLE UTASÍTÁST megkerülő megoldásként is használható. A Azure SQL Database adatintegritás-kezelésével kapcsolatos részletekért tekintse meg a [Azure SQL Database adatok integritását](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) ismertető témakört. |

## <a name="next-steps"></a>Következő lépések

- A nagy kapacitású kapcsolatos gyakori [kérdésekért](service-tier-hyperscale-frequently-asked-questions-faq.md)lásd: nagy kapacitású.
- További információ a szolgáltatási szintekről: [szolgáltatási szintek](purchasing-models.md)
- A kiszolgálók és az előfizetési szintek korlátaival kapcsolatos információkért lásd: a [kiszolgálók erőforrás-korlátainak áttekintése](resource-limits-logical-server.md) .
- A modellre vonatkozó korlátok egyetlen adatbázishoz való megvásárlásával kapcsolatban lásd: [Azure SQL Database virtuális mag-alapú vásárlási modell korlátai egyetlen adatbázishoz](resource-limits-vcore-single-databases.md).
- A szolgáltatások és az összehasonlítások listájáért lásd: [általános SQL-szolgáltatások](features-comparison.md).
