---
title: GYIK – SQL Server adatbázisok biztonsági mentése Azure-beli virtuális gépeken
description: Válaszok az Azure-beli virtuális gépeken található SQL Server adatbázisok biztonsági mentésével kapcsolatos gyakori kérdésekre Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: a973761bf16e2d271d718e4a8b29e08624276987
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393143"
---
# <a name="faq-about-sql-server-databases-that-are-running-on-an-azure-vm-backup"></a>Gyakori kérdések az Azure-beli virtuális gépek biztonsági mentését futtató SQL Server-adatbázisokról

Ez a cikk az Azure-beli virtuális gépeken futó SQL Server adatbázisok biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ, és a [Azure Backup](backup-overview.md) szolgáltatást használja.

## <a name="can-i-use-azure-backup-for-iaas-vm-as-well-as-sql-server-on-the-same-machine"></a>Használhatom az Azure Backup szolgáltatást a IaaS virtuális géphez, valamint SQL Server ugyanazon a gépen?

Igen, a virtuális gép biztonsági mentését és az SQL biztonsági mentést is használhatja ugyanazon a virtuális gépen. Ebben az esetben a másolási csak a teljes biztonsági mentést a virtuális gépen, a naplók csonkítása nélkül indítjuk el.

## <a name="does-the-solution-retry-or-auto-heal-the-backups"></a>A megoldás újrapróbálkozik vagy automatikusan meggyógyítja a biztonsági mentéseket?

Bizonyos körülmények között a Azure Backup szolgáltatás elindítja a javító biztonsági mentéseket. Az automatikus gyógyulás az alábbiakban említett hat feltétel bármelyike miatt megtörténhet:

- Ha a naplózási vagy a különbözeti biztonsági mentés LSN-érvényesítési hiba miatt meghiúsul, a következő napló vagy különbözeti biztonsági mentés helyett a teljes biztonsági mentésre lesz konvertálva.
- Ha a napló vagy a különbözeti biztonsági mentés előtt nem történt teljes biztonsági mentés, a rendszer a naplót vagy a különbözeti biztonsági mentést teljes biztonsági mentésre konvertálja.
- Ha a legutolsó teljes biztonsági mentés időpontjának időpontja 15 napnál régebbi, a rendszer a következő naplót vagy különbözeti biztonsági mentést teljes biztonsági mentésre konvertálja.
- A bővítmény frissítése miatt megszakított összes biztonsági mentési feladat újra aktiválódik a frissítés befejeződése után, és a bővítmény elindult.
- Ha a visszaállítás során úgy dönt, hogy felülírja az adatbázist, a következő napló/különbözeti biztonsági mentés meghiúsul, és a rendszer egy teljes biztonsági mentést indít el helyette.
- Ha az adatbázis-helyreállítási modell változása miatt teljes biztonsági mentésre van szükség a naplók alaphelyzetbe állításához, a következő időpontra automatikusan aktiválódik a teljes érték.

Automatikus gyógyulás, mivel a funkció alapértelmezés szerint engedélyezve van az összes felhasználó számára; Ha azonban úgy dönt, hogy kikapcsolja, akkor hajtsa végre az alábbi műveleteket:

- A SQL Server példányon a *C:\Program Files\Azure munkaterhelés Backup\bin* mappában hozza létre vagy szerkessze a **ExtensionSettingsOverrides. JSON** fájlt.
- A **ExtensionSettingsOverrides. JSON**fájlban állítsa be a *{"EnableAutoHealer": false}* értéket.
- Mentse a módosításokat, és zárjuk be a fájlt.
- A SQL Server-példányon nyissa meg a **feladat kezelése** , majd a **AzureWLBackupCoordinatorSvc** szolgáltatás újraindítása műveletet.

## <a name="can-i-control-how-many-concurrent-backups-run-on-the-sql-server"></a>Szabályozható, hogy hány párhuzamos biztonsági mentés fut az SQL Serveren?

