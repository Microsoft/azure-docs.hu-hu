---
title: Mi a hyperscale szolgáltatási szint?
description: Ez a cikk a virtuálismag-alapú vásárlási modellben a Azure SQL Database-beli virtuálismag-alapú szolgáltatási szintet ismerteti, és elmagyarázza, miben különbözik a általános célú és üzletileg kritikus szolgáltatási szinttől.
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
ms.openlocfilehash: e0982b4a43a931552574e447d5639d3fa92402d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773771"
---
# <a name="hyperscale-service-tier"></a>Rugalmas skálázás szolgáltatási szint

Azure SQL Database SQL Server adatbázismotor architektúráján alapul, amely a felhőalapú környezethez igazítva biztosítja a 99,99%-os rendelkezésre állást még az infrastruktúra meghibásodása esetén is. Három architekturális modell használható a Azure SQL Database:

- általános célú/Standard
- Rugalmas skálázás
- üzletileg kritikus/Prémium

A virtuálismag-alapú Azure SQL Database szolgáltatásszint a virtuálismag-alapú vásárlási modell legújabb szolgáltatásszinte. Ez a szolgáltatási szint egy nagymértékben skálázható tárolási és számítási teljesítményszint, amely az Azure-architektúra segítségével skálázhatja fel horizontálisan a tárolási és számítási erőforrásokat egy olyan Azure SQL Database esetében, amely jelentősen meghaladja az általános célú és üzletileg kritikus szolgáltatási szintek korlátait.

> [!NOTE]
>
> - A virtuálismag-alapú vásárlási modell általános célú és üzletileg kritikus szolgáltatási rétegekkel kapcsolatos részletekért [](service-tier-general-purpose.md) lásd a általános célú és üzletileg kritikus [szolgáltatási](service-tier-business-critical.md) szintekről. A virtuálismag-alapú vásárlási modell és a DTU-alapú vásárlási modell összehasonlításához tekintse meg a [következőt:](purchasing-models.md)Azure SQL Database vásárlási modellek és erőforrások.
> - A hyperscale szolgáltatási szint jelenleg csak a Azure SQL Database érhető el, a Azure SQL Managed Instance.

## <a name="what-are-the-hyperscale-capabilities"></a>Mik a hiperméretű képességek?

A szolgáltatáscsomagban a Azure SQL Database a következő további képességeket biztosítja:

- Akár 100 TB méretű adatbázis támogatása
- Közel azonnali adatbázis-biztonsági mentések (az Azure Blob Storage-ban tárolt fájl-pillanatképek alapján) mérettől függetlenül, a számítási erőforrások I/O-hatása nélkül  
- Gyors adatbázis-visszaállítás (fájl-pillanatképek alapján) órák vagy napok helyett (nem adatművelet mérete) percek alatt
- Nagyobb általános teljesítmény a naplók nagyobb átviteli sebességének és gyorsabb tranzakció-véglegesítési időknek köszönhetően, az adatkötettől függetlenül
- Gyors horizontális felskálazás – egy vagy több csak olvasható csomópontot is kiépíthet az olvasási számítási feladatok kiszervezéséhez és készenléti tartalékként való használathoz
- Gyors felskálaolás – folyamatosan skálázhatja a számítási erőforrásokat, hogy szükség esetén nagy számítási feladatokhoz igazodjon, majd szükség esetén leskálásos méretezést is futtathat.

A hyperscale szolgáltatási szint eltávolítja a felhőalapú adatbázisokban hagyományosan látott számos gyakorlati korlátot. Ahol a többi adatbázist az egyetlen csomóponton elérhető erőforrások korlátozása korlátozza, a hyperscale szolgáltatási szinten található adatbázisokra nem vonatkoznak ilyen korlátok. Rugalmas tárolási architektúrájával a tárterület szükség szerint növekszik. A hiperméretű adatbázisok nem egy meghatározott maximális mérettel vannak létrehozva. A hiperméretű adatbázisok szükség szerint nőnek , és csak a használt kapacitásért kell fizetni. Az olvasásigényes számítási feladatok esetében a hyperscale szolgáltatásszint gyors horizontális felskálát biztosít, mivel további olvasási replikákat is kiépít az olvasási számítási feladatok kiszervezéséhez.

