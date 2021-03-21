---
title: Mi az a VM-alapú adatfelismerés?
description: A VM-információk áttekintése, amely figyeli az Azure-beli virtuális gépek állapotát és teljesítményét, és automatikusan feltérképezi és leképezi az alkalmazás-összetevőket és azok függőségeit.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: 18e1fdcdee347a057c452f6170f36ec7f1f43244
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046414"
---
# <a name="overview-of-vm-insights"></a>A VM-információk áttekintése

A VM-információk figyelik a virtuális gépek és a virtuálisgép-méretezési csoportok teljesítményét és állapotát, beleértve a futó folyamatokat és a más erőforrásokkal kapcsolatos függőségeket is. A teljesítménnyel kapcsolatos szűk keresztmetszetek és hálózati problémák azonosításával segítheti a létfontosságú alkalmazások kiszámítható teljesítményét és rendelkezésre állását, és segít megérteni, hogy a probléma más függőségekhez is kapcsolódik-e.

A VM-ismeretek a következő gépeken támogatják a Windows és a Linux operációs rendszereket:

- Azure-beli virtuális gépek
- Azure-beli virtuálisgép-méretezési csoportok
- Az Azure arc-hoz csatlakozó hibrid virtuális gépek
- Helyszíni virtuális gépek
- Más felhőalapú környezetben üzemeltetett virtuális gépek
  

A VM-elemzések Azure Monitor naplókban tárolják az adattárakat, ami lehetővé teszi, hogy hatékony összesítést és szűrést nyújtson, és az adattrendeket az idő múlásával elemezze. Ezeket az adatokat egyetlen virtuális gépen tekintheti meg közvetlenül a virtuális gépről, vagy a Azure Monitor használatával több virtuális gép összesített nézetét is továbbíthatja.

![Virtuális gépekkel kapcsolatban bepillantást nyerhet a Azure Portal](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Díjszabás
A virtuális gépekkel kapcsolatos bepillantást nem számítjuk fel közvetlen költséggel, de a tevékenységért a Log Analytics munkaterületen kell fizetnie. A [Azure monitor díjszabási oldalon](https://azure.microsoft.com/pricing/details/monitor/)közzétett díjszabás alapján a VM-megállapítások számlázása a következő:

- Az ügynököktől betöltött és a munkaterületen tárolt adatok.
- A vendég állapotból gyűjtött állapot-adatok (előzetes verzió)
- A riasztási szabályok a naplózási és az állapotadatok alapján.
- Riasztási szabályokból küldött értesítések.

A napló mérete a teljesítményszámlálók hosszának megfelelően változik, és növelheti a virtuális gép számára lefoglalt logikai lemezek és hálózati adapterek számát. Ha már használja a Service Map-t, az egyetlen változás, amelyet a Azure Monitor adattípusnak elküldett további teljesítményadatokat láthat `InsightsMetrics` .


## <a name="configuring-vm-insights"></a>A VM-ismeretek konfigurálása
A VM-ismeretek konfigurálásának lépései a következők. Az egyes lépésekhez tartozó részletes útmutatásért kövesse az alábbi hivatkozásokat:

- [Log Analytics munkaterület létrehozása.](./vminsights-configure-workspace.md#create-log-analytics-workspace)
- [VMInsights-megoldás hozzáadása a munkaterülethez.](./vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [A figyeléshez telepítse az ügynököket a virtuális gépre és a virtuálisgép-méretezési csoportra.](./vminsights-enable-overview.md)



## <a name="next-steps"></a>Következő lépések

- A virtuális gépek figyelésének engedélyezéséhez tekintse meg a VM-információk [üzembe helyezésével](./vminsights-enable-overview.md) kapcsolatos követelményeket és módszereket.