Igen. A biztonsági mentési házirend futási arányának szabályozásával csökkentheti a SQL Server-példányra gyakorolt hatást. A beállítás módosítása:

1. A SQL Server-példányon, a *C:\Program Files\Azure munkaterhelés Backup\bin* mappában hozza létre a *ExtensionSettingsOverrides. JSON* fájlt.
2. A *ExtensionSettingsOverrides. JSON* fájlban módosítsa a **DefaultBackupTasksThreshold** beállítást alacsonyabb értékre (például 5). <br>
  `{"DefaultBackupTasksThreshold": 5}`
<br>
A DefaultBackupTasksThreshold alapértelmezett értéke **20**.

3. Mentse a módosításokat, és zárjuk be a fájlt.
4. A SQL Server-példányon nyissa meg a **Feladatkezelő eszközt**. Indítsa újra a **AzureWLBackupCoordinatorSvc** szolgáltatást.<br/> <br/>
 Habár ez a módszer segít, ha a biztonságimásolat-készítő alkalmazás nagy mennyiségű erőforrást használ fel, SQL Server [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor?view=sql-server-2017) sokkal általánosabb módszer a határértékek megadására a bejövő alkalmazások által igényelt CPU, fizikai IO és memória mennyisége tekintetében.

> [!NOTE]
> Az UX-ben továbbra is bármikor előre ütemezheti a sok biztonsági mentést, azonban a fenti példában szereplő, 5-öt lecsúsztatható ablakban fogja feldolgozni.

## <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Futtathatok teljes biztonsági mentést egy másodlagos replikáról?

Az SQL-korlátozások szerint a csak a teljes biztonsági mentést a másodlagos replikán futtathatja. azonban a teljes biztonsági mentés nem engedélyezett.

## <a name="can-i-protect-availability-groups-on-premises"></a>Biztosíthatom a helyszínen a rendelkezésre állási csoportokat?

Nem. A Azure Backup az Azure-ban futó SQL Server-adatbázisokat védi. Ha egy rendelkezésre állási csoport (AG) az Azure és a helyszíni gépek között oszlik el, az AG védelme csak akkor lehetséges, ha az elsődleges replika az Azure-ban fut. Emellett a Azure Backup csak azokat a csomópontokat védi, amelyek ugyanabban az Azure-régióban futnak, mint a Recovery Services-tároló.

## <a name="can-i-protect-availability-groups-across-regions"></a>Biztosíthatom a rendelkezésre állási csoportok kivédését a régiók között?

A Azure Backup Recovery Services-tároló képes felderíteni és védelemmel ellátni az összes olyan csomópontot, amely ugyanabban a régióban található, mint a tároló. Ha a SQL Server always on rendelkezésre állási csoport több Azure-régióra is kiterjed, állítsa be a biztonsági mentést az elsődleges csomóponttal rendelkező régióból. A Azure Backup a biztonsági mentési beállításoknak megfelelően felismeri és védetté teheti a rendelkezésre állási csoportban lévő összes adatbázist. Ha a biztonsági mentési beállítások nem teljesülnek, a biztonsági mentések sikertelenek lesznek, és a rendszer hibaüzenetet küld.

## <a name="do-successful-backup-jobs-create-alerts"></a>A sikeres biztonsági mentési feladatok riasztásokat hozzanak létre?

Nem. A sikeres biztonsági mentési feladatok nem hoznak fel riasztásokat. A rendszer csak a sikertelen biztonsági mentési feladatokhoz küld riasztásokat. A portálon megjelenő riasztások részletes viselkedését [itt](backup-azure-monitoring-built-in-monitor.md)dokumentáljuk. Ha azonban érdekli, hogy még a sikeres feladatok esetében is vannak riasztások, a [figyelést a Azure monitor használatával](backup-azure-monitoring-use-azuremonitor.md)is használhatja.

## <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>Láthatom az ütemezett biztonsági mentési feladatokat a biztonsági mentési feladatok menüben?

