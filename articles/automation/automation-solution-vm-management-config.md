---
title: Azure Automation konfigurálása Start/Stop VMs during off-hours
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Start/Stop VMs during off-hours funkciót a különböző használati esetek és forgatókönyvek támogatásához.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: b52b51133f059f028baf470515e886d17077af6a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593938"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Start/Stop VMs during off-hours konfigurálása

Ez a cikk azt ismerteti, hogyan konfigurálható a [Start/Stop VMS During off-hours](automation-solution-vm-management.md) funkció az ismertetett forgatókönyvek támogatásához. Azt is megtudhatja, hogyan végezheti el a következőket:

* [E-mail-értesítések konfigurálása](#configure-email-notifications)
* [Virtuális gép hozzáadása](#add-a-vm)
* [Virtuális gép kizárása](#exclude-a-vm)
* [Az indítási és leállítási ütemtervek módosítása](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>1. forgatókönyv: virtuális gépek indítása/leállítása ütemterv szerint

Ez a forgatókönyv az alapértelmezett konfiguráció a Start/Stop VMs during off-hours első telepítésekor. Konfigurálhatja például a szolgáltatást úgy, hogy leállítsa az összes virtuális gépet egy előfizetésen belül, amikor az esti munkát elhagyja, és reggel, amikor visszatért az irodába. Ha az üzembe helyezés során ütemezi a **ütemezett StartVM** és az **ütemezett StopVM** , a rendszer elindítja és leállítja a célként megadott virtuális gépeket. 

A szolgáltatás csak a virtuális gépek leállítására való konfigurálása támogatott. Lásd: [az indítási és leállítási ütemtervek módosítása](#modify-the-startup-and-shutdown-schedules) , hogy megtudja, hogyan konfigurálhat egyéni ütemtervet.

> [!NOTE]
> A szolgáltatás által használt időzóna az aktuális időzóna, amikor konfigurálja az ütemterv időpontja paramétert. Azure Automation azonban Azure Automation UTC formátumban tárolja. Nem szükséges időzóna-átalakítást végeznie, mivel ezt a rendszer a gép üzembe helyezése során kezeli.

A hatókörben lévő virtuális gépek szabályozásához konfigurálja a következő változókat: `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` és `External_ExcludeVMNames` .

Engedélyezheti a műveletet egy előfizetéshez és az erőforráscsoporthoz, vagy megcélozhatja a virtuális gépek egy adott listáját, de mindkettőt nem.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és a leállítási műveletek célzása egy előfizetéshez és az erőforráscsoporthoz

1. Konfigurálja a `External_Stop_ResourceGroupNames` és a `External_ExcludeVMNames` változókat a cél virtuális gépek megadásához.

2. Az **ütemezett StartVM** és az **ütemezett StopVM** ütemezések engedélyezése és frissítése.

3. Futtassa a **ScheduledStartStop_Parent** Runbook a **művelet** paraméterrel a **Start** értékre, és a **WHATIF** paraméter értéke TRUE (igaz), hogy megtekintse a módosításokat.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>A Start és a stop művelet megcélzása virtuálisgép-lista alapján

1. Futtassa a **ScheduledStartStop_Parent** runbook a következő **művelettel** : **Start**.

2. Adja hozzá a virtuális gépek vesszővel tagolt listáját (szóközök nélkül) a **VMList** paraméter mezőben. Egy példa a listára `vm1,vm2,vm3` .

3. A módosítások megtekintéséhez állítsa a **WHATIF** paramétert True (igaz) értékre.

4. Konfigurálja a `External_ExcludeVMNames` változót a virtuális gépek vesszővel tagolt listájával (VM1, VM2, VM3), és ne legyen szóköz a vesszővel tagolt értékek között.

5. Ez a forgatókönyv nem tartja tiszteletben a `External_Start_ResourceGroupNames` és `External_Stop_ResourceGroupnames` változókat. Ebben a forgatókönyvben létre kell hoznia a saját automatizálási ütemtervét. Részletekért lásd: [runbook Azure Automationban való beosztása](shared-resources/schedules.md).

    > [!NOTE]
    > A célként megadott **ResourceGroup-nevek** értékét a rendszer a és a értékének megfelelően tárolja `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` . A további részletesség érdekében módosíthatja ezeket a változókat a különböző erőforráscsoportok megcélzásához. Az indítási művelethez használja a parancsot `External_Start_ResourceGroupNames` , és használja `External_Stop_ResourceGroupNames` a stop művelethez. A virtuális gépek automatikusan hozzáadódnak az indítási és leállítási ütemtervekhez.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>2. forgatókönyv: virtuális gépek indítása és leállítása címkék használatával

Egy olyan környezetben, amely több, elosztott munkaterhelést támogató virtuális gépen található kettő vagy több összetevőt tartalmaz, és amely támogatja az összetevők indításának és leállításának sorrendjét. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>A kezdési és a leállítási műveletek célzása egy előfizetéshez és az erőforráscsoporthoz

1. Adjon hozzá egy `sequencestart` és egy `sequencestop` pozitív egész számmal rendelkező címkét a és a `External_Start_ResourceGroupNames` változót használó virtuális gépekhez `External_Stop_ResourceGroupNames` . A kezdési és a leállítási műveletek növekvő sorrendben lesznek végrehajtva. A virtuális gépek címkézésével kapcsolatos további információkért lásd: [Windows rendszerű virtuális gép címkézése az Azure-ban](../virtual-machines/tag-portal.md) , és Linux rendszerű [virtuális gép címkézése az Azure-ban](../virtual-machines/tag-cli.md).

2. Módosítsa az ütemezett **StartVM** és a **sorozatos StopVM** az igényeinek megfelelő dátumra és időpontra, és engedélyezze az ütemtervet.

3. A módosítások előnézetének megtekintéséhez futtassa az **SequencedStartStop_Parent** **runbook a** következővel: **Start** és **WHATIF** True értékre állítva.

4. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépeken való megvalósítás előtt. Ha elkészült, manuálisan hajtsa végre a runbook a (z) paraméterrel **hamis** értékre állítva, vagy hagyja, hogy az automatizálási ütemezések **sorozatos StartVM** és **szekvenciális StopVM** automatikusan fussanak az előírt ütemezése után.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>A kezdő és a leállítási műveletek célzása virtuálisgép-lista alapján

1. `sequencestart` `sequencestop` A paraméterhez hozzáadni kívánt virtuális gépekhez adjon hozzá pozitív egész értékkel rendelkező és egy címkét `VMList` .

2. Futtassa a **SequencedStartStop_Parent** runbook a következő **művelettel** : **Start**.

3. Adja hozzá a virtuális gépek vesszővel tagolt listáját (szóközök nélkül) a **VMList** paraméter mezőben. Egy példa a listára `vm1,vm2,vm3` .

4. Állítsa a **WHATIF** True (igaz) értékre a módosítások megtekintéséhez. 

5. Konfigurálja a `External_ExcludeVMNames` változót a virtuális gépek vesszővel tagolt listájával, a vesszővel tagolt értékek közötti szóközök nélkül.

6. Ez a forgatókönyv nem tartja tiszteletben a `External_Start_ResourceGroupNames` és `External_Stop_ResourceGroupnames` változókat. Ebben a forgatókönyvben létre kell hoznia a saját automatizálási ütemtervét. Részletekért lásd: [runbook Azure Automationban való beosztása](shared-resources/schedules.md).

7. Tekintse meg a műveletet, és végezze el a szükséges módosításokat az éles virtuális gépeken való megvalósítás előtt. Ha elkészült, manuálisan hajtsa végre a **figyelés és diagnosztika/figyelés – művelet – groupsrunbook** paramétert a **false** értékre. Azt is megteheti, hogy az automatizálási ütemezések **sorozatos StartVM** és **szekvenciális StopVM** automatikusan az előírt ütemterv szerint futnak.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>3. forgatókönyv: automatikusan indítás vagy leállítás CPU-kihasználtság alapján

A Start/Stop VMs during off-hours segítségével kezelheti a Azure Resource Manager és a klasszikus virtuális gépek az előfizetésben való futtatásának költségeit úgy, hogy kiértékeli azokat a számítógépeket, amelyeket nem a nem csúcsidőben használ, például órák után, és automatikusan leáll, ha a processzor kihasználtsága kisebb, mint a megadott százalék.

Alapértelmezés szerint a szolgáltatás előre konfigurálva van a százalékos CPU-metrika kiértékeléséhez, hogy az átlagos kihasználtság 5 százalék vagy kevesebb legyen. Ezt a forgatókönyvet a következő változók vezérlik, és módosíthatók, ha az alapértelmezett értékek nem felelnek meg a követelményeknek:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Engedélyezheti és megcélozhatja a műveletet egy előfizetéshez és az erőforráscsoporthoz, vagy megcélozhatja a virtuális gépek egy adott listáját.

A **AutoStop_CreateAlert_Parent** runbook futtatásakor ellenőrzi, hogy a célként megadott előfizetés, erőforráscsoport (ok) és virtuális gépek léteznek-e. Ha a virtuális gépek léteznek, a runbook meghívja a **AutoStop_CreateAlert_Child** runbook a szülő runbook által ellenőrzött összes virtuális géphez. Ez a gyermek runbook:

* Metrikai riasztási szabályt hoz létre minden ellenőrzött virtuális géphez.
* Egy adott virtuális gép **AutoStop_VM_Child** runbook indítja el, ha a processzor a megadott időtartam alatt csökken a beállított küszöbérték alá. 
* Megkísérli leállítani a virtuális gépet.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Az autostop művelet megcélzása az előfizetésben lévő összes virtuális gépen

1. Győződjön meg arról, hogy a `External_Stop_ResourceGroupNames` változó üres, vagy a * (helyettesítő karakter) értékre van állítva.

2. Választható Ha ki szeretne zárni néhány virtuális gépet az autostop műveletből, a virtuális gépek nevének vesszővel tagolt listáját felveheti a `External_ExcludeVMNames` változóba.

3. Az előfizetéshez tartozó összes virtuális gép esetében a szükséges leállítási virtuálisgép-metrikai szabályok létrehozásához engedélyezze a **Schedule_AutoStop_CreateAlert_Parent** ütemezett futtatását. Az ilyen típusú ütemezés futtatásával új metrikai riasztási szabályokat hozhat létre, mivel új virtuális gépeket adnak hozzá az előfizetéshez.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Az autostop művelet megcélzása egy erőforráscsoport vagy több erőforráscsoport összes virtuális gépe esetében

1. Adja hozzá az erőforráscsoportok neveinek vesszővel tagolt listáját a `External_Stop_ResourceGroupNames` változóhoz.

2. Ha ki szeretne zárni néhány virtuális gépet az autostop-ből, a virtuális gépek nevének vesszővel tagolt listáját a változóhoz is hozzáadhatja `External_ExcludeVMNames` .

3. Engedélyezze a **Schedule_AutoStop_CreateAlert_Parent** ütemezett futtatását, hogy létrehozza a szükséges leállítás virtuálisgép-metrika riasztási szabályait az erőforráscsoportok összes virtuális gépén. A művelet egy adott időpontban történő futtatása lehetővé teszi új metrikai riasztási szabályok létrehozását, mivel új virtuális gépek kerülnek be az erőforráscsoport (ok) ba.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Az autostop művelet célja a virtuális gépek listájának megcélzása

1. Hozzon létre egy új [ütemtervet](shared-resources/schedules.md#create-a-schedule) , és kapcsolja össze a **AutoStop_CreateAlert_Parent** runbook, és adja hozzá a virtuális gépek neveinek vesszővel tagolt listáját a `VMList` paraméterhez.

2. Ha néhány virtuális GÉPET ki szeretne zárni az autostop műveletből, hozzáadhat egy vesszővel tagolt listát a virtuálisgép-nevekhez (szóközök nélkül) a `External_ExcludeVMNames` változóhoz.

## <a name="configure-email-notifications"></a>E-mail-értesítések konfigurálása

Ha Start/Stop VMs during off-hours telepítése után módosítani szeretné az e-mail-értesítéseket, módosíthatja az üzembe helyezés során létrehozott műveleti csoportot.  

> [!NOTE]
> A Azure Government-felhőben lévő előfizetések nem támogatják a funkció e-mail-funkcióit.

1. A Azure Portal navigáljon a **figyelés**, majd a **műveleti csoportok** elemre. Válassza ki a **StartStop_VM_Notication** nevű műveleti csoportot.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="Képernyőkép a figyelő – műveleti csoportok lapról.":::

2. A StartStop_VM_Notification **lapon kattintson a részletek** elemre.  Ekkor megnyílik az E-mail/SMS/leküldés/hang lap. Frissítse az e-mail-címet, és kattintson **az OK** gombra a módosítások mentéséhez.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Képernyőkép az e-mailek/SMS/push/Voice lapról, amely egy példaként frissített e-mail-címet mutat be.":::

    Másik lehetőségként további műveleteket is hozzáadhat a műveleti csoporthoz, hogy többet tudjon meg a műveleti csoportokról, lásd: [műveleti csoportok](../azure-monitor/alerts/action-groups.md)

A következő példa egy olyan e-mailt küld, amelyet a szolgáltatás a virtuális gépek leállításakor küld.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="A szolgáltatás által a virtuális gépek leállításakor küldött példa e-mail-üzenet képernyőképe.":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Virtuális gépek hozzáadása vagy kizárása

A funkció lehetővé teszi, hogy a virtuális gépeket megcélozva vagy kizárva adja hozzá. 

### <a name="add-a-vm"></a>Virtuális gép hozzáadása

A szolgáltatás futása során kétféleképpen biztosítható, hogy egy virtuális gép szerepeljen:

* A szolgáltatás minden szülő [runbookok](automation-solution-vm-management.md#runbooks) rendelkezik egy `VMList` paraméterrel. A virtuális gépek neveinek vesszővel tagolt listáját (szóközök nélkül) is átadhatja a paraméternek, ha a megfelelő fölérendelt runbook ütemezi a helyzethez, és ezeket a virtuális gépeket a szolgáltatás futása során is felveszi a rendszer.

* Több virtuális gép kiválasztásához állítson be `External_Start_ResourceGroupNames` és `External_Stop_ResourceGroupNames` azokat az erőforráscsoport-nevekkel, amelyek tartalmazzák az elindítani vagy leállítani kívánt virtuális gépeket. A változókat úgy is beállíthatja, hogy `*` a szolgáltatás az előfizetésben lévő összes erőforráscsoport esetében le legyen állítva.

### <a name="exclude-a-vm"></a>Virtuális gép kizárása

Ha ki szeretne zárni egy virtuális gépet a munkaidőn kívüli leállítási/kezdő virtuális gépekből, adja hozzá a nevét a `External_ExcludeVMNames` változóhoz. Ez a változó az adott virtuális gépek (szóközök nélkül) vesszővel tagolt listája, amely kizárható a szolgáltatásból. Ez a lista 140 virtuális gépre korlátozódik. Ha több mint 140 virtuális gépet ad hozzá a listához, a kizárni kívánt virtuális gépeket a rendszer akaratlanul elindíthatja vagy leállíthatja.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Az indítási és leállítási ütemtervek módosítása

Az indítási és leállítási ütemtervek kezelése ebben a szolgáltatásban ugyanazokat a lépéseket követi, mint a [runbook Azure Automation-ben](shared-resources/schedules.md)való bevezetésének ütemterve. A virtuális gépek elindításához és leállításához külön ütemterv szükséges.

A szolgáltatás konfigurálása a virtuális gépek leállítására egy adott időpontban támogatott. Ebben a forgatókönyvben csak egy leállítási ütemtervet hoz létre, és nem megfelelő kezdési ütemtervet. 

1. Győződjön meg arról, hogy hozzáadta a virtuális gépekhez tartozó erőforráscsoportokat a változóban való leállításhoz `External_Stop_ResourceGroupNames` .

2. Hozzon létre saját ütemtervet, amikor le szeretné állítani a virtuális gépeket.

3. Navigáljon a **ScheduledStartStop_Parent** runbook, és kattintson az **ütemterv** gombra. Ez lehetővé teszi az előző lépésben létrehozott ütemterv kiválasztását.

4. Válassza a paraméterek lehetőséget, **és futtassa a beállításokat** , és állítsa a **művelet** mezőt **Leállítás** értékre.

5. A módosítások mentéséhez kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések

* A szolgáltatás működés közbeni figyeléséhez tekintse meg a [Start/Stop VMS During off-hours lekérdezési naplóit](automation-solution-vm-management-logs.md).
* A virtuális gépek kezelése során felmerülő problémák kezeléséhez tekintse meg [Start/Stop VMS During off-hours problémák elhárítása](troubleshoot/start-stop-vm.md)című témakört.
