---
title: Riasztás az Azure-Cloud Services kapcsolatos problémákról az Azure-beli Azure Diagnostics-integráció használatával Application Insights | Microsoft Docs
description: Figyelje az Azure Cloud Services Azure-beli rendszerindítási hibáit, összeomlásait és szerepkör-újrahasznosítási ciklusait Application Insights
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.openlocfilehash: 1cdfc6dc3ac74997743512ee07f9293699e3ad10
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87309290"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Riasztás az Azure Cloud Services az Azure Diagnostics Azure-beli integrációjának használatával Application Insights

Ebből a cikkből megtudhatja, hogyan állíthat be olyan riasztási szabályokat, amelyek az indítási hibák, az összeomlások és a szerepkör-újrahasznosítási hurkok figyelését mutatják be az Azure Cloud Services-ben (webes és feldolgozói szerepkörök).

A jelen cikkben ismertetett módszer a [Application Insightsekkel való Azure Diagnostics integráción](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/), valamint a Application Insights képességhez legutóbb kiadott [naplózási riasztásokon](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/) alapul.

## <a name="define-a-base-query"></a>Alapszintű lekérdezés definiálása

Első lépésként definiálunk egy alapszintű lekérdezést, amely lekéri a Windows Eseménynapló eseményeit a Windows Azure-csatornáról, amely nyomkövetési rekordokként rögzítve lesz Application Insights.
Ezek a rekordok az Azure-Cloud Services különböző problémáinak észleléséhez használhatók, például indítási hibák, futásidejű hibák és újrahasznosítási hurkok.

> [!NOTE]
> Az alábbi alaplekérdezés 30 perces időtartományban ellenőrzi a problémákat, és 10 percet vesz igénybe a telemetria rekordok betöltése során. Ezek az alapértelmezett beállítások úgy konfigurálhatók, ahogy jónak látja.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Adott eseményazonosító keresése

A Windows eseménynapló eseményeinek beolvasása után adott problémák észlelhetők a megfelelő eseményazonosító és az üzenet tulajdonságainak ellenőrzésével (lásd az alábbi példákat).
Egyszerűen egyesítse a fenti alaplekérdezést az alábbi lekérdezések egyikével, és használja ezt a kombinált lekérdezést a napló riasztási szabályának meghatározásakor.

> [!NOTE]
> Az alábbi példákban a rendszer problémát észlel, ha az elemzett időintervallumban több mint három esemény található. Ez az alapértelmezett beállítás úgy konfigurálható, hogy megváltoztassa a riasztási szabály érzékenységét.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Riasztás létrehozása

A Application Insights erőforrásának navigációs menüjében lépjen a **riasztások** elemre, majd válassza az **új riasztási szabály** lehetőséget.

![Képernyőfelvétel a szabály létrehozásáról](./media/proactive-cloud-services/001.png)

A **szabály létrehozása** ablakban a **riasztási feltétel meghatározása** szakaszban kattintson a **feltétel hozzáadása** elemre, majd válassza az **egyéni naplók keresése** lehetőséget.

![Képernyőkép a riasztásra vonatkozó feltételek meghatározásáról](./media/proactive-cloud-services/002.png)

A **keresési lekérdezés** mezőbe illessze be az előző lépésben előkészített egyesített lekérdezést.

Ezután folytassa a **küszöbérték** mezővel, és állítsa az értékét 0-ra. Igény szerint megadhatja az **időszak** és a gyakoriság **mezőket**.
Kattintson a **Kész** gombra.

![A jel logikai lekérdezés konfigurálásának képernyőképe](./media/proactive-cloud-services/003.png)

A **riasztás részleteinek meghatározása** szakaszban adja meg a riasztási szabály **nevét** és **leírását** , és állítsa be **súlyosságát**.
Győződjön meg arról is, hogy a létrehozás gombon a **szabály engedélyezése után** beállítás **Igen** értékre van állítva.

![Képernyőkép-riasztás részletei](./media/proactive-cloud-services/004.png)

A **műveleti csoport meghatározása** szakaszban választhat egy meglévő **műveleti csoportot** , vagy létrehozhat egy újat.
Dönthet úgy, hogy a műveleti csoport több különböző típusú műveletet tartalmaz.

![Képernyőfelvételi műveleti csoport](./media/proactive-cloud-services/005.png)

Miután definiálta a műveleti csoportot, erősítse meg a módosításokat, majd kattintson a **riasztási szabály létrehozása** elemre.

## <a name="next-steps"></a>Következő lépések

További információ az automatikus észlelésről:

[Hibák rendellenességei](./proactive-failure-diagnostics.md) 
 [Memóriavesztés](./proactive-potential-memory-leak.md) 
 [Teljesítménnyel kapcsolatos rendellenességek](./proactive-performance-diagnostics.md)

