---
title: Log Analytics Agent adatforrások a Azure Monitor
description: Az adatforrások határozzák meg azokat a naplózási adatokat, amelyeket a Azure Monitor ügynökökből és más csatlakoztatott forrásokból gyűjt.  Ez a cikk a Azure Monitor adatforrások felhasználásának fogalmát ismerteti, ismerteti a konfigurálásának részleteit, és összefoglalja a különböző elérhető adatforrásokat.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: 51cdee9c899feeb003a7d6301d2da0749fad65e9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041934"
---
# <a name="log-analytics-agent-data-sources-in-azure-monitor"></a>Log Analytics Agent adatforrások a Azure Monitor
Az [log Analytics](./log-analytics-agent.md) ügynököt használó virtuális gépekről Azure monitor gyűjtött adatokat az [log Analytics munkaterületen](../logs/data-platform-logs.md)konfigurált adatforrások határozzák meg.   Minden adatforrás egy adott típusú rekordokat hoz létre, amelyek mindegyike rendelkezik saját tulajdonságokkal.

> [!IMPORTANT]
> Ez a cikk az [log Analytics-ügynök](./log-analytics-agent.md) adatforrásait ismerteti, amely a Azure monitor által használt ügynökök egyike. Más ügynökök különböző adatokat gyűjtenek, és eltérően vannak konfigurálva. A rendelkezésre álló ügynökök és az összegyűjtött adatok listáját lásd: [Azure monitor ügynökök áttekintése](agents-overview.md) .

![Naplózási adatgyűjtés](media/agent-data-sources/overview.png)

> [!IMPORTANT]
> A cikkben ismertetett adatforrások csak az Log Analytics ügynököt futtató virtuális gépekre vonatkoznak. 

## <a name="summary-of-data-sources"></a>Adatforrások összefoglalása
A következő táblázat felsorolja a Log Analytics ügynökkel jelenleg elérhető ügynök-adatforrásokat.  Mindegyiknek van egy külön cikkre mutató hivatkozása, amely részletesen ismerteti az adatforrás részleteit.   Emellett a metódussal és a gyűjtemények gyakoriságával kapcsolatos információkat is tartalmaz. 


| Adatforrás | Platform | Log Analytics-ügynök | Operations Manager-ügynök | Azure Storage tárterület | Operations Manager kötelező? | A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Egyéni naplók](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | érkezéskor |
| [Egyéni naplók](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | érkezéskor |
| [IIS-naplók](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |a naplófájl-átváltási beállítástól függ |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |ütemezett, legalább 10 másodperc |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |ütemezett, legalább 10 másodperc |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |Azure Storage-ból: 10 perc; ügynöktől: érkezéskor |
| [Windows-eseménynaplók](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | érkezéskor |


## <a name="configuring-data-sources"></a>Adatforrások konfigurálása
Log Analytics-ügynökök adatforrásainak konfigurálásához lépjen a Azure Portal **log Analytics munkaterületek** menüjére, és válasszon ki egy munkaterületet. Kattintson az **ügynökök konfigurálása** elemre. Válassza ki a konfigurálni kívánt adatforrás lapját. A fenti táblázatban található hivatkozásokat követve az egyes adatforrások dokumentációját és a konfigurációjuk részleteit is megtekintheti.

Minden konfiguráció az adott munkaterülethez csatlakozó összes ügynök számára elérhető.  A csatlakoztatott ügynökök nem zárhatók ki ebből a konfigurációból.

[![Windows-események konfigurálása](media/agent-data-sources/configure-events.png)](media/agent-data-sources/configure-events.png#lightbox)



## <a name="data-collection"></a>Adatgyűjtés
Az adatforrás-konfigurációk olyan ügynököknek érkeznek, amelyek néhány percen belül közvetlenül kapcsolódnak Azure Monitorhoz.  A megadott adatokat a rendszer az ügynöktől gyűjti, és közvetlenül az egyes adatforrásokra vonatkozó időközönként továbbítja Azure Monitor.  Tekintse meg az egyes adatforrások dokumentációját ezekre a részletekre.

A csatlakoztatott felügyeleti csoportok System Center Operations Manager ügynökei esetében az adatforrás-konfigurációk felügyeleti csomagokba lesznek lefordítva, és alapértelmezés szerint 5 percenként érkeznek a felügyeleti csoportba.  Az ügynök letölti a felügyeleti csomagot, és a megadott adatokat gyűjti. Az adatforrástól függően a rendszer elküldi az adatforrást egy olyan felügyeleti kiszolgálónak, amely továbbítja az adatAzure Monitor, vagy az ügynök elküldi az adatAzure Monitornak a felügyeleti kiszolgáló nélkül. További részletekért lásd: az [Azure-beli figyelési megoldások adatgyűjtési adatai](../monitor-reference.md) .  A Operations Manager összekapcsolásával és Azure Monitorával kapcsolatos részletekért olvassa el a konfigurációt a System Center Operations Manager- [integráció konfigurálása](./om-agents.md)című cikkből.

Ha az ügynök nem tud csatlakozni a Azure Monitorhoz vagy Operations Managerhoz, akkor továbbra is gyűjti az adatokat, amelyeket a kapcsolat létrehozásakor fog teljesíteni.  Az adatvesztés elvész, ha az adatmennyiség eléri a gyorsítótár maximális méretét az ügyfél számára, vagy ha az ügynök 24 órán belül nem tud kapcsolatot létesíteni.

## <a name="log-records"></a>Rekordok naplózása
A Azure Monitor által gyűjtött összes naplózási adatokat a munkaterületen rekordként tárolja a rendszer.  A különböző adatforrások által gyűjtött rekordok saját tulajdonságokkal rendelkeznek, és a **Type (típus** ) tulajdonságuk alapján azonosíthatók.  Az egyes bejegyzéstípusokkal kapcsolatos részletekért tekintse meg az egyes adatforrások és megoldások dokumentációját.

## <a name="next-steps"></a>Következő lépések
* Ismerkedjen meg azokkal a [figyelési megoldásokkal](../insights/solutions.md) , amelyek a Azure monitor funkciókat bővítik, és adatokat gyűjtenek a munkaterületen.
* További információ az adatforrásokból és a figyelési megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../logs/log-query-overview.md) .  
* [Riasztások](../alerts/alerts-overview.md) konfigurálása az adatforrásokból és a figyelési megoldásokból gyűjtött kritikus adatok proaktív értesítésére.