---
title: Windows esemény-nyomkövetés (ETW) események gyűjtése elemzési Azure Monitor naplókhoz
description: Megtudhatja, hogyan gyűjthet Windows esemény-nyomkövetés (ETW) Azure Monitor-naplók elemzéséhez.
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: d0ded409d76d0b26a76aebb47b8de8f6143ceba5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719899"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Windows esemény-nyomkövetés (ETW) események gyűjtése elemzési Azure Monitor naplókhoz

A *Windows esemény-nyomkövetés (ETW)* mechanizmust biztosít a felhasználói módú alkalmazások és a kernel módú illesztőprogramok rendszerállapotának kialakításához. A Log Analytics ügynök a felügyeleti és operatív [ETW csatornába](/windows/win32/wes/eventmanifestschema-channeltype-complextype)írt [Windows-események összegyűjtésére](./data-sources-windows-events.md) szolgál. Időnként azonban szükség van a más események, például az analitikus csatornába írt események rögzítésére és elemzésére.  

## <a name="event-flow"></a>Esemény folyamata

Ahhoz, hogy sikeresen begyűjtse a Azure Monitor-naplók elemzésére szolgáló [ETW eseményeket](/windows/win32/etw/about-event-tracing#types-of-providers) , az [Azure Diagnostics bővítményt](./diagnostics-extension-overview.md) kell használnia a Windowshoz (wad). Ebben a forgatókönyvben a diagnosztikai bővítmény a ETW fogyasztóként működik, és az Azure Storage-ba (táblákba) beírja az eseményeket közbenső tárolóként. Itt egy **WADETWEventTable** nevű táblázatban lesz tárolva. A Log Analytics ezután gyűjti a tábla adatait az Azure Storage-ból, és egy **ETWEvent** nevű táblázatként mutatja be.

![Esemény folyamata](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>ETW-naplózási gyűjtemény konfigurálása

### <a name="step-1-locate-the-correct-etw-provider"></a>1. lépés: a megfelelő ETW-szolgáltató megkeresése

Használja a következő parancsok egyikét a ETW-szolgáltatók enumerálásához a forrásként szolgáló Windows rendszeren.

Parancssor:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Dönthet úgy is, hogy ezt a PowerShell-kimenetet Out-Gridview a navigáció támogatásához.

Jegyezze fel a ETW-szolgáltató nevét és GUID azonosítóját, amely a Eseménynaplóban bemutatott analitikai vagy hibakeresési naplóhoz igazodik, vagy pedig arra a modulra, amelyben az esemény adatait össze kívánja gyűjteni.

### <a name="step-2-diagnostics-extension"></a>2. lépés: diagnosztikai bővítmény

Győződjön meg arról, hogy a *Windows Diagnostics bővítmény* [telepítve](./diagnostics-extension-windows-install.md#install-with-azure-portal) van az összes forrásoldali rendszeren.

### <a name="step-3-configure-etw-log-collection"></a>3. lépés: a ETW-naplózási gyűjtemény konfigurálása

1. Navigáljon a virtuális gép **diagnosztikai beállítások** paneljére

2. A **naplók** lap kijelölése

3. Görgessen le, és engedélyezze az **esemény-nyomkövetés a Windowshoz (ETW) – események** a ![ diagnosztikai beállítások képernyőképén](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Állítsa be a szolgáltató GUID azonosítóját vagy a szolgáltatói osztályt azon szolgáltató alapján, amelyhez a gyűjteményt konfigurálja

5. A [**naplózási szint**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session) megfelelő beállítása

6. Kattintson a megadott szolgáltató melletti három pontra, majd kattintson a **Konfigurálás** elemre.

7. Győződjön meg arról, hogy az **alapértelmezett céltábla** **etweventtable** értékre van állítva

8. Szükség esetén [**kulcsszavas szűrő**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) megadása

9. A szolgáltató és a napló beállításainak mentése

A megfelelő események generálása után meg kell jelennie az Azure Storage **WADetweventtable** táblájában megjelenő ETW-események megtekintéséhez. Ezt a Azure Storage Explorer segítségével ellenőrizheti.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>4. lépés: a Log Analytics Storage-fiók gyűjtésének konfigurálása

Kövesse [ezeket az utasításokat](/azure/azure-monitor/agents/diagnostics-extension-logs#collect-logs-from-azure-storage) a naplók Azure Storage-ból való összegyűjtéséhez. A konfigurálást követően a ETW-eseménynek meg kell jelennie a Log Analytics a **ETWEvent** tábla alatt.

## <a name="next-steps"></a>Következő lépések
- [Egyéni mezők](../logs/custom-fields.md) használatával létrehozhat struktúrát a ETW-eseményekben
- További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../logs/log-query-overview.md) .