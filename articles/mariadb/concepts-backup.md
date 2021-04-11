---
title: Biztonsági mentés és visszaállítás – Azure Database for MariaDB
description: Ismerje meg a Azure Database for MariaDB-kiszolgáló automatikus biztonsági mentését és visszaállítását.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 8/13/2020
ms.openlocfilehash: b46efa53bba3b845fa5837b91a3707f4a85d298e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258775"
---
# <a name="backup-and-restore-in-azure-database-for-mariadb"></a>Biztonsági mentés és visszaállítás Azure Database for MariaDB

Azure Database for MariaDB automatikusan létrehozza a kiszolgáló biztonsági másolatait, és a helyileg redundáns vagy földrajzilag redundáns tárolóban tárolja azokat. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

## <a name="backups"></a>Biztonsági másolatok

Azure Database for MariaDB biztonsági másolatokat készít az adatfájlokról és a tranzakciónaplóról. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Opcionálisan akár 35 napig is [beállíthatja](howto-restore-server-portal.md#set-backup-configuration) . Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

Ezek a biztonságimentés-fájlok nem láthatók a felhasználók számára, és nem exportálhatók. Ezek a biztonsági másolatok csak Azure Database for MySQL-beli visszaállítási műveletekhez használhatók. A [mysqldump](howto-migrate-dump-restore.md) használatával másolhat egy adatbázist.

A biztonsági mentés típusa és gyakorisága a kiszolgálók háttér-tárolási helyétől függ.

### <a name="backup-type-and-frequency"></a>A biztonsági mentés típusa és gyakorisága

#### <a name="basic-storage-servers"></a>Alapszintű Storage-kiszolgálók

Az alapszintű tároló a háttérbeli tároló, amely az [alapszintű kiszolgálókat](concepts-pricing-tiers.md)támogatja. Az alapszintű Storage-kiszolgálók biztonsági mentései pillanatkép-alapúak. A teljes adatbázis-pillanatkép naponta történik. Az alapszintű Storage-kiszolgálók esetében nem végeznek különbözeti biztonsági mentést, és az összes pillanatkép biztonsági mentése csak az adatbázis teljes biztonsági másolata.

A tranzakciós naplók biztonsági mentése öt percenként történik.

#### <a name="general-purpose-storage-servers-with-up-to-4-tb-storage"></a>Általános célú Storage-kiszolgálók legfeljebb 4 TB-os tárhelygel

Az általános célú tárolás a háttérbeli tároló, amely a [általános célú](concepts-pricing-tiers.md) és a [memóriára optimalizált platform](concepts-pricing-tiers.md) -kiszolgálót támogatja. A 4 TB-os általános célú tárolóval rendelkező kiszolgálók esetében a teljes biztonsági mentés hetente egyszer történik. A különbözeti biztonsági mentések naponta kétszer történnek. A tranzakciós naplók biztonsági mentése öt percenként történik. Az általános célú, 4 TB-os tárterületre vonatkozó biztonsági másolatok nem pillanatkép-alapúak, és az i/o-sávszélességet használják a biztonsági mentés időpontjában. A 4 TB-os tárolón található nagyméretű adatbázisok (> 1 TB) esetében javasoljuk, hogy vegye figyelembe a következőt:

- További IOPs kiépítés a Backup IOs vagy a
- Azt is megteheti, hogy olyan általános célú tárhelyre telepít át, amely akár 16 TB-nyi tárhelyet is támogat, ha a mögöttes tároló-infrastruktúra elérhető az Ön által preferált [Azure](./concepts-pricing-tiers.md#storage)- A legfeljebb 16 TB-nyi tárterületet támogató általános célú tárterületre nem vonatkozik további díj. A 16 TB-os tárhelyre való áttelepítéssel kapcsolatos segítségért nyisson meg egy támogatási jegyet Azure Portalról.

#### <a name="general-purpose-storage-servers-with-up-to-16-tb-storage"></a>Általános célú Storage-kiszolgálók legfeljebb 16 TB tárhellyel

Az [Azure-régiók](./concepts-pricing-tiers.md#storage)egy részhalmazában az újonnan kiosztott kiszolgálók az általános célú tárolást akár 16 TB tárhellyel is támogathatják. Ez azt jelenti, hogy a tárterület akár 16 TB tárhellyel is az alapértelmezett általános célú tárterület az összes olyan [régió](concepts-pricing-tiers.md#storage) esetében, ahol ez támogatott. A 16 TB-os tárolási kiszolgálókon a biztonsági másolatok pillanatkép-alapúak. Az első teljes pillanatkép biztonsági mentése a kiszolgáló létrehozása után azonnal ütemezve van. Az első teljes pillanatkép biztonsági mentése a kiszolgáló alapbiztonsági mentéseként marad. A pillanatképek későbbi biztonsági mentései csak különbségi biztonsági mentések lesznek.

A különbségi biztonsági mentések legalább naponta egyszer végbemennek. A különbségi biztonsági mentések nem meghatározott ütemezés szerint mennek végbe. A különbözeti Pillanatképek biztonsági mentései 24 óránként történnek, kivéve, ha a tranzakciós napló (a MariaDB BinLog) meghaladja a 50 GB-ot az utolsó különbözeti biztonsági mentés óta. Egy adott napon legfeljebb hat különbségi pillanatkép készítése engedélyezett.

A tranzakciós naplók biztonsági mentése öt percenként történik.
 

### <a name="backup-retention"></a>Biztonsági mentés megőrzése

A biztonsági mentések a kiszolgálón tárolt biztonsági másolatok megőrzési időszakának beállítása alapján őrződnek meg. 7 és 35 nap közötti megőrzési időtartamot választhat. Az alapértelmezett megőrzési időtartam 7 nap. A megőrzési időszakot a kiszolgáló létrehozásakor vagy később állíthatja be, ha a [Azure Portal](howto-restore-server-portal.md#set-backup-configuration) vagy az [Azure CLI](howto-restore-server-cli.md#set-backup-configuration)használatával frissíti a biztonsági mentési konfigurációt. 

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mert az elérhető biztonsági másolatokon alapul. A biztonsági mentés megőrzési időszaka helyreállítási perspektívában is kezelhető helyreállítási ablakként. A biztonsági másolatok megőrzési időszakán belül az időponthoz való visszaállításhoz szükséges összes biztonsági másolat megmarad a biztonságimásolat-tárolóban. Ha például a biztonsági másolat megőrzési időtartama 7 nap, a helyreállítási időszak az utolsó 7 nap lesz. Ebben a forgatókönyvben a kiszolgáló utolsó 7 napban történő visszaállításához szükséges összes biztonsági mentést megőrzi a rendszer. A biztonsági másolatok megőrzési ablaka hét nap:

- A legfeljebb 4 TB-os tárterülettel rendelkező kiszolgálók legfeljebb 2 teljes adatbázis-biztonsági mentést, az összes különbözeti biztonsági mentést és a tranzakciónapló biztonsági másolatait a legkorábbi teljes adatbázis biztonsági mentése óta hajtják végre.
-   A legfeljebb 16 TB tárhellyel rendelkező kiszolgálók megőrzik a teljes adatbázis-pillanatképet, a különbözeti pillanatképeket és a tranzakciónapló biztonsági mentését az elmúlt 8 napban.

#### <a name="long-term-retention-of-backups"></a>Biztonsági másolatok hosszú távú megőrzése
A biztonsági másolatok hosszú távú megőrzése a 35 napnál hosszabb ideig még nem támogatott a szolgáltatás általi natív módon. Lehetősége van arra, hogy a mysqldump használatával készítsen biztonsági másolatokat, és tárolja őket a hosszú távú megőrzés érdekében. A támogatási csapatunk [részletesen](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/automate-backups-of-your-azure-database-for-mysql-server-to/ba-p/1791157) ismerteti, hogy miként lehet elérni. 

### <a name="backup-redundancy-options"></a>A Backup redundancia beállításai

Azure Database for MariaDB rugalmasságot biztosít a helyileg redundáns vagy geo-redundáns biztonsági mentési tárolók közötti választáshoz a általános célú és a memóriára optimalizált rétegekben. Ha a biztonsági mentések a földrajzilag redundáns biztonsági mentési tárolóban tárolódnak, azok nem csak abban a régióban vannak tárolva, amelyben a kiszolgáló üzemeltetve van, de egy [párosított adatközpontba](../best-practices-availability-paired-regions.md)is replikálódnak. Ez jobb védelmet nyújt, és lehetővé teszi a kiszolgáló egy másik régióban való visszaállítását vészhelyzet esetén. Az alapszintű csomag csak a helyileg redundáns biztonsági mentési tárhelyet kínálja.

#### <a name="moving-from-locally-redundant-to-geo-redundant-backup-storage"></a>Áthelyezés helyileg redundáns biztonsági mentési tárolóba
A helyileg redundáns vagy georedundáns biztonsági mentési tárolás konfigurálása csak a kiszolgáló létrehozása közben engedélyezett. A biztonsági mentési tároló redundanciára vonatkozó beállításait a kiszolgáló üzembe helyezése után már nem lehet módosítani. Ahhoz, hogy a biztonsági mentési tárhelyet a helyileg redundáns tárolóból a Geo-redundáns tárolóba helyezze át, egy új kiszolgáló létrehozása és az adatok a [dump és a Restore](howto-migrate-dump-restore.md) használatával történő áttelepítése az egyetlen támogatott lehetőség.

### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási díj

A Azure Database for MariaDB a kiépített kiszolgáló tárterületének akár 100%-át is elérhetővé teszi a biztonsági mentési tárolóként, többletköltség nélkül. Minden további felhasznált biztonsági mentési tárterületért GB/hó díjat számítunk fel. Ha például 250 GB tárterülettel rendelkező kiszolgálót épít ki, akkor a kiszolgáló biztonsági mentéséhez 250 GB-nyi további tárterület is rendelkezésre áll. A biztonsági mentéshez a 250 GB-nál nagyobb mennyiségű tárterületet a [díjszabási modell](https://azure.microsoft.com/pricing/details/mariadb/)szerint számítjuk fel. 

A Azure Monitor által elérhető [biztonsági mentési tár](concepts-monitoring.md) a Azure Portal segítségével a kiszolgáló által felhasznált biztonsági mentési tárterület figyelésére használható. A biztonsági mentési tár használt mérőszáma a teljes adatbázis biztonsági mentése, a különbözeti biztonsági másolatok és a naplózott biztonsági mentések által felhasznált tárterület összegét adja meg a kiszolgáló biztonsági mentésének megőrzési időszaka alapján. A biztonsági mentések gyakorisága a szolgáltatás által felügyelt és korábban ismertetett. A kiszolgáló magas szintű tranzakciós tevékenysége miatt a biztonsági másolatok tárolási kihasználtsága a teljes adatbázis méretétől függetlenül növekedhet. A földrajzilag redundáns tároláshoz a biztonsági mentési tárterület a helyileg redundáns tárolásnál kétszer szerepel. 

A biztonsági mentési tárolási költségek szabályozásának elsődleges módja a biztonsági mentési megőrzési időtartam beállítása, valamint a megfelelő biztonsági mentési redundancia-beállítások kiválasztása a kívánt helyreállítási célok eléréséhez. A megőrzési időtartamot 7 és 35 nap közé is kiválaszthatja. A általános célú és a memóriára optimalizált kiszolgálók dönthetnek úgy, hogy a biztonsági mentések földrajzilag redundáns tárolóhelyet biztosítanak.

## <a name="restore"></a>Visszaállítás

Azure Database for MariaDB a Restore művelet elvégzésével létrehoz egy új kiszolgálót az eredeti kiszolgáló biztonsági másolatai közül, és visszaállítja a kiszolgálón található összes adatbázist.

Két típusú visszaállítás érhető el:

- Az **időponthoz való visszaállítás** a Backup redundancia beállítással érhető el, és egy új kiszolgálót hoz létre ugyanabban a régióban, mint az eredeti kiszolgáló, amely a teljes és a tranzakciónapló biztonsági mentésének kombinációját használja.
- A **geo-visszaállítás** csak akkor érhető el, ha a kiszolgálót a Geo-redundáns tároláshoz konfigurálta, és lehetővé teszi a kiszolgáló egy másik régióba való visszaállítását, amely a legutóbbi biztonsági mentést használja.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázisok méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az azonos régióban lévő adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. A kiszolgálói erőforrások, a telepítés után a véletlen törlés vagy a váratlan módosítások elleni védelem érdekében a rendszergazdák kihasználhatják a [felügyeleti zárolásokat](../azure-resource-manager/management/lock-resources.md).

### <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

A biztonsági mentési redundancia-beállítástól függetlenül a biztonsági másolatok megőrzési időszakán belül bármikor elvégezheti a visszaállítást. A rendszer létrehoz egy új kiszolgálót ugyanabban az Azure-régióban, mint az eredeti kiszolgálót. A rendszer az eredeti kiszolgáló konfigurációját hozza létre a díjszabási csomag, a számítási generáció, a virtuális mag száma, a tárterület mérete, a biztonsági másolatok megőrzési időtartama és a biztonsági mentési redundancia beállítás esetében.

Az időponthoz való visszaállítás több esetben is hasznos lehet. Ha például egy felhasználó véletlenül törli az adatvesztést, elveszít egy fontos táblát vagy adatbázist, vagy ha egy alkalmazás hibája miatt véletlenül rossz adatmennyiséggel felülírja a megfelelő adatmennyiséget.

Előfordulhat, hogy meg kell várnia a következő tranzakciónapló biztonsági mentését, mielőtt az utolsó öt percen belül helyre tudja állítani az adott időpontot.

### <a name="geo-restore"></a>Georedundáns visszaállítás

A kiszolgálót visszaállíthatja egy másik Azure-régióba, ahol a szolgáltatás elérhető, ha konfigurálta a kiszolgálót a Geo-redundáns biztonsági mentésekhez. A legfeljebb 4 TB tárterületet támogató kiszolgálók visszaállíthatók a Geo-párosítású régióba vagy bármely olyan régióba, amely akár 16 TB tárterületet is támogat. A legfeljebb 16 TB tárterületet támogató kiszolgálók esetében a Geo-biztonsági másolatok bármely olyan régióban visszaállíthatók, amely támogatja a 16 TB-os kiszolgálót is. Tekintse át a támogatott régiók listáját a [Azure Database for MariaDB díjszabási szintjein](concepts-pricing-tiers.md) .

A Geo-visszaállítás az alapértelmezett helyreállítási lehetőség, ha a kiszolgáló nem érhető el, mert a kiszolgálót futtató régióban incidens található. Ha egy adott régióban a nagyméretű incidensek nem állnak rendelkezésre az adatbázis-alkalmazás számára, visszaállíthat egy kiszolgálót a Geo-redundáns biztonsági másolatokból egy másik régióban található kiszolgálóra. A Geo-visszaállítás a kiszolgáló legújabb biztonsági mentését használja. A biztonsági másolat készítése és más régióba való replikálása között késés történt. Ez a késleltetés akár egy óráig is eltarthat, így ha egy katasztrófa következik be, akár egy órányi adatvesztés is lehet.

> [!IMPORTANT]
>Ha egy újonnan létrehozott kiszolgáló geo-visszaállítást végez, a kezdeti biztonsági mentési szinkronizálás az adatmérettől függően több mint 24 órát is igénybe vehet, a kezdeti teljes pillanatkép-másolási idő sokkal magasabb. A pillanatképek későbbi biztonsági mentései növekményes másolást végeznek, ezért a visszaállítások a kiszolgáló 24 órányi létrehozása után gyorsabbak lesznek. Ha a Geo-visszaállítások kiértékelésével határozza meg a RTO, javasoljuk, hogy a jobb becslések érdekében **csak 24 órányi** kiszolgáló létrehozása után várjon és értékelje ki a Geo-visszaállítást.

A Geo-visszaállítás során a megváltoztatható kiszolgálói konfigurációk közé tartoznak a számítási generáció, a virtuális mag, a biztonsági másolatok megőrzési időtartama és a biztonsági mentési redundancia beállításai. Az árképzési szint (alapszintű, általános célú vagy memória optimalizálása) vagy a tárolási méret módosítása a Geo-visszaállítás során nem támogatott.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázisok méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az azonos régióban lévő adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

### <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása

A helyreállítási mechanizmusból való visszaállítás után a következő feladatokat kell elvégeznie a felhasználók és alkalmazások biztonsági mentésének és futtatásának visszaszerzéséhez:

- Ha az új kiszolgáló lecseréli az eredeti kiszolgálót, átirányítja az ügyfeleket és az ügyfélalkalmazások az új kiszolgálóra
- Győződjön meg arról, hogy a felhasználók csatlakozhatnak a megfelelő VNet-szabályokhoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
- Győződjön meg arról, hogy a megfelelő bejelentkezések és az adatbázis-szintű engedélyek vannak érvényben
- Konfigurálja a riasztásokat, ha szükséges.

## <a name="next-steps"></a>Következő lépések

- Az üzletmenet folytonosságával kapcsolatos további tudnivalókért tekintse meg az [üzletmenet folytonosságának áttekintése](concepts-business-continuity.md)című témakört.
- Ha a Azure Portal használatával szeretne visszaállítani egy időpontra, tekintse meg [a kiszolgáló visszaállítása a Azure Portal használatával](howto-restore-server-portal.md)című témakört.
- Az Azure CLI-vel való időpontra való visszaállításhoz lásd: [kiszolgáló visszaállítása egy adott időpontra a CLI használatával](howto-restore-server-cli.md).
