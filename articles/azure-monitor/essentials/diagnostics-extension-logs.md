---
title: BLOB Storage használata IIS-hez és Table Storage a Azure Monitor eseményeihez | Microsoft Docs
description: A Azure Monitor beolvashatja az Azure-szolgáltatások naplóit, amelyek diagnosztikát írnak a Table Storage vagy a blob Storage-ba írt IIS-naplók számára.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 703a5f145aee93fe7ec4ad2f8ec102f98bdd4174
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616878"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Adatok gyűjtése az Azure Diagnostics bővítményből Azure Monitor naplókba
Az Azure Diagnostics bővítmény olyan [Azure monitor ügynöke](../agents/agents-overview.md) , amely az Azure-beli számítási erőforrások, például a virtuális gépek vendég operációs rendszerének figyelési adatait gyűjti. Ez a cikk azt ismerteti, hogyan gyűjtheti össze a diagnosztikai bővítmény által gyűjtött adatokat az Azure Storage-ból Azure Monitor naplókba.

> [!NOTE]
> A Azure Monitor Log Analytics ügynöke általában az előnyben részesített módszer, amellyel adatokat gyűjthet a vendég operációs rendszerből Azure Monitor naplókba. Az ügynökök részletes összehasonlítását lásd: [a Azure monitor ügynökök áttekintése](../agents/agents-overview.md) .

## <a name="supported-data-types"></a>Támogatott adattípusok
Az Azure Diagnostics bővítmény egy Azure Storage-fiókban tárolja az adatait. Az adatok gyűjtéséhez Azure Monitor naplók esetében a következő helyen kell lennie:

| Napló típusa | Erőforrás típusa | Hely |
| --- | --- | --- |
| IIS-naplók |Virtual Machines <br> Webes szerepkörök <br> Feldolgozói szerepkörök |wad-IIS-LogFiles (Blob Storage) |
| Rendszernapló |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Operatív események Service Fabric |Service Fabric csomópontok |WADServiceFabricSystemEventTable |
| Megbízható Actors-események Service Fabric |Service Fabric csomópontok |WADServiceFabricReliableActorEventTable |
| Megbízható szolgáltatási események Service Fabric |Service Fabric csomópontok |WADServiceFabricReliableServiceEventTable |
| Windows-eseménynaplók |Service Fabric csomópontok <br> Virtual Machines <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW-naplók |Service Fabric csomópontok <br> Virtual Machines <br> Webes szerepkörök <br> Feldolgozói szerepkörök |WADETWEventTable (Table Storage) |

## <a name="data-types-not-supported"></a>Az adattípusok nem támogatottak

- A vendég operációs rendszer teljesítményadatait
- IIS-naplók az Azure websitesból


## <a name="enable-azure-diagnostics-extension"></a>Az Azure Diagnostics bővítmény engedélyezése
A diagnosztikai bővítmény telepítésének és konfigurálásának részleteiért lásd: a [Windows Azure Diagnostics bővítmény (wad) telepítése és konfigurálása](../agents/diagnostics-extension-windows-install.md) , illetve a [Linux diagnosztikai bővítmény használata](../../virtual-machines/extensions/diagnostics-linux.md) . Ezzel a képernyőébresztés megadhatja a Storage-fiókot, és konfigurálhatja azokat az adatgyűjtést, amelyeket továbbítani kíván Azure Monitor naplókba.


## <a name="collect-logs-from-azure-storage"></a>Naplók gyűjtése az Azure Storage-ból
Az alábbi eljárással engedélyezheti a diagnosztikai bővítmények adatainak gyűjtését egy Azure Storage-fiókból:

1. A Azure Portal lépjen a **log Analytics munkaterületek** elemre, és válassza ki a munkaterületet.
1. A menü **munkaterület-adatforrások** szakaszában kattintson a **Storage-fiókok naplói** elemre.
2. Kattintson a  **Hozzáadás** gombra.
3. Válassza ki azt a **Storage-fiókot** , amely a gyűjteni kívánt adatokat tartalmazza.
4. Válassza ki a gyűjteni kívánt **adattípust** .
5. A forrás értékét a rendszer automatikusan kitölti az adattípus alapján.
6. A konfiguráció mentéséhez kattintson **az OK** gombra.
7. További adattípusok esetén ismételje meg a műveletet.

Körülbelül 30 percen belül megtekintheti a Storage-fiók adatait a Log Analytics munkaterületen. A rendszer csak a konfiguráció után a tárolóba írt információt fogja látni. A munkaterület nem olvassa be a meglévő, a Storage-fiókból származó adatait.

> [!NOTE]
> A portál nem ellenőrzi, hogy a forrás létezik-e a Storage-fiókban, vagy új adatírás történik.



## <a name="next-steps"></a>Következő lépések

* [Naplók és mérőszámok gyűjtése az Azure-szolgáltatásokhoz](../platform/resource-logs.md#send-to-log-analytics-workspace) a támogatott Azure-szolgáltatásokhoz.
* [Lehetővé teszi](../insights/solutions.md) , hogy a megoldások betekintést nyújtsanak az adataiba.
* Az adatelemzéshez [használjon keresési lekérdezéseket](../log-query/log-query-overview.md) .