A **biztonsági mentési feladat** menü csak az igény szerinti biztonsági mentési feladatokat jeleníti meg. Ütemezett feladatokhoz [Azure monitor használatával történő figyelést](backup-azure-monitoring-use-azuremonitor.md)használhat.

## <a name="are-future-databases-automatically-added-for-backup"></a>A jövőbeli adatbázisok automatikusan hozzáadódnak a biztonsági mentéshez?

Igen, az [automatikus védelem](backup-sql-server-database-azure-vms.md#enable-auto-protection)révén elérheti ezt a képességet.  

## <a name="if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups"></a>Ha törölek egy adatbázist egy automatikusan védett példányból, mi fog történni a biztonsági másolatokban?

Ha egy adatbázis eldobása egy, a védett példányból történik, a rendszer továbbra is megkísérli az adatbázis biztonsági mentését. Ez azt jelenti, hogy a törölt adatbázis nem Kifogástalan állapotba kerül, a **biztonsági mentési elemek** alatt, és továbbra is védve van.

Az adatbázis védelmének leállítására szolgáló helyes módszer a **biztonsági mentés leállítása** az adatbázisban lévő **adatok törlésével** .  

## <a name="if-i-do-stop-backup-operation-of-an-autoprotected-database-what-will-be-its-behavior"></a>Ha leállítom egy automatikusan védett adatbázis biztonsági mentési műveletét, mi lesz a viselkedése?

Ha nem állítja be a **biztonsági mentést az adatmegőrzéssel**, a rendszer nem hajtja végre a jövőbeli biztonsági mentéseket, és a meglévő helyreállítási pontok érintetlenek maradnak. Az adatbázist továbbra is védettnek tekinti a rendszer, és a **biztonsági mentési elemek**alatt jelenik meg.

Ha nem **törli az adatokat a biztonsági mentésből**, a rendszer nem hajtja végre a jövőbeli biztonsági mentéseket, és a meglévő helyreállítási pontokat is törli. Az adatbázis nem védettnek minősül, és a példány alatt jelenik meg a biztonsági mentés konfigurálása területen. Azonban a más, manuálisan kiválasztható vagy az általa védett adatbázisokkal ellentétben az adatbázis szürkén jelenik meg, és nem választható ki. Az adatbázis újbóli védelmének egyetlen módja az automatikus védelem letiltása a példányon. Most kiválaszthatja ezt az adatbázist, és konfigurálhatja a védelmet, vagy újból engedélyezheti az automatikus védelmet a példányon.

## <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior"></a>Ha a védelem után módosítom az adatbázis nevét, mi lesz a viselkedés?

Az átnevezett adatbázist új adatbázisként kezeli a rendszer. A szolgáltatás ezért úgy fogja kezelni ezt a helyzetet, mintha az adatbázis nem található, és a biztonsági mentések sikertelenek lesznek.

Kiválaszthatja az adatbázist, amely már átnevezve van és konfigurálhatja a védelmet. Ha az automatikus védelem engedélyezve van a példányon, a rendszer automatikusan észleli és védi az átnevezett adatbázist.

## <a name="why-cant-i-see-an-added-database-for-an-autoprotected-instance"></a>Miért nem látok hozzáadott adatbázist egy, az autoprotected példányhoz?

Előfordulhat, hogy nem jelenik meg azonnal a védett elemek alatt egy olyan adatbázis, amelyet hozzáad egy, az egy adott, az adott egy adott [példányhoz](backup-sql-server-database-azure-vms.md#enable-auto-protection) . Ennek az az oka, hogy a felderítés általában 8 óránként fut. Az új adatbázisokat azonban azonnal felderítheti és védetté teheti, ha manuálisan futtat egy felderítést, **Ha kiválasztja az**alábbi ábrán látható módon:

  ![Újonnan hozzáadott adatbázis manuális felderítése](./media/backup-azure-sql-database/view-newly-added-database.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [készíthet biztonsági mentést egy](backup-azure-sql-database.md) Azure-beli virtuális gépen futó SQL Server-adatbázisról.
