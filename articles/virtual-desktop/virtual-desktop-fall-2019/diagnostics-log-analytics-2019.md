---
title: Windows rendszerű virtuális asztali (klasszikus) diagnosztika log Analytics – Azure
description: A log Analytics használata a Windows rendszerű virtuális asztali (klasszikus) diagnosztika szolgáltatással.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9cfa50e13756692295c84b02d02dd71228b16eb9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445001"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Log Analytics használata a Windows Virtual Desktop diagnosztikai szolgáltatásához (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../diagnostics-log-analytics.md).

A Windows rendszerű virtuális asztali szolgáltatás diagnosztikai szolgáltatást biztosít, amely lehetővé teszi, hogy a rendszergazda egyetlen felületen azonosítsa a problémákat. Ez a szolgáltatás naplózza a diagnosztikai adatokat, amikor valaki hozzárendelt egy Windows rendszerű virtuális asztali szerepkört a szolgáltatáshoz. Minden napló tartalmaz információt arról, hogy mely Windows virtuális asztali szerepkört vett részt a tevékenységben, a munkamenet során megjelenő hibaüzeneteket, a bérlői adatokat és a felhasználói adatokat. A diagnosztikai szolgáltatás a felhasználói és rendszergazdai műveletekhez is létrehozza a tevékenység naplóit. Minden tevékenység naplója három fő kategóriába tartozik:

- Hírcsatorna-előfizetési tevékenységek: amikor egy felhasználó Microsoft Távoli asztal-alkalmazásokon keresztül próbál csatlakozni a hírcsatornához.
- Kapcsolódási tevékenységek: amikor egy felhasználó Microsoft Távoli asztal alkalmazáson keresztül próbál csatlakozni egy asztali vagy RemoteApp-hoz.
- Felügyeleti tevékenységek: Ha a rendszergazda felügyeleti műveleteket hajt végre a rendszeren, például a gazdagépek létrehozása, a felhasználók hozzárendelése az alkalmazás-csoportokhoz és a szerepkör-hozzárendelések létrehozása.

Azok a kapcsolatok, amelyek nem érik el a Windows virtuális asztalt, nem jelennek meg a diagnosztikai eredményekben, mert maga a diagnosztikai szerepkör-szolgáltatás a Windows virtuális asztal része. A Windows rendszerű virtuális asztali kapcsolattal kapcsolatos problémák akkor fordulnak elő, ha a felhasználó hálózati kapcsolati problémákba ütközik.

## <a name="why-you-should-use-log-analytics"></a>Miért érdemes használni a Log Analytics

Javasoljuk, hogy a Log Analytics használatával elemezze az Azure-ügyfél diagnosztikai adatait, amely túllépi az egyfelhasználós hibaelhárítást. A virtuálisgép-teljesítményszámlálók beolvasása Log Analytics egy eszköz segítségével gyűjtheti össze az üzembe helyezésre vonatkozó információkat.

## <a name="before-you-get-started"></a>A kezdés előtt

Mielőtt a diagnosztikai szolgáltatással Log Analytics használni, [létre kell hoznia egy munkaterületet](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace).

Miután létrehozta a munkaterületet, kövesse a [Windows rendszerű számítógépek Összekapcsolásának Azure monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) a következő információk beszerzéséhez című témakör utasításait:

- A munkaterület azonosítója
- A munkaterület elsődleges kulcsa

Ezt az információt később a telepítési folyamat során kell megadnia.

## <a name="push-diagnostics-data-to-your-workspace"></a>Diagnosztikai adatai leküldése a munkaterületre

A Windows rendszerű virtuális asztali bérlő diagnosztikai adatait leküldheti a munkaterülethez tartozó Log Analyticsba. Ezt a szolgáltatást azonnal beállíthatja a bérlő első létrehozásakor, ha a munkaterületet a bérlőhöz kapcsolja, vagy később egy meglévő Bérlővel is beállíthatja.

Ha az új bérlő beállítása közben szeretné összekapcsolni a bérlőt a Log Analytics munkaterülettel, futtassa a következő parancsmagot a Windows rendszerű virtuális asztalra való bejelentkezéshez a TenantCreator felhasználói fiókjával:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Ha új bérlő helyett egy meglévő bérlőt szeretne összekapcsolni, futtassa a következő parancsmagot:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Ezeket a parancsmagokat minden olyan bérlőhöz futtatnia kell, amelyhez Log Analytics szeretne csatolni.

>[!NOTE]
>Ha a bérlő létrehozásakor nem szeretné összekapcsolni a Log Analytics munkaterületet, futtassa `New-RdsTenant` inkább a parancsmagot.

## <a name="cadence-for-sending-diagnostic-events"></a>A diagnosztikai események küldésének ritmusa

Ha elkészült, a rendszer a diagnosztikai eseményeket a Log Analytics elküldi.

## <a name="example-queries"></a>Példa a lekérdezésekre

Az alábbi példák azt mutatják be, hogy a diagnosztikai szolgáltatás hogyan készít jelentést a rendszer leggyakoribb tevékenységeiről:

Ez az első példa a támogatott távoli asztali ügyfelekkel rendelkező felhasználók által kezdeményezett kapcsolódási tevékenységeket mutatja be:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

A következő példában szereplő lekérdezés a rendszergazdák által a bérlők számára végzett felügyeleti tevékenységeket mutatja be:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Adatok küldésének leállítása a Log Analyticsba

Ha le szeretné állítani az adatok egy meglévő bérlőről Log Analyticsra való küldését, futtassa a következő parancsmagot, és állítsa be az üres karakterláncokat:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Ezt a parancsmagot minden olyan bérlő esetében futtatnia kell, amelyről le szeretné állítani az adatok küldését.

## <a name="next-steps"></a>Következő lépések

A diagnosztikai szolgáltatás által azonosítható gyakori hibák áttekintése: a [problémák azonosítása és diagnosztizálása](diagnostics-role-service-2019.md#common-error-scenarios).
