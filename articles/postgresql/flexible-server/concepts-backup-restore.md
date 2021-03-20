---
title: Biztonsági mentés és visszaállítás Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Tudnivalók a biztonsági mentés és a visszaállítás fogalmáról Azure Database for PostgreSQL rugalmas kiszolgálóval
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: d0e79e42c7c004638336ada23de663bbe74b7e48
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92532645"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Biztonsági mentés és visszaállítás Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

A biztonsági mentések az üzletmenet folytonossági stratégiájának lényeges részét képezik. Segítenek az adatok véletlen sérülés vagy Törlés elleni védelmében. Azure Database for PostgreSQL – a rugalmas kiszolgáló automatikusan biztonsági másolatot készít a kiszolgálóról, és legfeljebb 35 napig őrzi meg a biztonsági mentéseket. A visszaállítás során megadhatja azt a dátumot és időpontot, ameddig a megőrzési időn belül vissza kívánja állítani a visszaállítást. A visszaállítás és helyreállítás teljes ideje az adatbázisfájlok méretétől és a végrehajtandó helyreállítás mennyiségétől függ. 

### <a name="backup-process-in-flexible-server"></a>Biztonsági mentési folyamat rugalmas kiszolgálón
Az első pillanatkép biztonsági mentése a rugalmas kiszolgáló létrehozása után azonnal ütemezve van. Ezt követően a rendszer elvégzi az adatfájlok napi biztonsági mentését. A biztonsági mentéseket egy adott régióban található redundáns tároló tárolja. A tranzakciónaplók (az előre írható naplók – WAL) is folyamatosan archiválva lesznek, így az utolsó véglegesített tranzakcióra is visszaállíthatók. Ezeknek az adatoknak és naplóknak a biztonsági mentése lehetővé teszi a kiszolgálók visszaállítását bármely időpontra a konfigurált megőrzési időszakban. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva.

Ha az adatbázis magas rendelkezésre állással van konfigurálva, a rendszer a napi pillanatképeket az elsődlegesből hajtja végre, és a folyamatos biztonsági másolatokat készenléti állapotból hajtja végre.

> [!IMPORTANT]
>A leállított kiszolgálókon nem végeznek biztonsági mentést. A biztonsági mentések azonban akkor is folytatódnak, ha az adatbázist 7 nap vagy a felhasználó indítja el.

A biztonsági másolatok csak a rugalmas kiszolgálón belüli visszaállítási műveletekhez használhatók. Ha szeretné exportálni vagy importálni az adatfájlokat a rugalmas kiszolgálóra, használja a [dump és a Restore](../howto-migrate-using-dump-and-restore.md) módszert.


### <a name="backup-retention"></a>Biztonsági mentés megőrzése

A biztonsági mentések a kiszolgáló biztonsági mentés megőrzési időtartamának beállítása alapján őrződnek meg. 7 és 35 nap közötti megőrzési időtartamot választhat. Az alapértelmezett megőrzési időtartam hét nap. Megadhatja a megőrzési időszakot a kiszolgáló létrehozásakor, vagy később is frissítheti. A biztonsági mentések még a leállított kiszolgálók esetében is megmaradnak.

A biztonsági másolatok megőrzési időszaka azt szabályozza, hogy az adott időpontra visszamenőleges visszaállítás hogyan kérhető le, mivel az az \' elérhető biztonsági másolatokon alapul. A biztonsági mentés megőrzési időszaka helyreállítási perspektívában is kezelhető helyreállítási ablakként. A biztonsági másolat megőrzési idejének időponthoz való visszaállításához szükséges biztonsági másolatok megmaradnak a biztonsági mentési tárolóban. Ha például a biztonsági másolat megőrzési időtartama hét nap, a helyreállítási időszak az utolsó hét nap lesz. Ebben az esetben az utolsó hét napban a kiszolgáló helyreállításához és helyreállításához szükséges összes adat és napló megmarad. 


### <a name="backup-storage-cost"></a>Biztonsági mentési tárolási díj

