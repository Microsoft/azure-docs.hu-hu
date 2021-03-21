---
title: A virtuális gépekkel kapcsolatban észlelt hibák megoldása
description: A virtuális gépekkel való betekintés telepítésének hibája.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: 59b4f38efde2416687702647031d2b37553ff8ed
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555412"
---
# <a name="troubleshoot-vm-insights"></a>A virtuális gépekkel kapcsolatban észlelt hibák megoldása
Ez a cikk a virtuálisgép-megállapítások engedélyezése vagy használata során felmerülő problémák elhárításával kapcsolatos információkat tartalmaz.

## <a name="cannot-enable-vm-insights-on-a-machine"></a>Nem engedélyezhető a virtuális gépek elemzése a gépen
Amikor Azure-beli virtuális gépet telepít a Azure Portalból, a következő lépések történnek:

- Ha a beállítás ki van választva, egy alapértelmezett Log Analytics munkaterület jön létre.
- Ha az ügynök már telepítve van, a Log Analytics ügynök virtuálisgép-bővítmény használatával települ az Azure-beli virtuális gépekre.
- A függőségi ügynök az Azure-beli virtuális gépeken bővítmény használatával van telepítve, ha azt meg kell határozni.
  
A bevezetési folyamat során a rendszer ezeket a lépéseket ellenőrzi, valamint a portálon megjelenített értesítési állapotot. A munkaterület és az ügynök telepítésének konfigurálása általában 5 – 10 percet vesz igénybe. Egy újabb 5 – 10 percet is igénybe vehet, amíg az adathozzáférés elérhetővé válik a portálon.

Ha üzenet jelenik meg arról, hogy a virtuális gépet a bevezetési folyamat elvégzése után kell bekészíteni, a folyamat befejeződéséig akár 30 percet is engedélyezhet. Ha a probléma továbbra is fennáll, a következő részekben találhatja meg a lehetséges okokat.

### <a name="is-the-virtual-machine-running"></a>Fut a virtuális gép?
 Ha a virtuális gép ki van kapcsolva egy ideig, ki van kapcsolva jelenleg, vagy csak nemrég lett bekapcsolva, akkor nem fog tudni egy kicsit megjeleníteni, amíg az adatérkezik.

