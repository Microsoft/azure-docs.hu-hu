---
title: Biztonsági mentés és visszaállítás Azure Database for MySQL rugalmas kiszolgálón
description: Tudnivalók a Azure Database for MySQL rugalmas kiszolgálóval végzett biztonsági mentési és visszaállítási fogalmakról
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 2d69427f9f11a47cedeccb4b1da38b770952f029
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240766"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Biztonsági mentés és visszaállítás Azure Database for MySQL rugalmas kiszolgálóban (előzetes verzió)

> [!IMPORTANT] 
> Azure Database for MySQL – a rugalmas kiszolgáló jelenleg nyilvános előzetes verzióban érhető el.

Azure Database for MySQL rugalmas kiszolgáló, a automatikusan létrehozza a kiszolgáló biztonsági másolatait, és biztonságosan tárolja azokat a helyi redundáns tárolóban a régión belül. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és helyreállítás minden üzletmenet-folytonossági stratégia elengedhetetlen része, hiszen ez védi meg az adatokat a véletlen sérülésektől és törléstől.

## <a name="backup-overview"></a>A biztonsági mentés áttekintése

A rugalmas kiszolgáló pillanatképes biztonsági másolatot készít az adatfájlokról, és egy helyi redundáns tárolóban tárolja őket. A kiszolgáló a tranzakciós naplók biztonsági mentését is végrehajtja, és a helyi redundáns tárolóban tárolja őket. Ezek a biztonsági másolatok lehetővé teszik a kiszolgálók visszaállítását bármely időpontra a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Az adatbázis biztonsági mentését opcionálisan 1 – 35 napig is beállíthatja. Az összes biztonsági mentés titkosítása AES 256 bites titkosítással történik a nyugalmi állapotban tárolt adatok esetében.

Ezeket a biztonságimásolat-fájlokat nem lehet exportálni. A biztonsági másolatok csak a rugalmas kiszolgálók visszaállítási műveleteihez használhatók. A [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) -t egy MySQL-ügyfélről is használhatja egy adatbázis másolásához.

## <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

A rugalmas kiszolgálókon tárolt biztonsági másolatok pillanatkép-alapúak. Az első teljes pillanatkép biztonsági mentése a kiszolgáló létrehozása után azonnal ütemezve van. Az első teljes pillanatkép biztonsági mentése a kiszolgáló alapbiztonsági mentéseként marad. A pillanatképek későbbi biztonsági mentései csak különbségi biztonsági mentések lesznek.

A különbségi biztonsági mentések legalább naponta egyszer végbemennek. A különbségi biztonsági mentések nem meghatározott ütemezés szerint mennek végbe. A pillanatképek különbségi biztonsági mentései 24 óránként mennek végbe, kivéve, ha a MySQL-ben lévő bináris naplók a legutóbbi különbözeti biztonsági mentés óta meghaladják az 50 GB-ot. Egy adott napon legfeljebb hat különbségi pillanatkép készítése engedélyezett. A tranzakciós naplók biztonsági mentése öt percenként történik.

## <a name="backup-retention"></a>Biztonsági mentés megőrzése

Az adatbázis biztonsági mentéseit egy helyi redundáns tárolóban (LRS) tárolja a rendszer, amely egy adott régión belül három példányban tárolódik. A biztonsági mentések a kiszolgálón tárolt biztonsági másolatok megőrzési időszakának beállítása alapján őrződnek meg. A megőrzési időtartam 1 – 35 nap, az alapértelmezett megőrzési időtartam pedig hét nap. A megőrzési időszakot a kiszolgáló létrehozásakor vagy később állíthatja be, ha a Azure Portal használatával frissíti a biztonsági mentési konfigurációt.

