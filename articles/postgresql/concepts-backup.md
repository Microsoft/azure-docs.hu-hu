---
title: Biztonsági mentés és visszaállítás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerkedjen meg az automatikus biztonsági mentéssel és a Azure Database for PostgreSQL kiszolgáló – egyetlen kiszolgáló visszaállításával.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: db3b62e7ce07c1e10bc5030c37cb8957d281ea05
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517297"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---single-server"></a>Biztonsági mentés és visszaállítás Azure Database for PostgreSQL – egyetlen kiszolgáló

Azure Database for PostgreSQL automatikusan létrehozza a kiszolgáló biztonsági másolatait, és a helyileg redundáns vagy földrajzilag redundáns tárolóban tárolja azokat. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

## <a name="backups"></a>Biztonsági másolatok

Azure Database for PostgreSQL biztonsági másolatokat készít az adatfájlokról és a tranzakciónaplóról. A maximálisan támogatott tárolási mérettől függően teljes és különbözeti biztonsági mentéseket (4 TB-os maximális tárolási kiszolgálókat) vagy pillanatképes biztonsági mentést (legfeljebb 16 TB-os maximális tárolási kiszolgálókat) használhat. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is beállíthatja. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

Ezeket a biztonságimásolat-fájlokat nem lehet exportálni. A biztonsági másolatok csak Azure Database for PostgreSQL visszaállítási műveleteihez használhatók. Az adatbázis másolásához [pg_dump](howto-migrate-using-dump-and-restore.md) használható.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

#### <a name="servers-with-up-to-4-tb-storage"></a>Legfeljebb 4 TB tárhellyel rendelkező kiszolgálók

Legfeljebb 4 TB-os maximális tárterületet támogató kiszolgálók esetén a teljes biztonsági mentés hetente egyszer történik. A különbözeti biztonsági mentések naponta kétszer történnek. A tranzakciós naplók biztonsági mentése öt percenként történik.


#### <a name="servers-with-up-to-16-tb-storage"></a>Legfeljebb 16 TB tárhellyel rendelkező kiszolgálók

