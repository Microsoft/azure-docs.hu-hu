---
title: Azure Backup figyelése Azure Monitor
description: Figyelje Azure Backup munkaterheléseket, és hozzon létre egyéni riasztásokat Azure Monitor használatával.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 1800771bfff0afbcec8440383536734246ea8f5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580736"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>A monitor méretezése Azure Monitor használatával

A Azure Backup [beépített figyelési és riasztási képességeket](backup-azure-monitoring-built-in-monitor.md) biztosít egy Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. Ez a beépített szolgáltatás azonban a következő esetekben korlátozott:

- Ha több Recovery Services-tároló adatait figyeli az előfizetések között
- Ha az előnyben részesített értesítési csatorna *nem* e-mail-cím
- Ha a felhasználók további forgatókönyvekhez szeretne riasztásokat kapni
- Ha olyan helyszíni összetevőtől szeretné megtekinteni az információkat, mint például a System Center Data Protection Manager az Azure-ban, a portál nem jelenik meg a [**biztonsági mentési feladatokban**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) vagy a [**biztonsági mentési riasztásokban**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Log Analytics munkaterület használata

### <a name="create-alerts-by-using-log-analytics"></a>Riasztások létrehozása Log Analytics használatával

Azure Monitor a saját riasztásokat a Log Analytics munkaterületen hozhatja létre. A munkaterületen Azure- *műveleti csoportokat* használ a kívánt értesítési mechanizmus kiválasztásához.

> [!IMPORTANT]
> További információ a lekérdezés létrehozásának költségéről: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Nyissa meg a Log Analytics munkaterület **naplók** szakaszát, és hozzon létre egy lekérdezést a saját naplókhoz. Ha **új riasztási szabályt** választ, megnyílik a Azure monitor riasztás létrehozása lap, ahogy az alábbi képen is látható.

![Riasztás létrehozása Log Analytics munkaterületen](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Itt az erőforrás már meg van jelölve Log Analytics munkaterületként, és a műveleti csoport integrációja van megadva.

![Az Log Analytics riasztás – létrehozási oldal](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Riasztási feltétel

A riasztások meghatározó jellemzője az aktiválási feltétel. Válassza ki a **feltételt** a Kusto-lekérdezés automatikus betöltéséhez a **naplók** lapon az alábbi ábrán látható módon. Itt szerkesztheti a feltételt, hogy megfeleljen az igényeinek. További információ: [Sample Kusto-lekérdezések](#sample-kusto-queries).

![Riasztási feltétel beállítása](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Ha szükséges, szerkesztheti a Kusto-lekérdezést. Válassza ki a küszöbértéket, az időszakot és a gyakoriságot. A küszöbérték határozza meg, hogy a riasztás Mikor kerül kiemelésre. Az időszak az az időablak, amelyben a lekérdezés fut. Ha például a küszöbérték nagyobb nullánál, az időszak 5 perc, a gyakoriság pedig 5 perc, a szabály 5 percenként futtatja a lekérdezést, és az előző 5 percet tekinti át. Ha az eredmények száma nagyobb, mint 0, a rendszer értesítést küld a kiválasztott műveleti csoporton.

> [!NOTE]
> Ha naponta egyszer szeretné futtatni a riasztási szabályt az összes, az adott napon létrehozott esemény/napló között, módosítsa a "period" és a "Frequency" érték értékét 1440-ra, azaz 24 órára.

#### <a name="alert-action-groups"></a>Riasztási műveleti csoportok

Az értesítési csatorna megadásához használjon műveleti csoportot. Az elérhető értesítési mechanizmusok megjelenítéséhez a **műveleti csoportok** területen válassza az **új létrehozása** lehetőséget.

![Elérhető értesítési mechanizmusok a "műveleti csoport hozzáadása" ablakban](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Az összes riasztási és figyelési követelményt Log Analytics egyedül is kielégítheti, vagy a Log Analytics használatával kiegészítheti a beépített értesítéseket.

További információ: a [naplók létrehozása, megtekintése és kezelése Azure monitor használatával,](../azure-monitor/alerts/alerts-log.md) valamint [műveleti csoportok létrehozása és kezelése a Azure Portalban](../azure-monitor/alerts/action-groups.md).

### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

Az alapértelmezett diagramok olyan alapszintű forgatókönyvekhez biztosítanak Kusto-lekérdezéseket, amelyeken riasztásokat hozhat létre. A lekérdezéseket úgy is módosíthatja, hogy megkapják azokat az adatkéréseket, amelyekről riasztást szeretne kapni. Illessze be a következő minta Kusto-lekérdezéseket a **naplók** lapra, majd hozzon létre riasztásokat a lekérdezéseken:

- Minden sikeres biztonsági mentési feladat

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Az összes sikertelen biztonsági mentési feladat

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Minden sikeres Azure-beli virtuális gép biztonsági mentési feladata

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Az összes sikeres SQL-napló biztonsági mentési feladata

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Az összes sikeres Azure Backup ügynök feladatai

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Biztonsági mentési tárterületet használ a biztonsági másolati elemek esetében

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Diagnosztikai Adatfrissítés gyakorisága

A tárolóból származó diagnosztikai adatok bekerülnek a Log Analytics munkaterületre, és némi késéssel. Minden esemény *20 – 30 perccel* a log Analytics munkaterületen érkezik, miután leküldte a Recovery Services-tárolóból. További részletek a lag-ról:

- Az összes megoldás esetében a biztonsági mentési szolgáltatás beépített riasztásait azonnal leküldi a rendszer a létrehozásuk után. Így általában 20 – 30 perc múlva jelennek meg az Log Analytics munkaterületen.
- Minden megoldásban az igény szerinti biztonsági mentési feladatok és a visszaállítási feladatok a *befejezésük* után azonnal leküldve lesznek.
- Az SQL Backup kivételével az összes megoldás esetében az ütemezett biztonsági mentési feladatok a *befejezésük* után azonnal leküldve lesznek.
- Az SQL Backup szolgáltatásban, mivel a naplók biztonsági mentései 15 percenként fordulnak elő, az összes befejezett ütemezett biztonsági mentési feladatra vonatkozó információ, beleértve a naplókat, a kötegbe kerül, és 6 óránként küldi el azokat.
- Minden megoldásban, például a biztonsági másolati elem, a házirend, a helyreállítási pontok, a tárterület és így tovább, *naponta legalább egyszer* leküldjük.
- A biztonsági mentési konfiguráció (például a házirend módosítása vagy a szerkesztési szabályzat) változása elindítja az összes kapcsolódó biztonsági mentési információt.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Az Recovery Services-tár tevékenység-naplófájljainak használata

> [!CAUTION]
> Az alábbi lépések csak az Azure-beli *virtuális gépek biztonsági mentésére vonatkoznak.* Ezeket a lépéseket nem használhatja olyan megoldásokhoz, mint például az Azure Backup ügynök, az Azure-ban SQL-alapú biztonsági másolatok vagy Azure Files.

A tevékenység-naplók segítségével értesítéseket kaphat az eseményekről, például a biztonsági mentés sikerességéről. A kezdéshez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra.
2. Nyissa meg a megfelelő Recovery Services-tárolót.
3. A tároló tulajdonságai között nyissa meg a **tevékenység napló** szakaszt.

A megfelelő napló azonosítása és riasztás létrehozása:

1. A következő képen látható szűrők alkalmazásával ellenőrizze, hogy a sikeres biztonsági mentésekhez kapott-e tevékenység naplókat. Módosítsa a **TimeSpan** értékét szükség szerint a rekordok megtekintéséhez.

   ![Az Azure-beli virtuális gépek biztonsági másolatainak keresésére szolgáló szűrési műveletek](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. A megfelelő részletek megtekintéséhez válassza ki a művelet nevét.
3. Válassza az **új riasztási szabály** lehetőséget a **szabály létrehozása** lap megnyitásához.
4. Hozzon létre egy riasztást a [műveletnapló riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/alerts/alerts-activity-log.md)című témakör lépéseit követve.

   ![Új riasztási szabály](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Itt az erőforrás maga a Recovery Services-tároló. Ismételje meg ugyanezeket a lépéseket minden olyan tárolónál, amelyben a tevékenységek naplóján keresztül szeretne értesítést kapni. A feltétel nem tartalmaz küszöbértéket, időszakot vagy gyakoriságot, mert ez a riasztás események alapján történik. A kapcsolódó tevékenység naplójának létrehozása után a rendszer riasztást küld.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics használata a nagy léptékű figyeléshez

Megtekintheti a tevékenységek naplóiból létrehozott összes riasztást és Log Analytics munkaterületeket Azure Monitor. Csak nyissa meg a **riasztások** panelt a bal oldalon.

Bár a tevékenység-naplókon keresztül kaphat értesítéseket, javasoljuk, hogy használja a Log Analyticst, és ne a tevékenység naplóit, hanem a nagy léptékű monitorozást. Ennek indokai a következők:

- **Korlátozott forgatókönyvek**: a tevékenység-naplókon keresztüli értesítések csak az Azure-beli virtuális gépek biztonsági mentésére vonatkoznak. Az értesítéseket minden Recovery Services-tárolóhoz be kell állítani.
- **Definíciós igazítás**: az ütemezett biztonsági mentési tevékenység nem felel meg a tevékenységek naplófájljainak legújabb definíciójának. Ehelyett az [erőforrás-naplókhoz](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)igazodik. Ez az igazítás váratlan hatásokat okoz, ha a tevékenység naplójának csatornáján áthaladó adat megváltozik.
- **Problémák a tevékenység naplójának csatornájában**: Recovery Services-tárolókban, a Azure Backup bepumpált tevékenység-naplók egy új modellt követnek. Ez a változás sajnos a Azure Government, az Azure Germany és az Azure China 21Vianet tevékenység-naplófájljainak előállítására is hatással van. Ha a felhőalapú szolgáltatások felhasználói a Azure Monitorban lévő tevékenységi naplókból hoznak létre vagy konfigurálnak riasztásokat, a riasztások nem aktiválódnak. Továbbá, ha a felhasználó az összes Azure-beli nyilvános régióban [begyűjti Recovery Services tevékenység naplóit egy log Analytics munkaterületre](../azure-monitor/essentials/activity-log.md), akkor ezek a naplók nem jelennek meg.

A Azure Backup által védett munkaterhelések esetében használjon Log Analytics munkaterületet a nagy léptékű figyeléshez és riasztáshoz.

## <a name="next-steps"></a>Következő lépések

Egyéni lekérdezések létrehozásához tekintse meg az [log Analytics adatmodellt](backup-azure-reports-data-model.md).
