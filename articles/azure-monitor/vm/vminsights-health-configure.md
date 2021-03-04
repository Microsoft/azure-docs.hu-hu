---
title: A figyelés konfigurálása a VM-beli betekintő Guest Health szolgáltatásban (előzetes verzió)
description: Ismerteti, hogyan lehet módosítani a virtuális gépekhez tartozó (előzetes verzió) alapértelmezett figyelését a Azure Portal használatával.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/14/2020
ms.openlocfilehash: 0f6599bb9f379cf471dafbb83a7deefbb05c0dbe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052211"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-preview"></a>A figyelés konfigurálása a VM-beli betekintő Guest Health szolgáltatásban (előzetes verzió)
A virtuális gép elemzése lehetővé teszi a virtuális gépek állapotának megtekintését olyan teljesítmény-mérési készlet alapján, amely rendszeres időközönként mintavételt végez. Ez a cikk azt ismerteti, hogyan módosíthatja az alapértelmezett figyelést a Azure Portal használatával. Emellett ismerteti a [figyelőknek az adatgyűjtési szabály használatával történő konfigurálásához](vminsights-health-configure-dcr.md)szükséges alapvető fogalmakat is.

## <a name="open-monitor-configuration"></a>Figyelő konfigurációjának megnyitása
Nyissa meg a figyelő konfigurációs tárolóját a Azure Portal válassza ki a figyelőt, majd a **konfiguráció** lapot.

[![A részleteket figyelő konfiguráció figyelése](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Figyelők engedélyezése vagy letiltása
Az egység figyelők és az összesített figyelők **állapot-figyelési állapota** beállítással engedélyezheti vagy tilthatja le a figyelőt. Ha egy figyelő engedélyezve van, az állapota megjelenik, és a virtuális gép állapotának beállítására szolgál. Ha egy figyelő le van tiltva, a rendszer nem számítja ki az állapotát, és nem használja a virtuális gép állapotának beállítására.

| Beállítás | Leírás |
|:---|:---|
| Engedélyezve | A figyelő engedélyezve van a szülő beállításától függetlenül. |
| Disabled (Letiltva) | A figyelő a szülő beállításától függetlenül le van tiltva. |
| Ugyanaz, mint a szülő | A figyelő a szülő beállításától függően lesz engedélyezve vagy letiltva. |

Ha egy figyelő le van tiltva, az alábbi példában látható módon a feltételek nem érhetők el.

![Letiltott figyelő](media/vminsights-health-configure/disabled-monitor.png)


> [!NOTE]
> Ha a szülő figyelő le van tiltva, akkor a rendszer letiltja az összes gyermek figyelőt is. Ha explicit módon engedélyezte a gyermek figyelőt, akkor a szülő is engedélyezve lesz, de a konfiguráció állapota változatlan marad. Ebben az esetben a szülő figyelőben a következő üzenet jelenik meg.
>
> *A figyelő konfigurált állapotának eltérése "Letiltva", de az állapot nem tükrözi ezt. Ennek az az oka, hogy a konfigurált módosításokat propagálja a rendszer, vagy a gyermek figyelők valamelyikét explicit módon engedélyezték.*

## <a name="enable-or-disable-virtual-machine"></a>Virtuális gép engedélyezése vagy letiltása
A virtuális gép figyelését letilthatja az összes figyelő ideiglenes leállításához. Előfordulhat, hogy letiltja a virtuális gép figyelését, például amikor karbantartást végez rajta.

| Beállítás | Leírás |
|:---|:---|
| Engedélyezve  | Megjelenik a számítógép állapotának állapota. |
| Disabled (Letiltva) | A számítógép állapota **Letiltva** állapotú. A riasztások nincsenek létrehozva. |

## <a name="health-state-logic"></a>Állapot logikája
Az egységekhez tartozó figyelő állapot-logikája határozza meg az állapot beállításának feltételeit. Megadhatja, hogy a figyelő hány állapotot és az egyes állapotok kiszámításának küszöbértékét határozza meg.

![Minta állapotra vonatkozó feltételek](media/vminsights-health-configure/sample-health-criteria.png)

Az összesített figyelők nem határoznak meg állapot-logikát. Az állapotukat az egység figyelők határozzák meg, az állapotuk összesítése alapján.

Az egység figyelők két vagy három állapottal rendelkeznek. Mindegyik mindig kifogástalan állapotú, és opcionálisan figyelmeztetési állapotot, kritikus állapotot vagy mindkettőt is tartalmaz. Figyelmeztetési és kritikus állapotok – az egyes esetekben egyedi feltételek szükségesek, amelyek meghatározzák, hogy mikor van beállítva a figyelő erre az állapotra. A kifogástalan állapot nem rendelkezik feltételekkel, mert ez az állapot akkor van beállítva, ha a többi állapotra vonatkozó feltételek nem teljesülnek.

A következő példában a CPU-kihasználtság a következő állapotokra van beállítva:

- Kritikus, ha meghaladja a 90%-ot.
- Figyelmeztetés, ha a nagyobb vagy egyenlő, mint 80%.
- Kifogástalan állapotú, ha az 80%-ban. Ez hallgatólagos, mert az a feltétel, hogy a többi feltétel sem érvényes.

## <a name="next-steps"></a>Következő lépések

- [A figyelők méretezése az adatgyűjtési szabályok használatával.](vminsights-health-configure-dcr.md)