Az [Azure-régiók](./concepts-pricing-tiers.md#storage)egy részhalmazában az újonnan kiosztott kiszolgálók akár 16 TB-nyi tárhelyet is támogatnak. Ezen nagyméretű tároló kiszolgálókon a biztonsági másolatok pillanatkép-alapúak. Az első teljes pillanatkép biztonsági mentése a kiszolgáló létrehozása után azonnal ütemezve van. Az első teljes pillanatkép biztonsági mentése a kiszolgáló alapbiztonsági mentéseként marad. A pillanatképek későbbi biztonsági mentései csak különbségi biztonsági mentések lesznek. A különbségi biztonsági mentések nem meghatározott ütemezés szerint mennek végbe. Egy nap alatt három különbözeti pillanatkép biztonsági mentése történik. A tranzakciós naplók biztonsági mentése öt percenként történik. 

### <a name="backup-retention"></a>Biztonsági mentés megőrzése

A biztonsági mentések a kiszolgálón tárolt biztonsági másolatok megőrzési időszakának beállítása alapján őrződnek meg. 7 és 35 nap közötti megőrzési időtartamot választhat. Az alapértelmezett megőrzési időtartam 7 nap. A megőrzési időszakot a kiszolgáló létrehozásakor vagy később állíthatja be, ha a [Azure Portal](./howto-restore-server-portal.md#set-backup-configuration) vagy az [Azure CLI](./howto-restore-server-cli.md#set-backup-configuration)használatával frissíti a biztonsági mentési konfigurációt. 

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mert az elérhető biztonsági másolatokon alapul. A biztonsági mentés megőrzési időszaka helyreállítási perspektívában is kezelhető helyreállítási ablakként. A biztonsági másolatok megőrzési időszakán belül az időponthoz való visszaállításhoz szükséges összes biztonsági másolat megmarad a biztonságimásolat-tárolóban. Ha például a biztonsági másolat megőrzési időtartama 7 nap, a helyreállítási időszak az utolsó 7 nap lesz. Ebben a forgatókönyvben a kiszolgáló utolsó 7 napban történő visszaállításához szükséges összes biztonsági mentést megőrzi a rendszer. A biztonsági másolatok megőrzési ablaka hét nap:
- A legfeljebb 4 TB-os tárterülettel rendelkező kiszolgálók legfeljebb 2 teljes adatbázis-biztonsági mentést, az összes különbözeti biztonsági mentést és a tranzakciónapló biztonsági másolatait a legkorábbi teljes adatbázis biztonsági mentése óta hajtják végre.
-   A legfeljebb 16 TB tárhellyel rendelkező kiszolgálók megőrzik a teljes adatbázis-pillanatképet, a különbözeti pillanatképeket és a tranzakciónapló biztonsági mentését az elmúlt 8 napban.

### <a name="backup-redundancy-options"></a>A Backup redundancia beállításai

Azure Database for PostgreSQL rugalmasságot biztosít a helyileg redundáns vagy geo-redundáns biztonsági mentési tárolók közötti választáshoz a általános célú és a memóriára optimalizált rétegekben. Ha a biztonsági mentések a földrajzilag redundáns biztonsági mentési tárolóban tárolódnak, azok nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemeltetve van, de egy [párosított adatközpontba](../best-practices-availability-paired-regions.md)is replikálódnak. Ez jobb védelmet nyújt, és lehetővé teszi a kiszolgáló egy másik régióban való visszaállítását vészhelyzet esetén. Az alapszintű csomag csak a helyileg redundáns biztonsági mentési tárhelyet kínálja.

> [!IMPORTANT]
> A helyileg redundáns vagy georedundáns biztonsági mentési tárolás konfigurálása csak a kiszolgáló létrehozása közben engedélyezett. A biztonsági mentési tároló redundanciára vonatkozó beállításait a kiszolgáló üzembe helyezése után már nem lehet módosítani.

### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási díj

A Azure Database for PostgreSQL a kiépített kiszolgáló tárterületének akár 100%-át is elérhetővé teszi a biztonsági mentési tárolóként, többletköltség nélkül. Minden további felhasznált biztonsági mentési tárterületért GB/hó díjat számítunk fel. Ha például 250 GB tárterülettel rendelkező kiszolgálót épít ki, akkor a kiszolgáló biztonsági mentéséhez 250 GB-nyi további tárterület is rendelkezésre áll. A biztonsági mentéshez a 250 GB-nál nagyobb mennyiségű tárterületet a [díjszabási modell](https://azure.microsoft.com/pricing/details/postgresql/)szerint számítjuk fel.

A Azure Portalban elérhető Azure Monitor [biztonsági mentési tár](concepts-monitoring.md) használható metrikával figyelheti a kiszolgáló által felhasznált biztonsági mentési tárterületet. A biztonsági mentési tár használt mérőszáma a teljes adatbázis biztonsági mentése, a különbözeti biztonsági másolatok és a naplózott biztonsági mentések által felhasznált tárterület összegét adja meg a kiszolgáló biztonsági mentésének megőrzési időszaka alapján. A biztonsági mentések gyakorisága a szolgáltatás által felügyelt és korábban ismertetett. A kiszolgáló magas szintű tranzakciós tevékenysége miatt a biztonsági másolatok tárolási kihasználtsága a teljes adatbázis méretétől függetlenül növekedhet. A földrajzilag redundáns tároláshoz a biztonsági mentési tárterület a helyileg redundáns tárolásnál kétszer szerepel. 

A biztonsági mentési tárolási költségek szabályozásának elsődleges módja a biztonsági mentési megőrzési időtartam beállítása, valamint a megfelelő biztonsági mentési redundancia-beállítások kiválasztása a kívánt helyreállítási célok eléréséhez. A megőrzési időtartamot 7 és 35 nap közé is kiválaszthatja. A általános célú és a memóriára optimalizált kiszolgálók dönthetnek úgy, hogy a biztonsági mentések földrajzilag redundáns tárolóhelyet biztosítanak.

## <a name="restore"></a>Visszaállítás

Azure Database for PostgreSQL a Restore művelet elvégzésével létrehoz egy új kiszolgálót az eredeti kiszolgáló biztonsági másolatai közül. 

Két típusú visszaállítás érhető el:

- Az **időponthoz való visszaállítás** a Backup redundancia beállítással érhető el, és egy új kiszolgálót hoz létre ugyanabban a régióban, ahol az eredeti kiszolgáló található.
- A **geo-visszaállítás** csak akkor érhető el, ha a kiszolgálót a földrajzilag redundáns tároláshoz konfigurálta, és lehetővé teszi a kiszolgáló másik régióba való visszaállítását.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázisok méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az azonos régióban lévő adatbázisok teljes számától. A helyreállítási idő a legutóbbi adatbiztonsági mentéstől és a szükséges helyreállítás mennyiségétől függően változhat. Általában kevesebb, mint 12 óra.

> [!NOTE] 
> Ha a forrás PostgreSQL-kiszolgáló az ügyfél által felügyelt kulcsokkal van titkosítva, további szempontokért tekintse meg a [dokumentációt](concepts-data-encryption-postgresql.md) . 

> [!NOTE]
> Ha a törölt PostgreSQL-kiszolgálót szeretné visszaállítani, kövesse az [itt](howto-restore-dropped-server.md)dokumentált eljárást.

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A biztonsági mentési redundancia-beállítástól függetlenül a biztonsági másolatok megőrzési időszakán belül bármikor elvégezheti a visszaállítást. A rendszer létrehoz egy új kiszolgálót ugyanabban az Azure-régióban, mint az eredeti kiszolgálót. A rendszer az eredeti kiszolgáló konfigurációját hozza létre a díjszabási csomag, a számítási generáció, a virtuális mag száma, a tárterület mérete, a biztonsági másolatok megőrzési időtartama és a biztonsági mentési redundancia beállítás esetében.

Az időponthoz való visszaállítás több esetben is hasznos lehet. Ha például egy felhasználó véletlenül törli az adatvesztést, elveszít egy fontos táblát vagy adatbázist, vagy ha egy alkalmazás hibája miatt véletlenül rossz adatmennyiséggel felülírja a megfelelő adatmennyiséget.

Előfordulhat, hogy meg kell várnia a következő tranzakciónapló biztonsági mentését, mielőtt az utolsó öt percen belül helyre tudja állítani az adott időpontot.

Ha vissza szeretné állítani az eldobott táblát, 
1. A forráskiszolgáló visszaállítása időpontra vonatkozó módszer használatával.
2. A tábla kiírása a `pg_dump` visszaállított kiszolgálóról.
3. A forrásoldali tábla átnevezése az eredeti kiszolgálón.
4. Tábla importálása a psql parancssor használatával az eredeti kiszolgálón.
5. Szükség esetén törölheti a visszaállított kiszolgálót.

>[!Note]
> Azt javasoljuk, hogy egyszerre ne hozzon létre több visszaállítást ugyanahhoz a kiszolgálóhoz. 

### <a name="geo-restore"></a>Georedundáns visszaállítás

A kiszolgálót visszaállíthatja egy másik Azure-régióba, ahol a szolgáltatás elérhető, ha konfigurálta a kiszolgálót a Geo-redundáns biztonsági mentésekhez. A legfeljebb 4 TB tárterületet támogató kiszolgálók visszaállíthatók a Geo-párosítású régióba vagy bármely olyan régióba, amely akár 16 TB tárterületet is támogat. A legfeljebb 16 TB tárterületet támogató kiszolgálók esetében a Geo-biztonsági másolatok bármely olyan régióban visszaállíthatók, amely támogatja a 16 TB-os kiszolgálót is. Tekintse át a támogatott régiók listáját a [Azure Database for PostgreSQL díjszabási szintjein](concepts-pricing-tiers.md) .

A Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha a kiszolgáló nem érhető el, mert a kiszolgálót futtató régióban incidens található. Ha egy adott régióban a nagyméretű incidensek nem állnak rendelkezésre az adatbázis-alkalmazás számára, visszaállíthat egy kiszolgálót a Geo-redundáns biztonsági másolatokból egy másik régióban található kiszolgálóra. A biztonsági másolat készítése és más régióba való replikálása között késés történt. Ez a késleltetés akár egy óráig is eltarthat, így ha egy katasztrófa következik be, akár egy órányi adatvesztés is lehet.

A Geo-visszaállítás során a megváltoztatható kiszolgálói konfigurációk közé tartoznak a számítási generáció, a virtuális mag, a biztonsági másolatok megőrzési időtartama és a biztonsági mentési redundancia beállításai. Az árképzési szint (alapszintű, általános célú vagy memória optimalizált) módosítása vagy a tárterület mérete nem támogatott.

> [!NOTE]
> Ha a forráskiszolgáló infrastruktúra-kettős titkosítást használ, a kiszolgáló visszaállításához korlátozásokat is beleértve, például az elérhető régiókat. További részletekért tekintse meg az [infrastruktúra kettős titkosítását](concepts-infrastructure-double-encryption.md) ismertető témakört.

### <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusból való visszaállítás után a következő feladatokat kell elvégeznie a felhasználók és alkalmazások biztonsági mentésének és futtatásának visszaszerzéséhez:

- Ha az új kiszolgáló lecseréli az eredeti kiszolgálót, átirányítja az ügyfeleket és az ügyfélalkalmazások az új kiszolgálóra. Módosítsa a felhasználónevet is `username@new-restored-server-name` .
- Gondoskodjon arról, hogy a felhasználók csatlakozzanak a megfelelő kiszolgálói szintű tűzfal-és VNet-szabályokhoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
- Győződjön meg arról, hogy a megfelelő bejelentkezések és az adatbázis-szintű engedélyek vannak érvényben
- Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan lehet visszaállítani [a Azure Portal](howto-restore-server-portal.md)használatával.
- Ismerje meg, hogyan állíthatja vissza [Az Azure CLI](howto-restore-server-cli.md)használatával.
- Az üzletmenet folytonosságával kapcsolatos további tudnivalókért tekintse meg az [üzletmenet folytonosságának áttekintése](concepts-business-continuity.md)című témakört.