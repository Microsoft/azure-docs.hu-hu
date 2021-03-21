---
title: Gyakori kérdések – SAP HANA-adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Ebből a cikkből megismerheti a SAP HANA adatbázisok a Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszokat.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: bf662600bafcd18b00c8f8d3b673fc3f9c110aca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95400207"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>Gyakori kérdések – SAP HANA adatbázisok biztonsági mentése Azure-beli virtuális gépeken

Ez a cikk az SAP HANA adatbázisok Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>Hány teljes biztonsági mentést támogat naponta?

Naponta csak egy teljes biztonsági mentést támogatunk. Ugyanazon a napon nem rendelkezhet különbözeti biztonsági mentéssel és teljes biztonsági mentéssel.

### <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok létrehoznak riasztásokat?

Nem. A sikeres biztonsági mentési feladatok nem hoznak fel riasztásokat. A rendszer csak a sikertelen biztonsági mentési feladatokhoz küld riasztásokat. A portálon megjelenő riasztások részletes viselkedését [itt](./backup-azure-monitoring-built-in-monitor.md)dokumentáljuk. Ha azonban még a sikeres feladatok esetében is érdekli a riasztás, [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md)is használhatja.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Láthatom az ütemezett biztonsági mentési feladatokat a biztonsági mentési feladatok menüben?

A biztonsági mentési feladat menü csak az igény szerinti biztonsági mentési feladatokat jeleníti meg. Ütemezett feladatokhoz használja a [Azure monitor](./backup-azure-monitoring-use-azuremonitor.md).

### <a name="are-future-databases-automatically-added-for-backup"></a>A jövőbeli adatbázisokról is automatikusan készül biztonsági mentés?

Nem, ez jelenleg nem támogatott.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>Ha egy példányból törölek egy adatbázist, mi fog történni a biztonsági másolatok?

Ha egy adatbázis eldobása egy SAP HANA-példányból történik, az adatbázis biztonsági mentései továbbra is próbálkoznak. Ez azt jelenti, hogy a törölt adatbázis nem Kifogástalan állapotba kerül, a **biztonsági mentési elemek** alatt, és továbbra is védve van.
Az adatbázis védelmének leállítására szolgáló helyes módszer a **biztonsági mentés leállítása az adatbázisban lévő adatok törlésével** .

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>Ha a védelem után módosítom az adatbázis nevét, mi lesz a viselkedés?

Az átnevezett adatbázist új adatbázisként kezeli a rendszer. Ezért a szolgáltatás úgy fogja kezelni ezt a helyzetet, mintha az adatbázis nem található, és a biztonsági mentések sikertelenek lesznek. Az átnevezett adatbázis új adatbázisként fog megjelenni, és a védelemre kell beállítani.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Mik a SAP HANA-adatbázisok Azure-beli virtuális gépen való biztonsági mentésének előfeltételei?