### <a name="is-the-operating-system-supported"></a>Támogatott az operációs rendszer?
Ha az operációs rendszer nem szerepel a [támogatott operációs rendszerek](vminsights-enable-overview.md#supported-operating-systems) listáján, akkor a bővítmény telepítése sikertelen lesz, és látni fogja ezt az üzenetet, amelyet a rendszer az adat megérkezésére vár.

### <a name="did-the-extension-install-properly"></a>Megfelelően települt a bővítmény?
Ha továbbra is megjelenik egy üzenet arról, hogy a virtuális gépnek be kell jelentkeznie, akkor előfordulhat, hogy a bővítmények közül egyet vagy mindkettőt nem sikerült megfelelően telepíteni. A Azure Portalban található virtuális gép **bővítmények** lapján ellenőrizze, hogy a következő bővítmények szerepelnek-e a listáján.

| Operációs rendszer | Ügynökök | 
|:---|:---|
| Windows | MicrosoftMonitoringAgent<br>Microsoft. Azure. monitoring. DependencyAgent |
| Linux | OMSAgentForLinux<br>DependencyAgentForLinux |

Ha nem látja az operációs rendszer mindkét bővítményét a telepített bővítmények listájában, akkor azokat telepíteni kell. Ha a bővítmények fel vannak sorolva, de az állapotuk nem jelenik meg a *kiépítés* során, akkor a bővítményt el kell távolítani és újra kell telepíteni.

### <a name="do-you-have-connectivity-issues"></a>Van kapcsolódási problémája?
Windows rendszerű gépek esetén a  *TestCloudConnectivity* eszköz segítségével azonosíthatja a kapcsolódási problémát. Ez az eszköz alapértelmezés szerint települ a *%systemroot%\Program Files\Microsoft monitoring Agent\Agent* mappában található ügynökkel. Futtassa az eszközt egy rendszergazda jogú parancssorból. A művelet visszaadja az eredményeket, és kiemeli, hogy a teszt sikertelen. 

![TestCloudConnectivity eszköz](media/vminsights-troubleshoot/test-cloud-connectivity.png)

### <a name="more-agent-troubleshooting"></a>További ügynök-hibaelhárítás

A Log Analytics ügynökkel kapcsolatos hibák elhárításához tekintse meg a következő cikkeket:

- [A Windowshoz készült Log Analytics-ügynökkel kapcsolatos hibák elhárítása](../agents/agent-windows-troubleshoot.md)
- [A Linuxhoz készült Log Analytics-ügynökkel kapcsolatos hibák elhárítása](../agents/agent-linux-troubleshoot.md)

## <a name="performance-view-has-no-data"></a>A teljesítmény nézetnek nincsenek adatai
Ha úgy tűnik, hogy az ügynökök megfelelően vannak telepítve, de nem lát semmilyen adat a teljesítmény nézetben, a következő részekben találhatja meg a lehetséges okokat.

### <a name="has-your-log-analytics-workspace-reached-its-data-limit"></a>Elérte a Log Analytics munkaterület az adatkorlátot?
A [kapacitás-foglalások és az adatfeldolgozás díjszabásának](https://azure.microsoft.com/pricing/details/monitor/)ellenőrzését.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>A virtuális gép naplófájlokat és teljesítményadatokat küld Azure Monitor naplókba?

Nyissa meg a Log Analytics a **naplókban** a Azure Portal Azure monitor menüjében. Futtassa a következő lekérdezést a számítógépen:

```kuso
Usage 
| where Computer == "my-computer" 
| summarize sum(Quantity), any(QuantityUnit) by DataType
```

Ha nem lát semmilyen információt, akkor problémák merülhetnek fel az ügynökkel kapcsolatban. Az ügynökkel kapcsolatos hibaelhárítási információkért lásd a fenti szakaszt.

## <a name="virtual-machine-doesnt-appear-in-map-view"></a>A virtuális gép nem jelenik meg a Térkép nézetben

### <a name="is-the-dependency-agent-installed"></a>A függőségi ügynök telepítve van?
 A fenti szakaszban található információk alapján megállapíthatja, hogy a függőségi ügynök telepítve van-e és megfelelően működik-e.

### <a name="are-you-on-the-log-analytics-free-tier"></a>A Log Analytics ingyenes szintet választja?
A [log Analytics ingyenes szintje](https://azure.microsoft.com/pricing/details/monitor/) ez egy örökölt díjszabási csomag, amely legfeljebb öt egyedi Service Map gép használatát teszi lehetővé. A további gépek még akkor sem jelennek meg a Service Mapben, ha az előző öt már nem küld adatokat.

### <a name="is-your-virtual-machine-sending-log-and-performance-data-to-azure-monitor-logs"></a>A virtuális gép naplófájlokat és teljesítményadatokat küld Azure Monitor naplókba?
A teljesítmény nézetben a log lekérdezés [nem tartalmaz](#performance-view-has-no-data) adatszakaszt annak meghatározásához, hogy az adatok gyűjtése folyamatban van-e a virtuális géphez. Ha nem gyűjt adatokat, a fent ismertetett TestCloudConnectivity eszköz használatával állapítsa meg, hogy van-e kapcsolódási problémája.


## <a name="virtual-machine-appears-in-map-view-but-has-missing-data"></a>A virtuális gép megjelenik a Térkép nézetben, de hiányzik az adathalmaz.
Ha a virtuális gép a Térkép nézetben van, akkor a függőségi ügynök telepítve van és fut, de a kernel-illesztőprogram nem töltődött be. Keresse meg a naplófájlt a következő helyeken:

| Operációs rendszer | Napló | 
|:---|:---|
| Windows | C:\Program Files\Microsoft függőségi Agent\logs\wrapper.log |
| Linux | /var/opt/microsoft/dependency-agent/log/service.log |

A fájl utolsó sorai jelzik, hogy a kernel miért nem töltődött be. Előfordulhat például, hogy a Linux nem támogatja a kernelt, ha frissítette a kernelt.
## <a name="next-steps"></a>Következő lépések

- A VM-információk bevezetésével kapcsolatos részletekért lásd: a [VM-információk áttekintésének engedélyezése](vminsights-enable-overview.md).
