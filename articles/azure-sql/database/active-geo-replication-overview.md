---
title: Aktív georeplikáció
description: Az aktív földrajzi replikálás használatával a Azure SQL Database önálló adatbázisaiból olvasható másodlagos adatbázisok hozhatók létre ugyanazon vagy különböző adatközpont-régiókban.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 08/27/2020
ms.openlocfilehash: 3a678f6280b5f2d0fd372e75bfbeb6eb2e9b1577
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634294"
---
# <a name="creating-and-using-active-geo-replication---azure-sql-database"></a>Active geo-Replication-Azure SQL Database létrehozása és használata
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az aktív geo-replikáció egy Azure SQL Database funkció, amely lehetővé teszi, hogy az azonos vagy különböző adatközpontokban (régiókban) lévő kiszolgálókon az egyes adatbázisok olvasható másodlagos adatbázisait hozzon létre.

> [!NOTE]
> Az Azure SQL felügyelt példánya nem támogatja az aktív geo-replikációt. Az SQL felügyelt példány példányainak földrajzi feladatátvételéhez használjon [automatikus feladatátvételi csoportokat](auto-failover-group-overview.md).

> [!NOTE]
> Ha az SQL-adatbázisokat Active geo-replikáció használatával szeretné áttelepíteni az Azure Germanyből, tekintse meg az [áttelepítés SQL Database az aktív földrajzi replikálás használatával](../../germany/germany-migration-databases.md#migrate-sql-database-using-active-geo-replication)című témakört

Az aktív földrajzi replikálás olyan üzletmenet-folytonossági megoldás, amely lehetővé teszi, hogy az alkalmazás az egyes adatbázisok gyors vész-helyreállítását regionális katasztrófa vagy nagy léptékű leállás esetén hajtsa végre. Ha a Geo-replikáció engedélyezve van, az alkalmazás feladatátvételt kezdeményezhet egy másik Azure-régióban található másodlagos adatbázisba. Akár négy formátumú másodlagos zónák is támogatott ugyanazon vagy különböző régiókban, és a formátumú másodlagos zónák csak olvasási hozzáférési lekérdezésekhez használható. A feladatátvételt az alkalmazásnak vagy a felhasználónak manuálisan kell kezdeményeznie. A feladatátvételt követően az új elsődleges végpont eltérő kapcsolódási ponttal rendelkezik.

> [!NOTE]
> Az aktív geo-replikáció replikálja a változásokat az adatfolyam-adatbázis tranzakciós naplója szerint. Nem kapcsolódik a [tranzakciós replikációhoz](/sql/relational-databases/replication/transactional/transactional-replication), amely a DML (INSERT, Update, DELETE) parancsok végrehajtásával replikálja a módosításokat.

Az alábbi ábra egy geo-redundáns felhőalapú alkalmazás tipikus konfigurációját szemlélteti az aktív geo-replikáció használatával.

![aktív geo-replikáció](./media/active-geo-replication-overview/geo-replication.png )

> [!IMPORTANT]
> A SQL Database támogatja az automatikus feladatátvételi csoportokat is. További információ: [automatikus feladatátvételi csoportok](auto-failover-group-overview.md)használata.

Ha bármilyen okból kifolyólag az elsődleges adatbázis meghibásodik, vagy egyszerűen offline állapotba kell helyezni, a feladatátvételt bármely másodlagos adatbázisba elindíthatja. Ha a feladatátvételt az egyik másodlagos adatbázisra aktiválja, az összes többi formátumú másodlagos zónák automatikusan az új elsődlegeshez lesz kapcsolva.

Az egyes adatbázisok replikálását és feladatátvételét kezelheti egy kiszolgálón vagy egy rugalmas készletben az aktív földrajzi replikálás használatával. Ezt a következőket teheti:

- A [Azure Portal](active-geo-replication-configure-portal.md)
- [PowerShell: önálló adatbázis](scripts/setup-geodr-and-failover-database-powershell.md)
- [PowerShell: rugalmas készlet](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- [Transact-SQL: önálló adatbázis vagy rugalmas készlet](/sql/t-sql/statements/alter-database-azure-sql-database)
- [REST API: önálló adatbázis](/rest/api/sql/replicationlinks)

Az aktív geo-replikáció az adatbázismotor [Always On rendelkezésre állási csoportjának](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) technológiáját használja, hogy a pillanatkép-elkülönítés használatával aszinkron módon replikálja a véglegesített tranzakciókat az elsődleges adatbázison egy másodlagos adatbázisba. Az automatikus feladatátvételi csoportok lehetővé teszik a csoportos szemantika használatát az aktív földrajzi replikáláson, de ugyanazt az aszinkron replikációs módszert használják. Amíg a másodlagos adatbázis egy adott ponton nem lehet az elsődleges adatbázis mögött, a másodlagos adat garantáltan soha nem lesz részleges tranzakció. A régiók közötti redundancia lehetővé teszi az alkalmazások számára, hogy a természeti katasztrófák, a katasztrofális emberi hibák vagy a kártékony tevékenységek által okozott teljes adatközpontok vagy adatközpontok részeinek végleges elvesztésével gyorsan helyreállítsák az alkalmazásokat. Az adott RPO-információk az [üzletmenet folytonosságának áttekintése című témakörben](business-continuity-high-availability-disaster-recover-hadr-overview.md)találhatók.

> [!NOTE]
> Ha a két régió között hálózati hiba történik, a kapcsolatok újbóli létrehozásához 10 másodpercenként próbálkozunk.

> [!IMPORTANT]
> Annak biztosításához, hogy a feladatátvétel előtt a rendszer replikálja az elsődleges adatbázis kritikus módosításait, kényszerítse a szinkronizálást, hogy biztosítsa a kritikus módosítások (például a jelszavak frissítései) replikálását. A kényszerített szinkronizálás hatással van a teljesítményre, mert blokkolja a hívó szálat, amíg az összes véglegesített tranzakció replikálódik. Részletekért lásd: [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync). Az elsődleges adatbázis és a Geo-másodlagos közötti replikációs késés figyeléséhez lásd: [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database).

Az alábbi ábra egy olyan aktív geo-replikálási példát mutat be, amely az Egyesült Államok északi középső régiójában, az USA déli középső régiójában, a másodlagos régióban található.

![földrajzi replikálási kapcsolat](./media/active-geo-replication-overview/geo-replication-relationship.png)

Mivel a másodlagos adatbázisok olvashatók, használhatók csak olvasható munkaterhelések, például jelentési feladatok kiszervezéséhez. Ha aktív földrajzi replikálást használ, akkor a másodlagos adatbázist ugyanabban a régióban lehet létrehozni az elsődlegesnél, de az alkalmazás nem fokozza a katasztrofális hibák miatti rugalmasságot. Ha automatikus feladatátvételi csoportokat használ, a másodlagos adatbázist a rendszer mindig egy másik régióban hozza létre.

A vész-helyreállítási aktív földrajzi replikáción kívül a következő esetekben is használhatók:

- **Adatbázis-áttelepítés**: az aktív geo-replikálás használatával áttelepíthet egy adatbázist az egyik kiszolgálóról a másikra minimális állásidővel.
- **Alkalmazások frissítése**: az alkalmazások frissítése során további másodlagosként is létrehozhat egy feladat-visszavételi másolatot.

A valós Üzletmenet-folytonosság eléréséhez az adatközpontok közötti adatbázis-redundancia csak a megoldás része. Egy alkalmazás (szolgáltatás) teljes körű helyreállítása végzetes hiba után a szolgáltatást és a függő szolgáltatásokat alkotó összes összetevő helyreállítását igényli. Ezen összetevők közé tartoznak például az ügyfélszoftverek (például egy egyéni JavaScripttel rendelkező böngésző), a webes kezelőfelületek, a tárterület és a DNS. Kritikus fontosságú, hogy minden összetevő azonos hibákra legyen rugalmas, és az alkalmazás helyreállítási idő célkitűzésén (RTO) belül elérhetővé váljon. Ezért az összes függő szolgáltatást azonosítania kell, és ismernie kell az általuk nyújtott garanciákat és képességeket. Ezt követően meg kell tennie a megfelelő lépéseket annak biztosításához, hogy a szolgáltatás a szolgáltatások feladatátvétele alatt működjön, amelytől függ. A megoldásoknak a vész-helyreállításhoz való kialakításával kapcsolatos további információkért lásd: [felhőalapú megoldások tervezése a vész-helyreállításhoz az aktív földrajzi replikálás használatával](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="active-geo-replication-terminology-and-capabilities"></a>Aktív geo-replikációs terminológia és képességek

- **Automatikus aszinkron replikáció**

  Létrehozhat egy másodlagos adatbázist egy meglévő adatbázis hozzáadásával. A másodlagos létrehozható bármely kiszolgálón. A létrehozást követően a másodlagos adatbázis az elsődleges adatbázisból másolt adatokkal lesz feltöltve. Ez a folyamat más néven kivetés. A másodlagos adatbázis létrehozása és összevetése után a rendszer automatikusan replikálja az elsődleges adatbázis frissítéseit a másodlagos adatbázisba. Az aszinkron replikáció azt jelenti, hogy a tranzakciók véglegesítése az elsődleges adatbázison történik, mielőtt a rendszer replikálja őket a másodlagos adatbázisba.

- **Olvasható másodlagos adatbázisok**

  Egy alkalmazás az elsődleges adatbázishoz való hozzáféréshez használt azonos vagy eltérő rendszerbiztonsági tag használatával tud hozzáférni egy másodlagos adatbázishoz az írásvédett műveletekhez. A másodlagos adatbázisok pillanatkép-elkülönítési módban működnek, így biztosítva, hogy az elsődleges (log replay) frissítések replikálása ne legyen késleltetve a másodlagos lekérdezésekben.

> [!NOTE]
> A napló újrajátszása késleltetve van a másodlagos adatbázisban, ha az elsődlegesen vannak séma-frissítések. Az utóbbihoz a másodlagos adatbázis sémájának zárolása szükséges.

> [!IMPORTANT]
> A Geo-replikálás használatával létrehozhat egy másodlagos adatbázist ugyanabban a régióban, ahol az elsődleges. Ezt a másodlagost használhatja a csak olvasási feladatok terheléselosztásához ugyanabban a régióban. Az ugyanabban a régióban található másodlagos adatbázisok azonban nem biztosítanak további hibatűrési rugalmasságot, ezért nem megfelelő feladatátvételi cél a vész-helyreállításhoz. Emellett a rendelkezésre állási zónák elkülönítését sem fogja garantálni. A rendelkezésre állási zónák elkülönítésének eléréséhez használja az üzleti szempontból kritikus vagy prémium szintű szolgáltatási szintet a [zóna redundáns konfigurációjával](high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) vagy általános célú a szolgáltatási szint [zóna redundáns konfigurációját](high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) .
>

- **Tervezett feladatátvétel**

  A tervezett feladatátvétel az elsődleges és a másodlagos adatbázisok szerepköreit a teljes szinkronizálás befejeződése után váltja át. Ez egy online művelet, amely nem eredményez adatvesztést. A művelet időpontja a szinkronizálni kívánt elsődleges tranzakciós napló méretétől függ. A tervezett feladatátvételt a következő esetekben tervezték meg: (a) a DR működés közbeni működéséhez, ha az adatvesztés nem fogadható el; (b) az adatbázis másik régióba való áthelyezéséhez; és (c) az adatbázis visszaadása az elsődleges régióba a leállás csökkentése után (feladat-visszavétel).

- **Nem tervezett feladatátvétel**

  A nem tervezett vagy kényszerített feladatátvétel azonnal átváltja a másodlagost az elsődleges szerepkörre anélkül, hogy szinkronizálnia kellene az elsődlegesével. Az elsődleges, de a másodlagosra nem replikált tranzakciók elvesznek. A művelet a leállás során helyreállítási módszerként van kialakítva, ha az elsődleges nem érhető el, de az adatbázis rendelkezésre állását gyorsan vissza kell állítani. Amikor az eredeti elsődleges újra online állapotba kerül, automatikusan újrakapcsolódik, és új másodlagos lesz. A rendszer a feladatátvétel előtt minden nem szinkronizált tranzakciót megtart a biztonságimásolat-fájlban, de az ütközések elkerülése érdekében nem szinkronizálja az új elsődleges művelettel. Ezeket a tranzakciókat manuálisan kell egyesíteni az elsődleges adatbázis legújabb verziójával.

- **Több olvasható formátumú másodlagos zónák**

  Akár 4 másodlagos adatbázis is létrehozható az egyes elsődlegesekhez. Ha csak egy másodlagos adatbázis van, és az nem sikerül, az alkalmazás nagyobb kockázatnak van kitéve, amíg új másodlagos adatbázist nem hoznak létre. Ha több másodlagos adatbázis létezik, az alkalmazás akkor is védve marad, ha a másodlagos adatbázisok egyike meghibásodik. A további formátumú másodlagos zónák a csak olvasási feladatok felskálázására is felhasználhatók

  > [!NOTE]
  > Ha aktív geo-replikálást használ egy globálisan elosztott alkalmazás létrehozásához, és csak olvasási hozzáférést kell biztosítania a négynél több régióban található adatmennyiséghez, másodlagos másodlagos (azaz láncolási folyamat) létrehozását is létrehozhatja. Így gyakorlatilag korlátlan számú adatbázis-replikáció valósítható meg. Emellett a láncolás csökkenti az elsődleges adatbázis replikációjának terhelését is. A kikapcsolás a levelek és a legfelső szintű adatbázisok megnövekedett replikálási késése.

- **Adatbázisok földrajzi replikálása rugalmas készletben**

  Minden másodlagos adatbázis külön részt vehet egy rugalmas készletben, vagy egyáltalán nem lehet rugalmas készletben. Az egyes másodlagos adatbázisok készletének kiválasztása különálló, és nem függ más másodlagos adatbázisok konfigurációjától (elsődleges vagy másodlagos). Az egyes rugalmas készletek egyetlen régióban találhatók, ezért az azonos topológiában található több másodlagos adatbázis soha nem oszthat meg rugalmas készletet.

- **Felhasználó által vezérelt feladatátvétel és feladat-visszavétel**

  Egy másodlagos adatbázis az alkalmazás vagy a felhasználó által bármikor átváltható az elsődleges szerepkörre. Valós leállás esetén a "nem tervezett" beállítást kell használni, amely azonnal előlépteti a másodlagosat elsődlegesként. Ha a sikertelen elsődleges helyreállítások újra elérhetővé válnak, a rendszer automatikusan másodlagosként jelöli meg a helyreállított elsődlegest, és az új elsődleges értékkel állítja be azt. A replikáció aszinkron jellege miatt a nem tervezett feladatátvételek során a rendszer kis mennyiségű adatvesztést okozhat, ha az elsődleges meghibásodás előtt a legutóbbi módosítások replikálódnak a másodlagosra. Ha a több formátumú másodlagos zónák rendelkező elsődleges művelet feladatátvételt végez, a rendszer automatikusan újrakonfigurálja a replikálási kapcsolatokat, és a fennmaradó formátumú másodlagos zónák az újonnan előléptetett elsődleges értékre kapcsolja anélkül, hogy felhasználói beavatkozásra lenne szükség. A feladatátvételt okozó leállás után érdemes lehet az alkalmazást az elsődleges régióba visszaállítani. Ehhez a feladatátvételi parancsot a "tervezett" beállítással kell meghívni.

## <a name="preparing-secondary-database-for-failover"></a>Másodlagos adatbázis előkészítése feladatátvételhez

Annak biztosítása érdekében, hogy az alkalmazás azonnal hozzáférhessen az új elsődleges feladatátvételhez, ellenőrizze, hogy a másodlagos kiszolgáló és az adatbázis hitelesítési követelményei megfelelően vannak-e konfigurálva. Részletekért lásd: [SQL Database biztonság a katasztrófa utáni helyreállítás után](active-geo-replication-security-configure.md). A megfelelőség biztosítása érdekében a feladatátvételt követően ellenőrizze, hogy a másodlagos adatbázis biztonsági mentési adatmegőrzési szabályzata megegyezik-e az elsődleges értékkel. Ezek a beállítások nem részei az adatbázisnak, és nem replikálódnak. Alapértelmezés szerint a másodlagos beállítás a hét nap alapértelmezett PITR megőrzési időtartamával lesz konfigurálva. Részletekért lásd: [SQL Database automatikus biztonsági mentések](automated-backups-overview.md).

> [!IMPORTANT]
> Ha az adatbázis egy feladatátvételi csoport tagja, nem kezdeményezhet feladatátvételt a Geo-replikáció feladatátvételi paranccsal. Használja a feladatátvételi parancsot a csoport számára. Ha egy egyedi adatbázist kell feladatátvételt végeznie, először el kell távolítania azt a feladatátvételi csoportból. Részletekért lásd:  [feladatátvételi csoportok](auto-failover-group-overview.md) .

## <a name="configuring-secondary-database"></a>Másodlagos adatbázis konfigurálása

Mind az elsődleges, mind a másodlagos adatbázisnak azonos szolgáltatási szinten kell lennie. Azt is javasoljuk, hogy a másodlagos adatbázist ugyanazzal a biztonságimásolat-tárolási redundanciával és számítási mérettel (DTU vagy virtuális mag) hozza létre a rendszer elsődlegesként. Ha az elsődleges adatbázis nagy mennyiségű írási feladatot észlel, akkor előfordulhat, hogy az alacsonyabb számítási mérettel rendelkező másodlagos nem tud lépést tartani. Ennek hatására a rendszer visszakényszeríti a másodlagos, és lehetséges, hogy a másodlagos nem elérhető marad. A kockázatok enyhítése érdekében az aktív földrajzi replikálás az elsődleges tranzakciós naplók sebességét fogja szabályozni, ha szükséges, hogy a formátumú másodlagos zónák fellépjen.

Egy kiegyensúlyozatlan másodlagos konfiguráció egy másik következménye, hogy a feladatátvételt követően az alkalmazás teljesítménye az új elsődleges számítási kapacitás hiánya miatt csökkenhet. Ebben az esetben szükség lesz az adatbázis-szolgáltatás céljának a szükséges szintre való skálázására, amely jelentős időt és számítási erőforrásokat is igénybe vehet, és a vertikális Felskálázási folyamat végén [magas rendelkezésre állású](high-availability-sla.md) feladatátvételre lesz szükség.

Ha úgy dönt, hogy az alacsonyabb számítási mérettel hozza létre a másodlagost, a Azure Portal log IO százalékos diagramja jó módszert kínál a replikálási terhelés fenntartásához szükséges másodlagos minimális számítási méret becslésére. Ha például az elsődleges adatbázisa P6 (1000 DTU), a napló írási százaléka pedig 50%, a másodlagosnak legalább P4 (500 DTU) értékkel kell rendelkeznie. A korábbi log IO-adatlekérdezéshez használja a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nézetet. Ha a legutóbbi log Write-adatokat magasabb részletességgel szeretné lekérni, ami jobban tükrözi a rövid távú tüskéket a naplózási arányban, használja a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) nézetet.

A rendszer a tranzakciós napló sebességét az elsődlegesen a másodlagos számítási méret miatt a HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO WAIT típussal jelzi, amely a [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) adatbázis-nézetekben látható.

Alapértelmezés szerint a másodlagos biztonsági mentési tárterület-redundancia megegyezik az elsődleges adatbázissal. Dönthet úgy, hogy a másodlagost egy másik biztonságimásolat-tárolási redundancia segítségével konfigurálja. A biztonsági mentéseket mindig az elsődleges adatbázison kell elvégezni. Ha a másodlagos konfiguráció eltérő biztonsági mentési tárterülettel van konfigurálva, akkor a feladatátvételt követően a másodlagos előléptetés után a biztonsági mentések számlázása az új elsődleges (előző másodlagos) adattároló-redundancia alapján történik. 

> [!NOTE]
> A tranzakciós naplók aránya az elsődlegesnél szabályozható olyan okok miatt, amelyek nem kapcsolódnak alacsonyabb számítási mérethez a másodlagosnál. Ez a fajta szabályozás akkor is előfordulhat, ha a másodlagosnál az elsődlegesnél megegyező vagy magasabb számítási méret van. A részletekért, beleértve a különböző naplózási sebességek szabályozására vonatkozó várakozási típusokat, lásd: a [tranzakciós napló arányának szabályozása](resource-limits-logical-server.md#transaction-log-rate-governance).

> [!NOTE]
> Azure SQL Database konfigurálható biztonsági mentési tár redundancia jelenleg a Dél-Brazíliában elérhető nyilvános előzetes verzióban érhető el, és általánosan elérhető a Délkelet-ázsiai Azure-régióban. Ha a forrásadatbázis helyileg redundáns vagy Zone-redundáns biztonságimásolat-tárolási redundanciával jön létre, a másodlagos adatbázis más Azure-régióban való létrehozása nem támogatott. 

A SQL Database számítási méretekkel kapcsolatos további információkért lásd: [Mi a SQL Database szolgáltatási szintek](purchasing-models.md).

## <a name="cross-subscription-geo-replication"></a>Előfizetések közötti földrajzi replikáció

A különböző előfizetésekhez tartozó két adatbázis közötti aktív földrajzi replikálás beállításához (függetlenül attól, hogy ugyanazt a bérlőt vagy sem), az ebben a szakaszban ismertetett speciális eljárást kell követnie.  Az eljárás az SQL-parancsokon alapul, és a következőket igényli:

- Rendszerjogosultságú bejelentkezés létrehozása mindkét kiszolgálón
- Adja hozzá az IP-címet a mindkét kiszolgáló módosítását végző ügyfél engedélyezési listájához (például az SQL Server Management Studio-t futtató gazdagép IP-címéhez).

A módosításokat végző ügyfélnek hálózati hozzáférésre van szüksége az elsődleges kiszolgálóhoz. Bár az ügyfél ugyanazt az IP-címet kell hozzáadni a másodlagos kiszolgáló engedélyezési listájához, a másodlagos kiszolgálóhoz való hálózati kapcsolat nem feltétlenül szükséges.

### <a name="on-the-master-of-the-primary-server"></a>Az elsődleges kiszolgáló főkiszolgálójára

1. Adja hozzá az IP-címet a módosításokat végrehajtó ügyfél engedélyezési listájához (További információ: a [tűzfal konfigurálása](firewall-configure.md)).
1. Hozzon létre egy dedikált bejelentkezést az aktív geo-replikáció beállításához (és szükség szerint módosítsa a hitelesítő adatokat):

   ```sql
   create login geodrsetup with password = 'ComplexPassword01'
   ```

1. Hozzon létre egy megfelelő felhasználót, és rendelje hozzá a DBManager szerepkörhöz:

   ```sql
   create user geodrsetup for login geodrsetup
   alter role dbmanager add member geodrsetup
   ```

1. Jegyezze fel az új bejelentkezés SID-azonosítóját a következő lekérdezés használatával:

   ```sql
   select sid from sys.sql_logins where name = 'geodrsetup'
   ```

### <a name="on-the-source-database-on-the-primary-server"></a>Az elsődleges kiszolgáló forrás-adatbázisán

1. Hozzon létre egy felhasználót ugyanahhoz a bejelentkezéshez:

   ```sql
   create user geodrsetup for login geodrsetup
   ```

1. Adja hozzá a felhasználót a db_owner szerepkörhöz:

   ```sql
   alter role db_owner add member geodrsetup
   ```

### <a name="on-the-master-of-the-secondary-server"></a>A másodlagos kiszolgáló főkiszolgálójára

1. Adja hozzá az ügyfél IP-címét az engedélyezési listához a másodlagos kiszolgáló tűzfalszabályok alatt. Győződjön meg arról, hogy az elsődleges kiszolgálón hozzáadott azonos ügyfél IP-cím is hozzá lett adva a másodlagoshoz. Ez egy szükséges lépés ahhoz, hogy a MÓDOSÍTÁSi ADATBÁZIShoz hozzá kell adni a másodlagos parancsot a Geo-replikáció indításához.

1. Hozza létre ugyanazt a bejelentkezést, mint az elsődleges kiszolgálón, ugyanazzal a felhasználónévvel és SID-lel:

   ```sql
   create login geodrsetup with password = 'ComplexPassword01', sid=0x010600000000006400000000000000001C98F52B95D9C84BBBA8578FACE37C3E
   ```

1. Hozzon létre egy megfelelő felhasználót, és rendelje hozzá a DBManager szerepkörhöz:

   ```sql
   create user geodrsetup for login geodrsetup;
   alter role dbmanager add member geodrsetup
   ```

### <a name="on-the-master-of-the-primary-server"></a>Az elsődleges kiszolgáló főkiszolgálójára

1. Jelentkezzen be az elsődleges kiszolgáló főkiszolgálójára az új bejelentkezés használatával.
1. Hozzon létre egy másodlagos replikát a forrás-adatbázisról a másodlagos kiszolgálón (szükség szerint módosítsa az adatbázis nevét és a servername-t):

   ```sql
   alter database dbrep add secondary on server <servername>
   ```

A kezdeti beállítás után eltávolíthatók a felhasználók, a bejelentkezések és a tűzfalszabályok.

## <a name="keeping-credentials-and-firewall-rules-in-sync"></a>A hitelesítő adatok és a tűzfalszabályok szinkronizálásának megtartása

Javasoljuk, hogy az [adatbázis-szintű IP-tűzfalszabályok](firewall-configure.md) használatával geo-alapú adatbázisokat használjon, így ezek a szabályok replikálhatók az adatbázissal annak biztosítása érdekében, hogy az összes másodlagos adatbázis azonos IP-tűzfalszabályok legyenek az elsődlegesek. Ez a megközelítés szükségtelenné teszi az ügyfelek számára a tűzfalszabályok manuális konfigurálását és karbantartását az elsődleges és másodlagos adatbázisokat üzemeltető kiszolgálókon. Hasonlóképpen, a [tárolt adatbázis-felhasználók](logins-create-manage.md) adathozzáféréshez való használata biztosítja, hogy az elsődleges és a másodlagos adatbázisok mindig ugyanazzal a felhasználói hitelesítő adatokkal rendelkezzenek, ezért a feladatátvétel során a bejelentkezések és a jelszavak nem egyeznek. [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)hozzáadásával az ügyfelek az elsődleges és a másodlagos adatbázisokhoz is kezelhetik a felhasználói hozzáférést, így nem kell a hitelesítő adatoknak az adatbázisokban való kezelését.

## <a name="upgrading-or-downgrading-primary-database"></a>Elsődleges adatbázis frissítése vagy visszaminősítése

A másodlagos adatbázisok leválasztása nélkül frissítheti vagy visszaminősítheti az elsődleges adatbázist más számítási méretre (ugyanazon a szolgáltatási szinten belül, általános célú és üzletileg kritikus között). A frissítéskor javasoljuk, hogy először frissítse a másodlagos adatbázist, majd frissítse az elsődlegest. Ha alacsonyabb számítási méretre vált, fordított sorrendben végezze el a módosítást: először az elsődleges adatbázist, majd a másodlagos adatbázist módosítsa. Ha egy alacsonyabb vagy magasabb szolgáltatási szintre módosítja az adatbázist, a rendszer kikényszeríti ezt az ajánlást.

> [!NOTE]
> Ha a feladatátvételi csoport konfigurációjának részeként hozta létre a másodlagos adatbázist, nem ajánlott alacsonyabb szintre módosítani a másodlagos adatbázist. Ezzel biztosíthatja, hogy az adatmennyiség elegendő kapacitással legyen feldolgozva a rendszeres számítási feladatok elvégzése után a feladatátvétel aktiválása után.

> [!IMPORTANT]
> A feladatátvételi csoportban lévő elsődleges adatbázis nem skálázható magasabb szintűre, kivéve, ha először a másodlagos adatbázist skálázza magasabb szintre. Ha az elsődleges adatbázist a másodlagos adatbázis skálázása előtt próbálja meg méretezni, a következő hibaüzenet jelenhet meg:
>
> `Error message: The source database 'Primaryserver.DBName' cannot have higher edition than the target database 'Secondaryserver.DBName'. Upgrade the edition on the target before upgrading the source.`
>

## <a name="preventing-the-loss-of-critical-data"></a>A kritikus fontosságú adatmennyiség elvesztésének megakadályozása

A nagyméretű hálózatok nagy késése miatt a folyamatos másolás aszinkron replikációs mechanizmust használ. Az aszinkron replikáció során az adatvesztés elkerülhető, ha hiba történik. Előfordulhat azonban, hogy egyes alkalmazások nem igényelnek adatvesztést. A kritikus frissítések elleni védelem érdekében az alkalmazás fejlesztője a tranzakció véglegesítése után azonnal meghívhatja a [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) rendszereljárást. A hívás **sp_wait_for_database_copy_sync** blokkolja a hívó szálat, amíg az utolsó véglegesített tranzakció át nem lett továbbítva a másodlagos adatbázisba. Azonban nem várja meg, amíg a továbbított tranzakciók újra le lesznek játszva és véglegesítve lettek a másodlagoson. **sp_wait_for_database_copy_sync** hatóköre egy adott folyamatos másolási hivatkozásra vonatkozik. Minden olyan felhasználó, aki az elsődleges adatbázishoz kapcsolódási jogokkal rendelkezik, meghívhatja ezt az eljárást.

> [!NOTE]
> **sp_wait_for_database_copy_sync** megakadályozza az adatvesztést a feladatátvétel után, de nem garantálja a teljes szinkronizálást az olvasási hozzáféréshez. A **sp_wait_for_database_copy_synci** eljárás hívása által okozott késleltetés jelentős lehet, és a tranzakciós napló méretétől függ a hívás időpontjában.

## <a name="monitoring-geo-replication-lag"></a>Földrajzi replikációs késés figyelése

A RPO vonatkozó késés figyeléséhez használja az elsődleges adatbázis [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) *replication_lag_sec* oszlopát. Másodpercek alatt az elsődleges és a másodlagoson megőrzött tranzakciók közötti késést jeleníti meg. Például Ha a késés értéke 1 másodperc, akkor az azt jelenti, hogy ha az elsődlegest egy kimaradás okozta, és a feladatátvételt kezdeményezték, a legutóbbi átmenetek közül 1 másodperc nem lesz mentve.

Annak érdekében, hogy az elsődleges adatbázison végrehajtott, a másodlagosnál alkalmazott változásokkal kapcsolatos késés mértékét, azaz a másodlagosról való olvasásra elérhető legyen, hasonlítsa össze *last_commit* időt a másodlagos adatbázison ugyanazzal az értékkel az elsődleges adatbázison.

> [!NOTE]
> Az elsődleges adatbázis *replication_lag_sec* néha NULL értékkel rendelkezik, ami azt jelenti, hogy az elsődleges nem tudja, hogy meddig van a másodlagos.   Ez általában a folyamat újraindítása után történik, és átmeneti feltételnek kell lennie. Vegye fontolóra az alkalmazás riasztását, ha a *replication_lag_sec* hosszabb ideig null értéket ad vissza. Ez azt jelzi, hogy a másodlagos adatbázis nem tud kommunikálni az elsődlegesvel állandó csatlakozási hiba miatt. Olyan feltételek is fennállnak, amelyek a másodlagos és az elsődleges adatbázis *last_commit* ideje közötti különbséget okozhatják. Például Ha az elsődleges végrehajtás hosszú időn belül nem változik, a különbség egy nagy értékre ugrik, mielőtt a rendszer gyorsan visszatér 0-ra. Vegye figyelembe, hogy a hiba feltétele, ha a két érték közötti különbség hosszú ideig nagy marad.

## <a name="programmatically-managing-active-geo-replication"></a>Az aktív földrajzi replikáció programozott kezelése

Amint azt korábban már említettük, az aktív geo-replikáció programozott módon felügyelhető Azure PowerShell és a REST API használatával is. A következő táblázatok ismertetik az elérhető parancsok készletét. Az aktív geo-replikálás Azure Resource Manager API-kat tartalmaz a felügyelethez, beleértve a [Azure SQL Database REST API](/rest/api/sql/) és [Azure PowerShell parancsmagokat](/powershell/azure/). Ezek az API-k az erőforráscsoportok használatát igénylik, és támogatják az Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC). A hozzáférési szerepkörök megvalósításával kapcsolatos további információkért lásd: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="t-sql-manage-failover-of-single-and-pooled-databases"></a>T-SQL: az önálló és a készletezett adatbázisok feladatátvételének kezelése

> [!IMPORTANT]
> Ezek a Transact-SQL-parancsok csak az aktív földrajzi replikálásra vonatkoznak, és nem vonatkoznak a feladatátvételi csoportokra. Ennek megfelelően az SQL felügyelt példány példányaira sem vonatkoznak, mivel csak a feladatátvételi csoportokat támogatják.

| Parancs | Leírás |
| --- | --- |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Másodlagos adatbázis hozzáadása egy meglévő adatbázishoz, és az adatreplikálás elindításához használja a másodlagos kiszolgáló hozzáadása argumentumot. |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Feladatátvétel vagy FORCE_FAILOVER_ALLOW_DATA_LOSS használatával váltson át másodlagos adatbázist elsődlegesre a feladatátvétel indításához |
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-current) |Használja a másodlagos eltávolítása a kiszolgálón lehetőséget a SQL Database és a megadott másodlagos adatbázis közötti adatreplikáció megszakításához. |
| [sys.geo_replication_links](/sql/relational-databases/system-dynamic-management-views/sys-geo-replication-links-azure-sql-database) |A kiszolgálón található egyes adatbázisokhoz tartozó összes meglévő replikációs hivatkozás információit adja vissza. |
| [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) |Lekéri a legutóbbi replikálási időt, a legutóbbi replikálási késést és az adott adatbázis replikációs hivatkozásával kapcsolatos egyéb információkat. |
| [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) |Megjeleníti az összes adatbázis-művelet állapotát, beleértve a replikációs hivatkozások állapotát is. |
| [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) |azt eredményezi, hogy az alkalmazás megvárja az összes véglegesített tranzakció replikálását és az aktív másodlagos adatbázis által történő elfogadását. |
|  | |

### <a name="powershell-manage-failover-of-single-and-pooled-databases"></a>PowerShell: az önálló és a készletezett adatbázisok feladatátvételének kezelése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

| Parancsmag | Leírás |
| --- | --- |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Egy vagy több adatbázist kér le. |
| [Új – AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary) |Létrehoz egy másodlagos adatbázist egy meglévő adatbázishoz, és elkezdi az adatok replikálását. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary) |Egy másodlagos adatbázist elsődlegessé tesz, hogy feladatátvételt kezdeményezzen. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) |Leállítja az adatreplikációt egy SQL-adatbázis és a megadott másodlagos adatbázis között. |
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) |Lekéri a Geo-replikációs hivatkozásokat egy Azure SQL Database és egy erőforráscsoport vagy logikai SQL Server között. |
|  | |

