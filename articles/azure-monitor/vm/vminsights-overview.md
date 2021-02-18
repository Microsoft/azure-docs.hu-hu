---
title: Mi az Azure Monitor for VMs?
description: A Azure Monitor for VMs áttekintése, amely figyeli az Azure-beli virtuális gépek állapotát és teljesítményét, és automatikusan feltérképezi és leképezi az alkalmazás-összetevőket és azok függőségeit.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 796f77a860f0c3303d85aaaae290225841da1b89
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619678"
---
# <a name="overview-of-azure-monitor-for-vms"></a>A virtuális gépekhez készült Azure Monitor áttekintése

Azure Monitor for VMs figyeli a virtuális gépek és a virtuálisgép-méretezési csoportok teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásokra vonatkozó függőségeket. A teljesítménnyel kapcsolatos szűk keresztmetszetek és hálózati problémák azonosításával segítheti a létfontosságú alkalmazások kiszámítható teljesítményét és rendelkezésre állását, és segít megérteni, hogy a probléma más függőségekhez is kapcsolódik-e.

A Azure Monitor for VMs a következő műveleteket támogatja a Windows és Linux operációs rendszereken:

- Azure-beli virtuális gépek
- Azure-beli virtuálisgép-méretezési csoportok
- Az Azure arc-hoz csatlakozó hibrid virtuális gépek
- Helyszíni virtuális gépek
- Más felhőalapú környezetben üzemeltetett virtuális gépek
  

A Azure Monitor for VMs Azure Monitor naplókban tárolja az adatait, ami lehetővé teszi, hogy hatékony összesítést és szűrést nyújtson, és az adattrendeket az idő múlásával elemezze. Ezeket az adatokat egyetlen virtuális gépen tekintheti meg közvetlenül a virtuális gépről, vagy a Azure Monitor használatával több virtuális gép összesített nézetét is továbbíthatja.

![Virtuális gépekkel kapcsolatban bepillantást nyerhet a Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Díjszabás
Azure Monitor for VMs nem jár közvetlen költséggel, de a Log Analytics munkaterületen a tevékenységért kell fizetnie. A [Azure monitor díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján Azure monitor for VMS a következő díjat számítjuk fel:

- Az ügynököktől betöltött és a munkaterületen tárolt adatok.
- A vendég állapotból gyűjtött állapot-adatok (előzetes verzió)
- A riasztási szabályok a naplózási és az állapotadatok alapján.
- Riasztási szabályokból küldött értesítések.

A napló mérete a teljesítményszámlálók hosszának megfelelően változik, és növelheti a virtuális gép számára lefoglalt logikai lemezek és hálózati adapterek számát. Ha már használja a Service Map-t, az egyetlen változás, amelyet a Azure Monitor adattípusnak elküldő további teljesítményadatokat láthat `InsightsMetrics` .


## <a name="configuring-azure-monitor-for-vms"></a>Azure Monitor for VMs konfigurálása
A Azure Monitor for VMs konfigurálásának lépései a következők: Az egyes lépésekhez tartozó részletes útmutatásért kövesse az alábbi hivatkozásokat:

- [Log Analytics munkaterület létrehozása.](../insights/vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights-megoldás hozzáadása a munkaterülethez.](../insights/vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [A figyeléshez telepítse az ügynököket a virtuális gépre és a virtuálisgép-méretezési csoportra.](../insights/vminsights-enable-overview.md)



## <a name="next-steps"></a>Következő lépések

- A virtuális gépek figyelésének engedélyezéséhez tekintse meg az [Azure monitor for VMS telepítése](../insights/vminsights-enable-overview.md) a követelményekhez és a módszerekhez című témakört.