Emellett az adatbázis biztonsági mentéséhez vagy a fel- vagy leskálához szükséges idő már nincs az adatbázisban az adatok mennyiségéhez kötve. A nagy kapacitású adatbázisok biztonsági mentése gyakorlatilag azonnali. Az adatbázisokat több tíz terabájtban is skálázhatja percek alatt. Ezzel a képességgel nem kell attól aggódik, hogy a kezdeti konfigurációs lehetőségek be vannak-e ásva.

A hyperscale szolgáltatási szint számítási méretével kapcsolatos további információkért lásd: [Szolgáltatási szint jellemzői.](service-tiers-vcore.md#service-tiers)

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Ki vegye figyelembe a hyperscale szolgáltatási szintet?

A rugalmas skálázású szolgáltatási szint a legtöbb üzleti számítási feladathoz használható, mivel nagy rugalmasságot és nagy teljesítményt nyújt függetlenül méretezhető számítási és tárolási erőforrásokkal. A tárterület akár 100 TB-ig való automatikus skálázásának lehetősége kiváló választás az olyan ügyfelek számára, akik:

- Nagy méretű adatbázisok a helyszínen, és szeretnék modernizálni az alkalmazásokat a felhőbe való áthelyezés során
- Már a felhőben vannak, és a többi szolgáltatási szint maximális adatbázisméret-korlátozásai (1–4 TB) korlátozva vannak
- Kisebb adatbázisokhoz, de gyors vertikális és horizontális számítási skálázást, nagy teljesítményt, azonnali biztonsági mentést és gyors adatbázis-visszaállítást igényelnek.

A nagy kapacitású szolgáltatási szint az SQL Server számítási feladatok széles körét támogatja, a tiszta OLTP-től a tiszta elemzésen át a tiszta elemzésen át ag, de elsősorban OLTP- és hibrid tranzakciós és analitikus feldolgozási (HTAP) számítási feladatokra van optimalizálva.

> [!IMPORTANT]
> A rugalmas készletek nem támogatják a rugalmas skálázású szolgáltatási szintet.

## <a name="hyperscale-pricing-model"></a>Nagy kapacitású díjszabási modell

A magas skálázású szolgáltatási szint csak a [virtuálismag-alapú modellben érhető el.](service-tiers-vcore.md) Az új architektúrához való igazodáshoz a díjszabási modell kissé eltér a általános célú szolgáltatási üzletileg kritikus modelljétől:

- **Compute :**

  A nagy kapacitású számítási egység ára replikánkénti. A [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) a rendszer automatikusan alkalmazza a skálázható replikák olvasására. Alapértelmezés szerint egy elsődleges replikát és egy csak olvasható replikát hozunk létre egy hiperméretű adatbázisonként.  A felhasználók 1 és 5 között módosíthatják a replikák teljes számát, beleértve az elsődleges replikákat is.

- **Storage :**

  A hyperscale adatbázis konfigurálásakor nem kell megadnia a maximális adatméretet. A hiperméretű szinten az adatbázis tárolásáért a tényleges foglalás alapján kell fizetnie. A tárterület 10 GB-os növekményekben automatikusan 40 GB és 100 TB között van lefoglalva. Szükség esetén egyszerre több adatfájl is nőhet. A rendszer egy 10 GB-os kezdőméretű, 10 GB-os kezdőméretű adatbázist hoz létre, amely 10 percenként 10 GB-mal növekszik, amíg el nem éri a 40 GB-os méretet.

További információ a hyperscale díjszabásról: Azure SQL Database [díjszabása](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Elosztott függvények architektúrája

A hagyományos adatbázismotorokkal ellentétben, amelyek egyetlen helyen/folyamatban központosítottak az összes adatkezelési funkciót (az úgynevezett elosztott adatbázisok éles környezetben jelenleg a monolitikus adatmotor több példányát is biztosítják), a hiperméretű adatbázis elkülöníti a lekérdezésfeldolgozó motort, ahol a különböző adatmotorok szemantikája eltér az adatok hosszú távú tárolását és tartósságát biztosítani tudó összetevőktől. Így a tárolási kapacitás szükség szerint zökkenőmentesen felskálizálható (a kezdeti cél 100 TB). A csak olvasható replikák ugyanazon a tárolási összetevőn osztoznak, ezért nincs szükség adatmásolatra az új, olvasható replika útjára való váltához.

Az alábbi ábra a különböző csomóponttípusokat mutatja be egy hiperméretű adatbázisban:

![architektúra](./media/service-tier-hyperscale/hyperscale-architecture.png)

A hiperméretű adatbázisok a következő típusú összetevőket tartalmaznak:

### <a name="compute"></a>Compute

A számítási csomópont az a hely, ahol a relációs motor található. Itt történik a nyelv, a lekérdezés és a tranzakciófeldolgozás. A hyperscale adatbázissal való minden felhasználói interakció ezeken a számítási csomópontokon keresztül történik. A számítási csomópontok SSD-alapú gyorsítótárral (az előző ábrán RBPEX – Resilient Buffer Pool Extension (RBPEX – Rugalmas pufferkészletbővítmény) címkével vannak megcímkézve) az adatlap beolvasásához szükséges hálózati adatforgalom minimálisra csökkentése érdekében. Van egy elsődleges számítási csomópont, ahol az összes olvasási-írási számítási feladat és tranzakció fel van feldolgozva. Van egy vagy több másodlagos számítási csomópont, amelyek készenléti készenléti csomópontként szolgálnak feladatátvételi célokra, valamint írásra alkalmas számítási csomópontként szolgálnak az olvasási számítási feladatok kiszervezéséhez (ha ez a funkció megfelelő).

A hiperméretű számítási csomópontokon futó adatbázismotor ugyanaz, mint a többi Azure SQL Database szolgáltatási szinten. Ha a felhasználók hiperméretű számítási csomópontokon kommunikálnak az adatbázismotorral, a támogatott felület és a motor viselkedése megegyezik a többi szolgáltatási szintével, az ismert korlátozások [kivételével.](#known-limitations)

### <a name="page-server"></a>Lapkiszolgáló

A lapkiszolgálók olyan rendszerek, amelyek egy horizontálisan felskálált tárolási motort jelképeznek.  Minden lapkiszolgáló felel az adatbázis lapjainak egy részéért.  Névlegesen minden lapkiszolgáló legfeljebb 128 GB vagy legfeljebb 1 TB adat felügyeletére szolgál. Egynél több lapkiszolgálón egyetlen adat sincs megosztva (azon a lapkiszolgáló-replikákon kívül, amelyek a redundancia és a rendelkezésre állás érdekében vannak megosztva). A lapkiszolgálók feladata, hogy az adatbázis-oldalakat igény szerint szolgálják ki a számítási csomópontoknak, és hogy a tranzakciófrissítési adatokkal folyamatosan frissítse a lapokat. A lapkiszolgálók a naplószolgáltatás naplórekordjainak lejátszása által naprakészek maradnak. A lapkiszolgálók emellett fenntartják az SSD-alapú gyorsítótárak lefedéseit is a teljesítmény növelése érdekében. Az adatlapok hosszú távú tárolása az Azure Storage-ban marad a nagyobb megbízhatóság érdekében.

### <a name="log-service"></a>Naplószolgáltatás

A naplószolgáltatás elfogadja az elsődleges számítási replikáról származó naplórekordokat, tartós gyorsítótárban marad, és továbbítja a naplórekordokat a többi számítási replikának (hogy frissítve legyenek a gyorsítótáraik), valamint a megfelelő oldalkiszolgáló(k)nak, hogy az adatok ott is frissíthetők legyenek. Így az elsődleges számítási replika összes adatváltozása a naplószolgáltatáson keresztül az összes másodlagos számítási replikára és lapkiszolgálóra propagálva lesz. Végül a naplórekordok hosszú távú tárolóba vannak kiküldve az Azure Storage-ban, amely egy gyakorlatilag végtelen tárház. Ezzel a mechanizmussal nincs szükség gyakori naplócsonkításra. A naplószolgáltatás helyi memóriával és SSD-gyorsítótárral is rendelkezik, amelyek felgyorsítják a naplórekordok hozzáférését.

### <a name="azure-storage"></a>Azure Storage tárterület

Az Azure Storage egy adatbázisban található összes adatfájlt tartalmazza. A lapkiszolgálók naprakészen tartják az Azure Storage-ban található adatfájlokat. Ez a tároló biztonsági mentésre, valamint Azure-régiók közötti replikációra szolgál. A biztonsági mentések adatfájlok tárolási pillanatképei alapján vannak megvalósítva. A pillanatképeket használó visszaállítási műveletek az adatmérettől függetlenül gyorsak. Az adatok bármely időpontra visszaállíthatóak az adatbázis biztonsági másolatának megőrzési időtartamán belül.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

A biztonsági másolatok fájl-pillanatképen alapulnak, ezért szinte azonnaliak. A tárolás és a számítás elkülönítése lehetővé teszi a biztonsági mentési/visszaállítási művelet leküldét a tárolási rétegbe az elsődleges számítási replika feldolgozási terhelésének csökkentése érdekében. Ennek eredményeképpen az adatbázis biztonsági mentése nincs hatással az elsődleges számítási csomópont teljesítményére. Hasonlóképpen, az időponthoz való helyreállítás (PITR) a fájl-pillanatképek visszaállításának műveletével történik, és így nem az adatművelet mérete. A több terabájtos adatbázisok visszaállítása ugyanabban az Azure-régióban állandó művelet, és akár több terabájtos adatbázisok is visszaállíthatóak órák vagy napok helyett percek alatt. Az új adatbázisok meglévő biztonsági másolat visszaállítással történő létrehozása szintén kihasználja ezt a funkciót: az adatbázis-másolatok létrehozása fejlesztési vagy tesztelési célokra akár több terabájtos adatbázisok esetén is percek alatt elérhető.

A nagy kapacitású adatbázisok georedurnálása: Hyperscale database to a different region (Hyperscale-adatbázis visszaállítása egy másik [régióba).](#restoring-a-hyperscale-database-to-a-different-region)

## <a name="scale-and-performance-advantages"></a>Méretezési és teljesítménybeli előnyök

A további, csak olvasható számítási csomópontok gyors fel-/le- és felugró módon való használatával a hyperscale architektúra jelentős olvasási skálázási képességeket tesz lehetővé, és az elsődleges számítási csomópontot is felszabadíthatja, hogy több írási kérést szolgáljon ki. Emellett a számítási csomópontok gyorsan skálázhatóak fel/le a nagy kapacitású architektúra megosztott tárolós architektúrája miatt.

## <a name="create-a-hyperscale-database"></a>Hyperscale-adatbázis létrehozása

A hyperscale adatbázis a következő parancsokkal [Azure Portal:](https://portal.azure.com) [, T-SQL,](/sql/t-sql/statements/create-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)vagy [parancssori felület.](/cli/azure/sql/db#az_sql_db_create) A nagy kapacitású adatbázisok csak a [virtuálismag-alapú vásárlási modellel érhetők el.](service-tiers-vcore.md)

A következő T-SQL-parancs egy hiperméretű adatbázist hoz létre. A kiadást és a szolgáltatási célt is meg kell adnia a `CREATE DATABASE` utasításban. Az érvényes szolgáltatási [célkitűzések listájáért](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) tekintse meg az erőforráskorlátokat.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Ez egy négymagos, 5. generációs hardveren létrehozott, nagy kapacitású adatbázist hoz létre.

## <a name="upgrade-existing-database-to-hyperscale"></a>Meglévő adatbázis frissítése a hyperscale-hez

A meglévő adatbázisokat áthelyezheti a Azure SQL Database a következő [Azure Portal,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/alter-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)vagy [CLI használatával:](/cli/azure/sql/db#az_sql_db_update). Jelenleg ez egy egyértes migrálás. Az adatbázisokat nem lehet áthelyezni a hiperméretű szolgáltatási szintről egy másik szolgáltatási szintre, csak az adatok exportálásával és importálásával. A koncepció igazolása (POC) érdekében javasoljuk, hogy másolatot készít az éles adatbázisokról, és a másolatot a hyperscale-be minkálja. A méretezési szinthez Azure SQL Database meglévő adatbázis áttelepítése az adatművelet mérete.

A következő T-SQL-parancs áthelyez egy adatbázist a hyperscale szolgáltatási szintre. A kiadást és a szolgáltatási célt is meg kell adnia a `ALTER DATABASE` utasításban.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Csatlakozás egy hiperméretű adatbázis olvasási skálázási replikához

A hiperméretű adatbázisokban az ügyfél által megadott kapcsolati sztring argumentuma határozza meg, hogy a kapcsolat az írási replikához vagy egy csak olvasható másodlagos replikához `ApplicationIntent` legyen irányítva. Ha a beállítása , és az adatbázis nem rendelkezik másodlagos replikával, a kapcsolat az elsődleges replikára lesz irányítva, és alapértelmezés szerint `ApplicationIntent` `READONLY` a viselkedésre van `ReadWrite` irányítva.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

A hiperméretű másodlagos replikák mind azonosak, és ugyanazt a szolgáltatásiszint-célkitűzést használják, mint az elsődleges replika. Ha több másodlagos replika is van, a számítási feladat el lesz osztva az összes elérhető másodlagos replikán. Minden másodlagos replika egymástól függetlenül frissül. Így a különböző replikák adatk késése az elsődleges replikához képest eltérő lehet.

## <a name="database-high-availability-in-hyperscale"></a>Az adatbázis magas rendelkezésre állása a hyperscale-hez

Mint minden más szolgáltatási szinten, a hyperscale a számítási replika rendelkezésre állásától függetlenül garantálja az adatok tartósságát a lekötött tranzakciókhoz. Az elsődleges replika elérhetetlenné válása miatti állásidő mértéke a feladatátvétel típusától (tervezett és nem tervezett) és legalább egy másodlagos replika jelenlététől függ. Tervezett feladatátvétel (például karbantartási esemény) esetén a rendszer vagy létrehozza az új elsődleges replikát a feladatátvétel megkezdése előtt, vagy egy meglévő másodlagos replikát használ feladatátvételi célként. Nem tervezett feladatátvétel esetén (vagyis az elsődleges replika hardverhibája esetén) a rendszer egy másodlagos replikát használ feladatátvételi célként, ha van ilyen, vagy új elsődleges replikát hoz létre a rendelkezésre álló számítási kapacitás készletében. Az utóbbi esetben az állásidő időtartama hosszabb az új elsődleges replika létrehozásához szükséges további lépések miatt.

A nagy skálázású SLA-val a [szolgáltatásiszint-szerződés (SLA) Azure SQL Database.](https://azure.microsoft.com/support/legal/sla/sql-database/)

## <a name="disaster-recovery-for-hyperscale-databases"></a>Hyperscale-adatbázisok vészhelyreállítása

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Hyperscale-adatbázis visszaállítása egy másik régióba

Ha az Azure SQL Database-ban található, nem az aktuálisan üzemeltetett régióban található, vészhelyreállítási művelet, működés, áthelyezés vagy egyéb ok miatt vissza kell állítania egy hiperméretű adatbázist, az elsődleges módszer az adatbázis georedukciója. Ez pontosan ugyanazokra a lépésekre vonatkozik, mint a másik régióban található SQL Database visszaállítása:

1. Hozzon [létre egy kiszolgálót](logical-servers.md) a célterületen, ha még nem rendelkezik megfelelő kiszolgálóval.  Ennek a kiszolgálónak ugyanannak az előfizetésnek kell lennie, mint az eredeti (forráskiszolgálónak).
2. Kövesse az oldal [georedúciós](./recovery-using-backups.md#geo-restore) témakörében található utasításokat az adatbázis automatikus biztonsági mentésből való Azure SQL Database visszaállításához.

> [!NOTE]
> Mivel a forrás és a cél különböző régiókban található, az adatbázis nem oszthatja meg a pillanatkép-tárolót a forrásadatbázissal, mint a nem geo-visszaállítások esetében, amelyek az adatbázis méretétől függetlenül gyorsan befejeződnek. A nagy kapacitású adatbázisok georedúciós visszaállítása akkor is adatméret-művelet lesz, ha a célhely a georeplikált tároló párosított régiójában van. Ezért a georedúnó-visszaállítás a visszaállított adatbázis méretével arányos időt fog tartani. Ha a cél a párosított régióban van, az adatátvitel egy régión belül történik, ami jelentősen gyorsabb lesz, mint a régiók közötti adatátvitel, de továbbra is adatméret-művelet lesz.

## <a name="available-regions"></a><a name=regions></a>Elérhető régiók

A Azure SQL Database szint minden régióban elérhető, de alapértelmezés szerint engedélyezve van az alábbi régiókban. Ha olyan régióban szeretne létrehozni egy hyperscale adatbázist, ahol a hyperscale alapértelmezés szerint nincs engedélyezve, elküldhet egy Azure Portal. Útmutatásért tekintse meg a [Kvótaemelések kérése a](quota-increase-request.md) Azure SQL Database útmutatásért. A kérelem elküldésekor használja az alábbi irányelveket:

- Használja [a Régió hozzáférése SQL Database](quota-increase-request.md#region) kvótatípust.
- A leírásban adja hozzá a számítási termékváltozatot/az összes magot, beleértve az olvasható replikákat is, és jelezze, hogy hyperscale-kapacitást kér.
- Emellett adja meg az összes adatbázis teljes méretének előrejelzését is a TB-ban megadott idő alatt.

Engedélyezett régiók:
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Ausztrália középső régiója
- Dél-Brazília
- Közép-Kanada
- Kelet-Kanada
- Az USA középső régiója
- Kína 2. keleti régiója
- Kína 2. északi régiója
- Kelet-Ázsia
- USA keleti régiója
- Usa 2. keleti régiója
- Közép-Franciaország
- Nyugat-Németország – Középső régió
- Kelet-Japán
- Nyugat-Japán
- Dél-Korea középső régiója
- Dél-Korea déli régiója
- USA északi középső régiója
- Észak-Európa
- Kelet-Kelet
- Nyugat-Nyugat-Németország
- Dél-Afrika északi régiója
- USA déli középső régiója
- Délkelet-Ázsia
- Nyugat-Svájc
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója
- US DoD – Középső régió
- US DoD – Kelet
- Us Govt Arizona
- US Govt Texas
- USA nyugati középső régiója
- Nyugat-Európa
- USA nyugati régiója
- USA 2. nyugati régiója

## <a name="known-limitations"></a>Ismert korlátozások

Ezek a magas skálázású szolgáltatási szint aktuális korlátozásai az ga ga verzióban.  Folyamatosan dolgozunk azon, hogy a lehető legtöbb korlátozást eltávolítsuk.

| Probléma | Description |
| :---- | :--------- |
| A kiszolgálók Biztonsági másolatok kezelése panele nem mutatja a hiperméretű adatbázisokat. Ezek a nézetből lesznek szűrve.  | A hyperscale külön módszert biztosít a biztonsági mentések kezelésére, így a Long-Term megőrzési és időponthoz megadott biztonsági mentések megőrzési beállításai nem érvényesek. Ennek megfelelően a hiperméretű adatbázisok nem jelennek meg a Biztonsági mentés kezelése panelen.<br><br>A más Azure SQL Database szolgáltatási szintekről a hyperscale-be migrált adatbázisok esetében az áttelepítés [](automated-backups-overview.md#backup-retention) előtti biztonsági másolatok a forrásadatbázis biztonsági másolatok megőrzési időszakára maradnak meg. Ezekkel a biztonsági másolatokkal visszaállítható a forrásadatbázis a migrálás előtti időpontra. [](recovery-using-backups.md#programmatic-recovery-using-automated-backups)|
| Adott időpontnak megfelelő helyreállítás | A nem nagy skálázású adatbázisok nem állíthatók vissza hiperméretű adatbázisként, és nem állíthatók vissza nem nagy kapacitású adatbázisként. A nem nagy skálázású adatbázisok esetében, amelyek a szolgáltatási szint módosításával migrálva vannak a skálázásba, a rendszer programozott módon támogatja a visszaállítást a migrálás előtti időpontra és az adatbázis biztonsági mentésének megőrzési időszakán [belül.](recovery-using-backups.md#programmatic-recovery-using-automated-backups) A visszaállított adatbázis nem lesz nagy skálázású. |
| Ha a Azure SQL Database szolgáltatásszintet hyperscale-re módosítja, a művelet meghiúsul, ha az adatbázis 1 TB-osnál nagyobb adatfájlokat tartalmaz | Bizonyos esetekben a probléma megoldásához a nagyméretű [](file-space-manage.md#shrinking-data-files) fájlok méretének 1 TB-os méretnél kisebbre zsugorítására van szükség, mielőtt megkísérli a szolgáltatásszintet hyperscale-re módosítani. Az adatbázisfájlok aktuális méretét a következő lekérdezéssel állapíthatja meg. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| SQL Managed Instance | Azure SQL Managed Instance skálázású adatbázisok jelenleg nem támogatottak. |
| Rugalmas készletek |  A rugalmas készletek jelenleg nem támogatottak a rugalmas skálázással.|
| A hyperscale-be való migrálás jelenleg egy egyértművelet | Az adatbázisok a hyperscale-be való migrálása után nem migrálható közvetlenül egy nem nagy kapacitású szolgáltatási szintre. Jelenleg az adatbázisok hyperscale-ről nem nagy skálázásúra való áttelepítésének egyetlen módja az, ha bacpac-fájllal vagy más adatátviteli technológiával exportál vagy importál (tömeges másolás, Azure Data Factory, Azure Databricks, SSIS stb.) A Bacpac exportálása/importálása a Azure Portal-ból, a PowerShellből a [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) vagy [a New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport)parancs [](/rest/api/sql/) használatával, az Azure CLI-ben az az sql [db export](/cli/azure/sql/db#az_sql_db_export) és az az [sql db import](/cli/azure/sql/db#az_sql_db_import)parancs használatával, REST API-ból nem támogatott. A bacpac importálása/exportálása kisebb (legfeljebb 200 GB-os) nagy kapacitású adatbázisok esetében az SSMS és az [SqlPackage](/sql/tools/sqlpackage) 18.4-es és újabb verzióinak használatával támogatott. Nagyobb adatbázisok esetén a BACPAC exportálása/importálása hosszú ideig is tart, és különböző okok miatt meghiúsulhat.|
| Adatbázisok áttelepítése OLTPIn-Memory objektumokkal | A hyperscale az OLTPIn-Memory objektumok egy részkészletét támogatja, beleértve a memóriaoptimalikus táblatípusokat, a táblaváltozókat és a natív módon lefordított modulokat. Ha azonban bármilyen OLTPIn-Memory objektum jelen van az áttelepített adatbázisban, a prémium és üzletileg kritikus szolgáltatási szintekről a hyperscale-be való migrálás nem támogatott. Az ilyen adatbázisok hyperscale-be való áttelepítéséhez az összes In-Memory OLTP-objektumot és azok függőségeit el kell dobni. Az adatbázis áttelepítése után ezek az objektumok újra létrehozhatóak. A rugalmas skálázás jelenleg nem támogatja a tartós és nem tartós memóriaoptimalikus táblákat, és lemeztáblákra kell módosítani.|
| Georeplikáció  | Még nem konfigurálhat georeplikációt a Azure SQL Database méretezéshez. |
| Adatbázis-másolat | A hyperscale adatbázis-másolata mostantól nyilvános előzetes verzióban érhető el. |
| Intelligens adatbázis-funkciók | A "Force Plan" (Terv kényszerítését) kivéve a hyperscale (Skálázás) funkció még nem támogatja az összes többi automatikus hangolási beállítást: előfordulhat, hogy a beállítások engedélyezve vannak, de nem lesznek javaslatok vagy műveletek. |
| Lekérdezési teljesítmény elemzése | A Lekérdezési teljesítményelemzés jelenleg nem támogatott a nagy kapacitású adatbázisokhoz. |
| Adatbázis zsugorítása | A DBCC SHRINKDATABASE DBCC SHRINKFILE a hyperscale adatbázisok jelenleg nem támogatottak. |
| Adatbázis-integritás ellenőrzése | A DBCC CHECKDB jelenleg nem támogatott a nagy kapacitású adatbázisok esetében. Megkerülő megoldásként a DBCC CHECKFILEGROUP és a DBCC CHECKTABLE használható. A [Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) adatintegritás kezelésével kapcsolatos részletekért lásd a Azure SQL Database. |

## <a name="next-steps"></a>Következő lépések

- A hyperscale-ről a Gyakori kérdések a hyperscale-ről című témakörben talál [gyakori kérdéseket.](service-tier-hyperscale-frequently-asked-questions-faq.md)
- További információ a szolgáltatásszintekkel kapcsolatban: [Szolgáltatásszintek](purchasing-models.md)
- A kiszolgáló és az előfizetés szintjén elérhető korlátokkal kapcsolatos információkért [lásd:](resource-limits-logical-server.md) A kiszolgáló erőforráskorlátának áttekintése.
- Az egy adatbázisra vonatkozó vásárlási modell korlátaiért lásd: [Azure SQL Database virtuálismag-alapú](resource-limits-vcore-single-databases.md)vásárlási modell korlátainak létrehozása egyetlen adatbázishoz.
- A funkciók és az összehasonlítási lista az [SQL gyakori funkcióit tartalmazza.](features-comparison.md)