> [!IMPORTANT]
> A példákat lásd: [önálló adatbázisok konfigurálása és feladatátvétele az aktív geo-replikációval](scripts/setup-geodr-and-failover-database-powershell.md) , valamint [a készletezett adatbázisok konfigurálása és feladatátvétele az aktív geo-replikáció használatával](scripts/setup-geodr-and-failover-elastic-pool-powershell.md).

### <a name="rest-api-manage-failover-of-single-and-pooled-databases"></a>REST API: önálló és készletezett adatbázisok feladatátvételének kezelése

| API | Leírás |
| --- | --- |
| [Adatbázis létrehozása vagy frissítése (createMode = Restore)](/rest/api/sql/databases/createorupdate) |Egy elsődleges vagy egy másodlagos adatbázis létrehozása, frissítése vagy visszaállítása. |
| [Adatbázis-létrehozási vagy-frissítési állapot beolvasása](/rest/api/sql/databases/createorupdate) |Az állapotot adja vissza egy létrehozási művelet során. |
| [Másodlagos adatbázis beállítása elsődlegesként (tervezett feladatátvétel)](/rest/api/sql/replicationlinks/failover) |Meghatározza, hogy melyik másodlagos adatbázis legyen elsődleges, ha az aktuális elsődleges adatbázisból feladatátvételt hajt végre. **Ez a beállítás nem támogatott az SQL felügyelt példányain.**|
| [Másodlagos adatbázis beállítása elsődlegesként (nem tervezett feladatátvétel)](/rest/api/sql/replicationlinks/failoverallowdataloss) |Meghatározza, hogy melyik másodlagos adatbázis legyen elsődleges, ha az aktuális elsődleges adatbázisból feladatátvételt hajt végre. A művelet adatvesztéshez vezethet. **Ez a beállítás nem támogatott az SQL felügyelt példányain.**|
| [Replikációs hivatkozás beolvasása](/rest/api/sql/replicationlinks/get) |Egy adott adatbázis adott replikációs hivatkozásának beolvasása egy földrajzi replikálási partnerségben. Lekéri az sys.geo_replication_links Catalog nézetben látható információkat. **Ez a beállítás nem támogatott az SQL felügyelt példányain.**|
| [Replikációs hivatkozások – lista adatbázis szerint](/rest/api/sql/replicationlinks/listbydatabase) | Az adott adatbázishoz tartozó összes replikációs hivatkozás beolvasása egy földrajzi replikálási partnerségben. Lekéri az sys.geo_replication_links Catalog nézetben látható információkat. |
| [Replikációs hivatkozás törlése](/rest/api/sql/replicationlinks/delete) | Töröl egy adatbázis-replikációs hivatkozást. Feladatátvétel közben nem végezhető el. |
|  | |




## <a name="next-steps"></a>Következő lépések

- A minta parancsfájlokat lásd:
  - [Önálló adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Rugalmas készletbe helyezett adatbázis konfigurálása és a feladatainak átvétele aktív georeplikációval](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
- A SQL Database támogatja az automatikus feladatátvételi csoportokat is. További információ: [automatikus feladatátvételi csoportok](auto-failover-group-overview.md)használata.
- Az üzletmenet folytonosságának áttekintése és forgatókönyvei: az [üzletmenet folytonosságának áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Az automatikus biztonsági mentések Azure SQL Databaseáról a [SQL Database automatizált biztonsági mentések](automated-backups-overview.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a helyreállítás automatizált biztonsági mentéséről, olvassa el [az adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatokból](recovery-using-backups.md)című témakört.
- Az új elsődleges kiszolgáló és adatbázis hitelesítési követelményeinek megismeréséhez tekintse meg a SQL Database biztonság a vész- [helyreállítás után](active-geo-replication-security-configure.md)című témakört.