Tekintse át az [előfeltételeket](tutorial-backup-sap-hana-db.md#prerequisites) , valamint azt, hogy [az előzetes regisztrációs parancsfájl milyen szakaszt tartalmaz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) .

### <a name="what-permissions-should-be-set-so-azure-can-back-up-sap-hana-databases"></a>Milyen engedélyeket kell beállítani, hogy az Azure biztonsági másolatot készítsen SAP HANA adatbázisokról?

Az előzetes regisztrációs parancsfájl futtatásakor a szükséges engedélyek megadásával engedélyezheti az Azure számára a SAP HANA adatbázisok biztonsági mentését. Az előzetes regisztrációs szkriptről [itt](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)talál további információt.

### <a name="will-backups-work-after-migrating-sap-hana-from-sdc-to-mdc"></a>A biztonsági mentések a SDC-ről a MDC-be való SAP HANA Migrálás után fognak működni?

Tekintse át a hibaelhárítási útmutató [ezen szakaszát](./backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) .

### <a name="what-should-be-done-while-upgrading-within-the-same-version"></a>Mi a teendő, ha ugyanazon a verzión belül kell frissíteni?

Tekintse át [ezt a szakaszt](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) a hibaelhárítási útmutatóban.

### <a name="can-azure-hana-backup-be-set-up-against-a-virtual-ip-load-balancer-and-not-a-virtual-machine"></a>Be lehet állítani az Azure HANA Backup szolgáltatást egy virtuális IP-cím (terheléselosztó) és nem virtuális gép között?

Jelenleg nincs lehetőség arra, hogy a megoldást a virtuális IP-címekre állítsa be. A megoldás végrehajtásához virtuális gépre van szükség.

### <a name="how-can-i-move-an-on-demand-backup-to-the-local-file-system-instead-of-the-azure-vault"></a>Hogyan helyezhetek át egy igény szerinti biztonsági mentést a helyi fájlrendszerbe az Azure-tároló helyett?

1. Várja meg, amíg a jelenleg futó biztonsági mentés készen áll a kívánt adatbázisra (a telepítés befejezéséhez keresse meg a stúdiót).
1. Tiltsa le a naplók biztonsági mentését, és állítsa be a katalógus biztonsági mentését a **fájlrendszerre** a kívánt adatbázisra a következő lépések segítségével:
1. Kattintson duplán a **SYSTEMDB**  ->  **konfigurálása**  ->  **adatbázis**  ->  **-szűrő kiválasztása (napló)** elemre.
    1. A enable_auto_log_backup beállítása **nem** értékre.
    1. Catalog_backup_using_backint beállítása **hamis** értékre.
1. Készítsen egy igény szerinti biztonsági mentést (teljes/különbözeti/növekményes) a kívánt adatbázison, és várjon, amíg a biztonsági mentés és a katalógus biztonsági mentése befejeződik.
1. Ha a napló biztonsági másolatait is át szeretné helyezni a fájlrendszerbe, állítsa a enable_auto_log_backup **értéket igen** értékre.
1. Térjen vissza az előző beállításokra, hogy a biztonsági mentések az Azure-tárolóba haladjanak:
    1. Állítsa a enable_auto_log_backup **értéket igen** értékre.
    1. A catalog_backup_using_backint beállítása **igaz** értékre.

>[!NOTE]
>Ha a biztonsági mentéseket a helyi fájlrendszerbe helyezi át, és ismét visszavált az Azure-tárolóra, előfordulhat, hogy a rendszer naplózza a tárolóban lévő biztonsági másolatok naplózási láncát. A művelet elindít egy teljes biztonsági mentést, amely a sikeres befejezést követően elindítja a naplók biztonsági mentését.

### <a name="how-can-i-use-sap-hana-backup-with-my-hana-replication-set-up"></a>Hogyan használhatom a SAP HANA biztonsági mentést a HANA-replikálási beállítással?

A Azure Backup jelenleg nem képes megérteni egy HSR beállítását. Ez azt jelenti, hogy a HSR elsődleges és másodlagos csomópontjai két önálló, nem kapcsolódó virtuális gépre lesznek kezelve. Először konfigurálnia kell a biztonsági mentést az elsődleges csomóponton. Ha feladatátvétel történik, a biztonsági mentést a másodlagos csomóponton kell konfigurálni (amely most az elsődleges csomópont lesz). Nincs automatikus feladatátvétel a másik csomópontra történő biztonsági mentésből.

Ha az aktív (elsődleges) csomópontról szeretne biztonsági másolatot készíteni egy adott időpontra vonatkozóan, **átválthatja a védelmet** a másodlagos csomópontra, amely most már az elsődleges feladatátvétel után válik elérhetővé.

Az alábbi lépéseket követve hajthatja végre a **kapcsoló védelmét**:

- A [védelem leállítása](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) (az adatmegőrzéssel) az elsődlegesen
- Az [előzetes regisztrációs parancsfájl](https://aka.ms/scriptforpermsonhana) futtatása a másodlagos csomóponton
- A másodlagos csomóponton [található adatbázisok felderítése](tutorial-backup-sap-hana-db.md#discover-the-databases) és a [biztonsági mentések konfigurálása](tutorial-backup-sap-hana-db.md#configure-backup)

Ezeket a lépéseket minden feladatátvétel után manuálisan kell elvégezni. Ezeket a lépéseket a Azure Portalon kívül parancssori/HTTP-REST-en keresztül is végrehajthatja. A lépések automatizálásához használhatja az Azure-runbook.

Itt látható egy részletes példa arra, hogyan kell elvégezni a **váltást** :

Ebben a példában két csomóponttal rendelkezik – az 1. csomópont (elsődleges) és a 2. csomópont (másodlagos) a HSR-készletben.  A biztonsági mentések konfigurálása az 1. csomóponton történik. Ahogy fent említettük, ne próbálkozzon még a biztonsági másolatok konfigurálásával a 2. csomóponton.

Ha az első feladatátvétel történik, a 2. csomópont lesz az elsődleges. Majd

1. Állítsa le az 1. csomópont (korábbi elsődleges) védelmét az adat megőrzése beállítással.
1. Futtassa az előzetes regisztrációs parancsfájlt a 2. csomóponton (amely most az elsődleges).
1. Fedezze fel az adatbázisokat a 2. csomóponton, rendeljen biztonsági mentési házirendet, és konfigurálja a biztonsági mentéseket

Ezt követően az első teljes biztonsági mentés a 2. csomóponton, a befejezést követően pedig a naplók biztonsági mentései kezdődnek.

Ha a következő feladatátvétel történik, az 1. csomópont ismét elsődleges lesz, a 2. csomópont pedig másodlagos lesz. Most ismételje meg a folyamatot:

1. Állítsa le a 2. csomópont védelmét az adat megőrzése beállítással.
1. Futtassa az előzetes regisztrációs parancsfájlt az 1. csomóponton (amely még az elsődleges lesz)
1. Ezután [folytassa a biztonsági mentést](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database) az 1. csomóponton a szükséges házirenddel (mivel a biztonsági mentéseket korábban leállították az 1. csomóponton).

Ezután a teljes biztonsági mentés újra aktiválódik az 1. csomóponton, majd a befejezés után a naplók biztonsági mentései megkezdődnek.

## <a name="restore"></a>Visszaállítás

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>Miért nem látom, hogy a HANA-rendszeren Szeretném visszaállítani az adatbázist?

Ellenőrizze, hogy teljesülnek-e a visszaállítás a cél SAP HANA példányra vonatkozó előfeltételei. További információ: [Előfeltételek – SAP HANA adatbázisok visszaállítása az Azure-beli virtuális gépen](./sap-hana-db-restore.md#prerequisites).

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>Miért nem sikerül felülírni az adatbázis-visszaállítást?

Győződjön meg arról, hogy a helyreállítás **kényszerítése** beállítás be van jelölve.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>Miért látom a "forrás-és célszámítógépek visszaállítása nem kompatibilis" hibaüzenetet?

A jelenleg támogatott visszaállítási típusok megtekintéséhez tekintse meg a [1642148](https://launchpad.support.sap.com/#/notes/1642148) -es SAP HANA megjegyzését.

### <a name="can-i-use-a-backup-of-a-database-running-on-sles-to-restore-to-an-rhel-hana-system-or-vice-versa"></a>Használhatok a SLES-on futó adatbázis biztonsági másolatát egy RHEL HANA rendszerre való visszaállításra, vagy fordítva?

Igen, a SLES-on futó HANA-adatbázison aktiválható streaming Backups használatával visszaállíthatja egy RHEL HANA rendszerre, és fordítva. Ez azt jelenti, hogy a rendszer a folyamatos átviteli biztonsági mentést is lehetővé teszi. Azonban győződjön meg arról, hogy a HANA rendszer, amelyet vissza szeretne állítani, és a visszaállításhoz használt HANA-rendszer egyaránt kompatibilis az SAP alapján történő visszaállítással. Tekintse meg SAP HANA Megjegyzés [1642148](https://launchpad.support.sap.com/#/notes/1642148) , hogy mely típusú visszaállítási típusok kompatibilisek.

## <a name="policy"></a>Szabályzat

### <a name="different-options-available-during-creation-of-a-new-policy-for-sap-hana-backup"></a>A SAP HANA biztonsági mentésére vonatkozó új szabályzat létrehozása során különböző lehetőségek érhetők el

A szabályzat létrehozása előtt törölni kell a RPO és a RTO követelményeit, valamint a kapcsolódó költségeket.

A RPO (helyreállítási pont-célkitűzés) azt jelzi, hogy mekkora adatvesztés fogadható el a felhasználó/ügyfél számára. Ezt a napló biztonsági mentési gyakorisága határozza meg. A naplók gyakoribb biztonsági mentései azt jelzik, hogy az alacsonyabb RPO és a Azure Backup szolgáltatás által támogatott minimális érték 15 perc. A napló biztonsági mentésének gyakorisága akár 15 percet is igénybe vehet.

RTO (helyreállítási idő – célkitűzés) – azt jelzi, hogy az adatvesztési forgatókönyv után milyen gyorsan kell visszaállítani az adott időpontot az utolsó elérhető időpontra. Ez a HANA által alkalmazott helyreállítási stratégiától függ, amely általában attól függ, hogy hány fájl szükséges a visszaállításhoz. Ez a költségeket is érinti, és az alábbi táblázat segítséget nyújt az összes forgatókönyv és azok következményeinek megismeréséhez.

|Biztonsági mentési szabályzat  |RTO  |Költségek  |
|---------|---------|---------|
|Napi teljes + naplók     |   Leggyorsabb, mivel csak egy teljes másolási és szükséges naplókra van szükség az időponthoz tartozó visszaállításhoz      |    Costliest beállítás, mivel a teljes másolás naponta történik, így egyre több és több adat gyűlik össze a háttérben, amíg meg nem történik a megőrzési idő   |
|Hetente teljes + napi különbözet + napló     |   A fentinél lassabb, de a következő beállításnál gyorsabb, mivel egy teljes másolási + egy különbözeti másolat + napló szükséges az időponthoz való visszaállításhoz      |    Kevésbé költséges lehetőség, mivel a napi különbözet általában kisebb, mint a teljes, és a teljes másolat csak hetente egyszer kerül beszámításra      |
|Hetente teljes + napi növekmény + napló     |  A leglassabb, mivel egy teljes másolási + "n" növekmény + naplóra van szükség az időponthoz tartozó helyreállításhoz       |     A legdrágább lehetőség, mivel a napi növekmény kisebb a különbözetnél, és a teljes másolat csak hetente jelenik meg.    |

> [!NOTE]
> A fenti lehetőségek a leggyakoribbak, de nem az egyetlen lehetőség. Például hetente két teljes biztonsági mentést és különbözetet használhat hetente és naplókban.

Ezért kiválaszthatja a házirend-változatot a RPO és a RTO célkitűzések és a Cost szempontok alapján.

### <a name="impact-of-modifying-a-policy"></a>A szabályzat módosításának következményei

A biztonsági mentési elem házirendjének az 1. szabályzatból a 2., illetve az 1. (P1) házirendbe való váltásának következményeit figyelembe kell venni.

- Az összes módosítás visszamenőlegesen is érvényben van. A legújabb biztonsági mentési szabályzatot a korábban végrehajtott helyreállítási pontokra is alkalmazza a rendszer. Tegyük fel például, hogy a napi teljes megőrzés 30 nap, a jelenleg aktív házirend szerint pedig 10 helyreállítási pont lett elvégezve. Ha a napi teljes megőrzés 10 napra módosul, akkor az előző pont lejárati idejét is újraszámítja a kezdési idő + 10 nap, és törölheti, ha lejártak.
- A módosítás hatóköre magában foglalja a biztonsági mentés napját, a biztonsági mentés típusát és a megőrzési időt is. Például: Ha egy házirendet vasárnap teljes egészében módosítanak a vasárnaponként, a rendszer minden korábbi, a vasárnapon nem szereplő teljes elemet megjelöl törlésre.
- A szülő nincs törölve, amíg a gyermek aktív/nem járt le. Minden biztonsági mentési típushoz tartozik egy lejárati idő a jelenleg aktív szabályzatnak megfelelően. A teljes biztonsági mentési típust azonban szülőként kell tekinteni a következő "különbözeti", "növekményes" és "naplók" számára. A "különbözet" és a "log" nem a szülők számára bárki másnak. A "növekményes" lehet egy szülő a következő "növekményes" értékre. Még ha a "Parent" jelölése törlésre van megjelölve, nem törlődik ténylegesen, ha a gyermek "különbségek" vagy "naplók" nem jártak le. Ha például egy házirendet a napi teljes és heti teljes egészében módosítanak a vasárnapra, a rendszer minden korábbi, a vasárnapon nem szereplő teljes elemet megjelöl törlésre. De valójában nem törlődnek, amíg a korábban a nap folyamán elkészített naplók lejárnak. Más szóval a napló legutóbbi időtartama szerint megőrzi őket. A naplók lejárta után a rendszer a naplókat és a teljes betelteket is törli.

Ezen alapelvek alapján a következő táblázatból megismerheti a szabályzatok módosításának következményeit.

|Régi házirend/új szabályzat  |Napi teljes és napló  | Hetente betelik + napi különbözet + naplók  |Hetente betelik + napi növekmények + naplók  |
|---------|---------|---------|---------|
|Napi teljes és napló     |   -      |    Az előző, a hét napján nem szereplő teljes összeg törlésre van megjelölve, de a napló megőrzési időszaka alatt marad.     |    Az előző, a hét napján nem szereplő teljes összeg törlésre van megjelölve, de a napló megőrzési időszaka alatt marad.     |
|Hetente betelik + napi különbözet + naplók     |   A rendszer újraszámítja az előző heti teljes adatmegőrzést a legutóbbi szabályzatnak megfelelően. Az előző különbözeteket azonnal törli a rendszer      |    -     |    Az előző különbözeteket azonnal törli a rendszer     |
|Hetente betelik + napi növekmények + naplók     |     A rendszer újraszámítja az előző heti teljes adatmegőrzést a legutóbbi szabályzatnak megfelelően. Az előző növekményeket azonnal törli a rendszer    |     Az előző növekményeket azonnal törli a rendszer    |    -     |

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [készíthet biztonsági mentést](./backup-azure-sap-hana-database.md) az Azure-beli virtuális gépeken futó SAP HANA adatbázisokról.
