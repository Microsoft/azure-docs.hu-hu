---
title: Recovery Services-tárolók diagnosztikai beállításainak használata
description: Ez a cikk a Azure Backup korábbi és új diagnosztikai eseményeinek használatát ismerteti.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575408"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services-tárolók diagnosztikai beállításainak használata

A Azure Backup diagnosztikai eseményeket küld, amelyeket elemzés, riasztás és jelentéskészítés céljából gyűjthet és használhat fel.

A Recovery Services-tároló diagnosztikai beállításait a Azure Portal használatával konfigurálhatja a tárolóba, majd a **diagnosztikai beállítások** lehetőségre kattintva. A **+ diagnosztikai beállítás** választása lehetővé teszi egy vagy több diagnosztikai esemény küldését egy Storage-fiókba, egy Event hub-ba vagy egy log Analytics munkaterületre.

![Diagnosztikai beállítások ablaktábla](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup felhasználók számára elérhető diagnosztikai események

Azure Backup a következő diagnosztikai eseményeket biztosítja. Minden esemény részletes információt tartalmaz a biztonsági másolatok kapcsolódó összetevőinek egy adott készletéről:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Ha továbbra is az [örökölt Event](#legacy-event) AzureBackupReport használja, javasoljuk, hogy váltson át a fenti események használatára.

További információ: [Azure Backup diagnosztikai események adatmodellje](./backup-azure-reports-data-model.md).

Ezeknek az eseményeknek az adatküldhető egy Storage-fiókba, egy Log Analytics-munkaterületre vagy egy Event hub-ba. Ha Log Analytics munkaterületre küldi ezeket az adatokat, válassza ki az **erőforrás-specifikus** kapcsolót a **diagnosztika beállítások** képernyőjén. További információt a következő részekben talál.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Diagnosztikai beállítások használata Log Analytics

Mostantól használhatja a Azure Backupt a tár diagnosztikai adatainak dedikált Log Analytics-táblákba való küldéséhez a biztonsági mentéshez. Ezeket a táblákat [erőforrás-specifikus tábláknak](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)nevezzük.

A tároló diagnosztikai adatainak elküldése a Log Analyticsba:

1. Nyissa meg a tárolót, és válassza a **diagnosztikai beállítások** lehetőséget. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet.
1. Adjon nevet a diagnosztikai beállításoknak.
1. Jelölje be a **küldés log Analyticsra** jelölőnégyzetet, majd válasszon ki egy log Analytics munkaterületet.
1. Válassza ki az **erőforrás-specifikus** elemet a váltáshoz, és válassza ki a következő hat eseményt: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage** és **AddonAzureBackupProtectedInstance**.
1. Kattintson a **Mentés** gombra.

   ![Erőforrás-specifikus mód](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Miután az adatfolyamatok bekerültek a Log Analytics munkaterületre, az egyes eseményekhez tartozó dedikált táblázatok a munkaterületen jönnek létre. Ezeket a táblákat közvetlenül is lekérdezheti. Szükség esetén összekapcsolásokat vagy szakszervezeteket is elvégezhet a táblázatok között.

> [!IMPORTANT]
> A hat esemény, nevezetesen a CoreAzureBackup, a AddonAzureBackupJobs, a AddonAzureBackupAlerts, a AddonAzureBackupPolicy, a AddonAzureBackupStorage és a AddonAzureBackupProtectedInstance *csak* a [biztonsági mentési jelentések](./configure-reports.md)erőforrás-specifikus módjában támogatott. *Ha az Azure Diagnostics-módban próbálja meg elküldeni a hat esemény adatait, a biztonsági mentési jelentésekben nem jelenik meg az adatgyűjtés.*

## <a name="legacy-event"></a>Örökölt esemény

A tárolók biztonsági másolatával kapcsolatos diagnosztikai összes adatát hagyományosan egyetlen, AzureBackupReport nevű esemény tárolja. Az itt leírt hat esemény lényegében az összes, a AzureBackupReport-ben található adatbontás.

Jelenleg továbbra is támogatjuk a AzureBackupReport eseményt a visszamenőleges kompatibilitás érdekében azokban az esetekben, amikor a felhasználók meglévő egyéni lekérdezésekkel rendelkeznek ezen az eseményen. Ilyenek például az egyéni naplózási riasztások és az egyéni vizualizációk. *Javasoljuk, hogy a lehető leghamarabb váltson az [új eseményekre](#diagnostics-events-available-for-azure-backup-users)*. Az új események:

* A naplózási lekérdezésekben sokkal könnyebben dolgozhat az adatkezelési műveletekkel.
* A sémák és a struktúrájuk jobb felderíthetővé válik.
* Javíthatja a teljesítményt a betöltési késés és a lekérdezési idő között.

*Az Azure Diagnostics-módban lévő örökölt esemény végül elavulttá válik. Az új események kiválasztásával elkerülhető, hogy egy későbbi időpontban el lehessen kerülni az összetett áttelepítést*. A Log Analyticst használó [jelentéskészítési megoldás](./configure-reports.md) az örökölt eseményről is leállítja az adatok támogatását.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>A Log Analytics munkaterület új diagnosztikai beállításaira való áttérés lépései

1. Azonosítsa, hogy mely tárolók küldenek adatokat az Log Analytics munkaterületekre az örökölt esemény és a hozzájuk tartozó előfizetések használatával. Futtassa az alábbi lekérdezést az egyes munkaterületeken a tárolók és előfizetések azonosításához.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Az alábbiakban látható egy képernyőkép arról, hogy a lekérdezés az egyik munkaterületen fut:

    ![Munkaterület-lekérdezés](./media/backup-azure-diagnostics-events/workspace-query.png)

2. A Azure Backup [beépített Azure Policy-definícióinak](./azure-policy-configure-diagnostics.md) használatával új diagnosztikai beállítást adhat hozzá a megadott hatókörben lévő összes tárolóhoz. Ez a házirend új diagnosztikai beállítást hoz létre olyan tárolók számára, amelyek nem rendelkeznek diagnosztikai beállítással, vagy csak örökölt diagnosztikai beállításokkal rendelkeznek. Ezt a házirendet egyszerre lehet hozzárendelni egy teljes előfizetéshez vagy erőforráscsoporthoz. Tulajdonosi hozzáféréssel kell rendelkeznie minden olyan előfizetéshez, amelyhez a szabályzat hozzá van rendelve.

Dönthet úgy is, hogy külön diagnosztikai beállításokat használ a AzureBackupReport és a hat új eseményhez, amíg az összes egyéni lekérdezést át nem telepítette az új táblákból származó adatok használatára. Az alábbi képen egy olyan tároló látható, amely két diagnosztikai beállítással rendelkezik. A **Setting1** nevű első beállítás egy AzureBackupReport-esemény adatait küldi el az Azure diagnostics üzemmód log Analytics munkaterületére. Az **Setting2** nevű második beállítás a hat új Azure Backup eseményt küldi el egy log Analytics munkaterületre az erőforrás-specifikus módban.

![Két beállítás](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Az AzureBackupReport-esemény *csak* Azure Diagnostics módban támogatott. *Ha erőforrás-specifikus módban kísérli meg az eseményre vonatkozó adatküldést, a Log Analytics munkaterületre nem kerül be az adatforgalom.*

> [!NOTE]
> Az **Azure Diagnostics** vagy az **erőforrás-specifikus** váltógomb csak akkor jelenik meg, ha a felhasználó a **Küldés log Analytics** lehetőséget választja. Ha az adatokat egy Storage-fiókba vagy egy Event hubhoz szeretné elküldeni, a felhasználó kiválasztja a szükséges célhelyet, és bejelöli a kívánt eseményekhez tartozó jelölőnégyzeteket a további bemenetek nélkül. Azt javasoljuk, hogy ne válassza az örökölt eseményt AzureBackupReport.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Azure Site Recovery események küldése Log Analytics

Azure Backup és Azure Site Recovery az eseményeket ugyanabból a Recovery Services-tárból küldik. Az Azure Site Recovery jelenleg nem érhető el erőforrás-specifikus táblákhoz. Azok a felhasználók, akik Azure Site Recovery eseményeket szeretnének küldeni Log Analyticsre, *csak* az Azure Diagnostics mód használatára vannak irányítva, ahogy az a képen látható. *Azure site Recovery események erőforrás-specifikus üzemmódjának kiválasztása megakadályozza, hogy a rendszer elküldje a szükséges adatoknak a log Analytics munkaterületre*.

![Események Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Összegezve:

* Ha már beállította Log Analytics diagnosztikát a Azure Diagnostics és az egyéni lekérdezésekkel rendelkezik, tartsa *érintetlenül* a beállítást, amíg át nem telepíti a lekérdezéseket az új eseményekről származó adatok használatára.
* Ha új táblákat is szeretne bevezetni, a javasolt módon hozzon létre egy **új** diagnosztikai beállítást, válassza az **erőforrás-specifikus** lehetőséget, és válassza ki a hat új eseményt.
* Ha jelenleg Azure Site Recovery eseményeket küld Log Analyticsra *, ne* válassza az adott eseményekhez tartozó erőforrás-specifikus módot. Ellenkező esetben az események adatai nem kerülnek be a Log Analytics munkaterületre. Ehelyett hozzon létre egy további diagnosztikai beállítást, válassza az **Azure Diagnostics** lehetőséget, majd válassza ki a megfelelő Azure site Recovery eseményeket.

Az alábbi képen egy olyan felhasználó látható, aki három diagnosztikai beállítással rendelkezik a tárolóhoz. Az első beállítás, a **Setting1** nevű AzureBackupReport-esemény adatokat küld az Azure diagnosztikai üzemmód log Analytics munkaterületére. A második, **Setting2** nevű beállítás a hat új Azure Backup eseményből küld adatokat az erőforrás-specifikus módban lévő log Analytics-munkaterületre. A **Setting3** nevű harmadik beállítás a Azure site Recovery eseményekről küld adatokat az Azure diagnostics üzemmód log Analytics munkaterületére.

![Három beállítás](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Következő lépések

[A diagnosztikai események Log Analytics adatmodelljének megismerése](./backup-azure-reports-data-model.md)