A biztonsági másolatok megőrzési időszaka szabályozza, hogy az idő meddig tart egy időpontra visszamenőleges visszaállítási műveletet, mivel az elérhető biztonsági másolatokon alapul. A biztonsági mentés megőrzési időszaka helyreállítási perspektívában is kezelhető helyreállítási ablakként. A biztonsági másolatok megőrzési időszakán belül az időponthoz való visszaállításhoz szükséges összes biztonsági másolat megmarad a biztonságimásolat-tárolóban. Ha például a biztonsági másolat megőrzési időtartama hét nap, a helyreállítási időszak az elmúlt hét nap. Ebben az esetben a kiszolgáló utolsó hét napban való visszaállításához szükséges összes biztonsági mentést megőrzi a rendszer. A biztonsági másolatok megőrzési időszaka hét nap, az adatbázis-Pillanatképek és a tranzakciónapló biztonsági mentései az elmúlt nyolc napban (1 nappal az ablak előtt) tárolódnak.

## <a name="backup-storage-cost"></a>Biztonsági mentési tárolási díj

A rugalmas kiszolgáló a kiépített kiszolgáló tárterületének akár 100%-át is elérhetővé teszi a biztonsági mentési tárolóként, többletköltség nélkül. Minden további felhasznált biztonsági mentési tárterületért GB/hó díjat számítunk fel. Ha például 250 GB tárterülettel rendelkező kiszolgálót épít ki, akkor a kiszolgáló biztonsági mentéséhez 250 GB tárterület áll rendelkezésre. Ha a napi biztonsági mentés 25 GB-os, akkor akár 10 napos ingyenes biztonsági mentési tárterületet is használhat. A biztonsági mentéshez a 250 GB-nál nagyobb mennyiségű tárterületet a [díjszabási modell](https://azure.microsoft.com/pricing/details/mysql/)szerint számítjuk fel.

A Azure Portalban elérhető Azure Monitor [biztonsági mentési tár](../concepts-monitoring.md) használható metrikával figyelheti a kiszolgáló által felhasznált biztonsági mentési tárterületet. A **biztonsági mentési tár** használt mérőszáma az összes adatbázis biztonsági mentése és a naplózott biztonsági másolatok által felhasznált tárterület összegét adja meg a kiszolgáló biztonsági mentésének megőrzési időszaka alapján. A kiszolgáló magas szintű tranzakciós tevékenysége miatt a biztonsági másolatok tárolási kihasználtsága a teljes adatbázis méretétől függetlenül növekedhet.

A biztonsági mentési tárolási költségek szabályozásának elsődleges módja a biztonsági mentés megőrzési időtartamának beállítása. 1 és 35 nap közötti megőrzési időtartamot választhat.

> [!IMPORTANT]
> Egy zónában konfigurált adatbázis-kiszolgálóról származó biztonsági másolatok redundáns magas rendelkezésre állási konfigurációja az elsődleges adatbázis-kiszolgálóról történik, mivel a terhelés minimális a pillanatképes biztonsági mentésekkel.

> [!IMPORTANT]
> A rugalmas kiszolgálók jelenleg nem támogatják a Geo-redundáns biztonsági mentéseket.

## <a name="point-in-time-restore"></a>Adott időpontnak megfelelő helyreállítás

Azure Database for MySQL rugalmas kiszolgáló esetében az időponthoz tartozó visszaállítás egy új kiszolgálót hoz létre a rugalmas kiszolgálóról a forráskiszolgáló azonos régiójában lévő biztonsági másolatokból. A rendszer az eredeti kiszolgáló konfigurációját hozza létre a számítási szinten, a virtuális mag számát, a tárterület méretét, a biztonsági másolatok megőrzési idejét és a biztonsági mentési redundancia beállítást. Emellett a címkék és beállítások, például a virtuális hálózat és a tűzfal öröklik a forráskiszolgálón található beállításokat. A visszaállított kiszolgáló számítási és tárolási szintjei, a konfiguráció és a biztonsági beállítások a visszaállítás befejeződése után módosíthatók.

> [!NOTE]
> A visszaállítási művelet után két kiszolgálói paraméter van visszaállítva az alapértelmezett értékekre (és nem másolja át az elsődleges kiszolgálóról).
> *   time_zone – ez az érték az alapértelmezett érték RENDSZERre van beállítva
> *   event_scheduler – a event_scheduler be van kapcsolva a visszaállított kiszolgálón

Az időponthoz való visszaállítás több esetben is hasznos lehet. A gyakori használati esetek közé tartoznak a következők:
-   Amikor egy felhasználó véletlenül törli az adatbázist
-   A felhasználó elveszít egy fontos táblát vagy adatbázist
-   A felhasználói alkalmazás véletlenül felülírja a megfelelő adatmennyiséget az alkalmazás hibája miatt.

A legutóbbi visszaállítási pont és az egyéni visszaállítási pont között [Azure Portal](how-to-restore-server-portal.md)használatával választhat.

-   **Legutóbbi visszaállítási pont**: a legújabb visszaállítási pont segítségével visszaállíthatja a kiszolgálót a forráskiszolgálón végrehajtott utolsó biztonsági mentésre. A visszaállítás időbélyege a portálon is megjelenik. Ez a beállítás akkor hasznos, ha gyorsan vissza szeretné állítani a kiszolgálót a legfrissített állapotba.
-   **Egyéni visszaállítási pont**: Ez lehetővé teszi, hogy az adott időpontot a rugalmas kiszolgáló számára meghatározott megőrzési időtartamon belül kiválassza. Ez a beállítás akkor hasznos, ha a kiszolgálót a pontos időpontban szeretné visszaállítani a felhasználói hibákból való helyreállításhoz.

A helyreállítás becsült ideje számos tényezőtől függ, többek között az adatbázis méretétől, a tranzakciós napló biztonsági másolati mérettől, a SKU számítási méretétől és a visszaállítás időpontjától. A visszaállítási folyamat részeként a tranzakciós napló helyreállítása a legtöbb időt vesz igénybe. Ha a visszaállítási idő a teljes vagy a különbözeti pillanatkép biztonsági mentésének időpontjához közelebb van kiválasztva, a visszaállítók gyorsabbak, mivel a tranzakciós napló alkalmazása minimális. A kiszolgáló pontos helyreállítási idejének becsléséhez javasoljuk, hogy tesztelje a környezetében, mert túl sok környezeti változóval rendelkezik.

> [!IMPORTANT]
> Ha olyan rugalmas kiszolgálót állít vissza, amely a zóna redundánsan magas rendelkezésre állását állítja be, a visszaállított kiszolgáló ugyanabban a régióban és zónában lesz konfigurálva, mint az elsődleges kiszolgáló, és a rendszer egyetlen rugalmas kiszolgálóként helyezi üzembe a nem egyidejű módban. Tekintse meg a [zóna redundáns magas rendelkezésre állását](concepts-high-availability.md) a rugalmas kiszolgáló számára.

> [!IMPORTANT]
> A törölt kiszolgálók **nem** állíthatók vissza. Ha törli a kiszolgálót, a kiszolgálóhoz tartozó összes adatbázis is törlődik, és nem állítható helyre. A kiszolgálói erőforrások, a telepítés után a véletlen törlés vagy a váratlan módosítások elleni védelem érdekében a rendszergazdák kihasználhatják a [felügyeleti zárolásokat](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Visszaállítás utáni feladatok végrehajtása

A **legutóbbi visszaállítási pont** vagy az **Egyéni visszaállítási pont** helyreállítási mechanizmusának visszaállítása után a következő feladatokat kell elvégeznie a felhasználók és alkalmazások biztonsági mentésének és futtatásának elvégzéséhez:

-   Ha az új kiszolgáló lecseréli az eredeti kiszolgálót, átirányítja az ügyfeleket és az ügyfélalkalmazások az új kiszolgálóra.
-   Győződjön meg arról, hogy a felhasználók csatlakozhatnak a megfelelő kiszolgálói szintű tűzfallal és virtuális hálózati szabályokkal.
-   Győződjön meg arról, hogy a megfelelő bejelentkezések és az adatbázis-szintű engedélyek vannak érvényben.
-   Szükség szerint konfigurálja a riasztásokat.

## <a name="next-steps"></a>Következő lépések

-   Az [üzletmenet folytonosságának](./concepts-business-continuity.md) megismerése
-   További információ a [zónák redundáns magas rendelkezésre állásáról](./concepts-high-availability.md)
-   Tudnivalók a [biztonsági mentésről és a helyreállításról](./concepts-backup-restore.md)