A rugalmas kiszolgáló a kiépített kiszolgáló tárterületének akár 100%-át is elérhetővé teszi a biztonsági mentési tárolóként, többletköltség nélkül. Minden további felhasznált biztonsági mentési tárterületért GB/hó díjat számítunk fel. Ha például létrehozta a 250 GiB tárterülettel rendelkező kiszolgálót, akkor a biztonsági mentési tárterület 250 GiB-t külön díj nélkül kiépítheti. Ha a napi biztonsági mentés 25 GiB, akkor akár 10 napos ingyenes biztonsági mentési tárterületet is használhat. A biztonsági mentési tárterület 250 GiB-nál nagyobb mennyiségét a [díjszabási modellnek](https://azure.microsoft.com/pricing/details/postgresql/)megfelelően számítjuk fel.

A kiszolgáló által felhasznált biztonsági mentési tárterület monitorozásához használhatja az Azure Portal [Használt biztonsági mentési tárhely](../concepts-monitoring.md) metrikáját. A Használt biztonsági mentési tárhely metrika az adatbázisok biztonsági mentései és a naplók biztonsági mentései által felhasznált összes tárterület összegét adja meg a biztonsági mentések kiszolgálóhoz beállított megőrzési időszaka alapján.  A kiszolgáló magas szintű tranzakciós tevékenysége miatt a biztonsági másolatok tárolási kihasználtsága a teljes adatbázis méretétől függetlenül növekedhet.

A biztonsági mentési tárolási költségek szabályozásának elsődleges módja a biztonsági mentési megőrzési időtartam beállítása, valamint a megfelelő biztonsági mentési redundancia-beállítások kiválasztása a kívánt helyreállítási célok eléréséhez.

> [!IMPORTANT]
> A rugalmas kiszolgálók jelenleg nem támogatják a Geo-redundáns biztonsági mentéseket.

## <a name="point-in-time-restore-overview"></a>Időponthoz való visszaállítás áttekintése

A rugalmas kiszolgálókon az adott időponthoz tartozó visszaállítás egy új kiszolgálót hoz létre a rugalmas kiszolgálói \' biztonsági mentésből, amely ugyanabban a régióban található, mint a forráskiszolgáló. A rendszer a forráskiszolgáló konfigurációját hozza létre a díjszabási csomag, a számítási generáció, a virtuális mag száma, a tárterület mérete, a biztonsági másolatok megőrzési időszaka és a biztonsági mentési redundancia beállítás tekintetében. Emellett az olyan címkék és beállítások is, mint a VNET és a tűzfal beállításai, a forráskiszolgálóról öröklődnek. 

 > [!IMPORTANT]
> Ha olyan rugalmas kiszolgálót állít vissza, amely a zóna redundánsan magas rendelkezésre állását állítja be, a visszaállított kiszolgáló magas rendelkezésre állás nélkül lesz konfigurálva, és az elsődleges kiszolgálóval megegyező régióban. 

 ### <a name="restore-process"></a>Visszaállítási folyamat

A rendszer először visszaállítja a fizikai adatbázis fájljait a pillanatkép biztonsági másolatokból a kiszolgáló adathelyébe. A rendszer automatikusan kiválasztja és visszaállítja a megfelelő biztonsági mentést, amely a kívánt időpontnál korábban lett elvégezve. Ezt követően a rendszer a WAL-fájlok használatával kezdeményezi a helyreállítási folyamatot, hogy az adatbázis konzisztens állapotba kerüljön. 

 Tegyük fel például, hogy a biztonsági mentések a nap minden napján 23:00 órakor történnek. Ha a visszaállítási pont a 2020-es augusztus 15-én, 10:00 órakor, a rendszer visszaállítja a (z) 2020 augusztus 14-i napi biztonsági mentését. Az adatbázis 2020. augusztus 25-én kerül helyreállításra, a tranzakciós naplók pedig 23:00. augusztus 24. és 10. között. 

 A következő [lépésekkel](./how-to-restore-server-portal.md) állíthatja vissza az adatbázis-kiszolgálót.

> [!IMPORTANT]
> A rugalmas kiszolgáló visszaállítási műveletei egy új adatbázis-kiszolgálót hoznak létre, és nem írják felül a meglévő adatbázis-kiszolgálót.

Az időponthoz való visszaállítás több esetben is hasznos lehet. Ha például egy felhasználó véletlenül törli az adatvesztést, elveszít egy fontos táblát vagy adatbázist, vagy ha egy alkalmazás hibája miatt véletlenül rossz adatmennyiséggel felülírja a megfelelő adatmennyiséget. A tranzakciós naplók folyamatos biztonsági mentése miatt a rendszer visszaállíthatja a legutóbbi tranzakciót.

A legkorábbi visszaállítási pont és az egyéni visszaállítási pont közül választhat.

-   **Legkorábbi visszaállítási pont**: a megőrzési időszaktól függően ez lesz a legkorábbi időpont, ameddig vissza lehet állítani. A legrégebbi biztonsági mentési idő automatikusan ki lesz választva, és megjelenik a portálon. Ez akkor lehet hasznos, ha meg szeretné vizsgálni, vagy végre kell hajtania egy tesztelési időszakot.

-   **Egyéni visszaállítási pont**: Ez a beállítás lehetővé teszi, hogy bármilyen időpontot válasszon a rugalmas kiszolgáló számára meghatározott megőrzési időtartamon belül. Alapértelmezés szerint az UTC legkésőbbi időpontja automatikusan be van jelölve, és hasznos, ha vissza szeretné állítani az utolsó véglegesített tranzakciót a tesztelési célokra. Igény szerint más napokat és időpontokat is választhat. 

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázis méretétől, a feldolgozandó tranzakciós naplók mennyiségétől, a hálózati sávszélességtől és az azonos régióban található adatbázisok teljes számától. A teljes helyreállítási idő általában néhány perctől akár néhány óráig is eltarthat.


> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. A kiszolgálói erőforrások, a telepítés után a véletlen törlés vagy a váratlan módosítások elleni védelem érdekében a rendszergazdák kihasználhatják a [felügyeleti zárolásokat](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása

Az adatbázis visszaállítása után a következő feladatokat végezheti el a felhasználók és az alkalmazások biztonsági mentésének és futtatásának visszaszerzéséhez:

-   Ha az új kiszolgáló lecseréli az eredeti kiszolgálót, átirányítja az ügyfeleket és az ügyfélalkalmazások az új kiszolgálóra.

-   Gondoskodjon arról, hogy a felhasználók csatlakozzanak a megfelelő kiszolgálói szintű tűzfal-és VNet-szabályokhoz. Ezeket a szabályokat a rendszer nem másolja át az eredeti kiszolgálóról.
  
-   A visszaállított kiszolgáló számítási felskálázása igény szerint méretezhető.

-   Győződjön meg arról, hogy a megfelelő bejelentkezések és az adatbázis-szintű engedélyek vannak érvényben.

-   Szükség szerint konfigurálja a riasztásokat.
  
-  Ha a magas rendelkezésre állással konfigurálta az adatbázist, és ha a visszaállított kiszolgálót magas rendelkezésre állással szeretné konfigurálni, kövesse [a lépéseket](./how-to-manage-high-availability-portal.md).


## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
-   Útmutató [a visszaállításhoz](./how-to-restore-server-portal.md)