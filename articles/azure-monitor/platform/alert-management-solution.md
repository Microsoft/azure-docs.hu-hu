---
title: Alert Management megoldás az Azure Log Analyticsban | Microsoft Docs
description: A Log Analytics Alert Management megoldása segít elemezni az összes riasztást a környezetben.  A Log Analyticson belül létrehozott riasztások összevonása mellett a a csatlakoztatott System Center Operations Manager-felügyeleti csoportokból származó riasztásokat Log Analyticsba importálja.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: fe484d8b5a06946b844acb5e506ec4dcc99ebc23
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932730"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Alert Management megoldás az Azure-ban Log Analytics

![Alert Management ikon](media/alert-management-solution/icon.png)

> [!NOTE]
>  A Azure Monitor mostantól támogatja a [riasztások nagy léptékű kezelésére](https://aka.ms/azure-alerts-overview)szolgáló továbbfejlesztett képességeket, beleértve az olyan [figyelési eszközök által generált eszközöket is, mint a System Center Operations Manager, a Zabbix vagy a Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


A Alert Management megoldás segítségével elemezheti a Log Analytics adattár összes riasztását.  Ezek a riasztások különböző forrásokból származnak, beleértve a [log Analytics által létrehozott](../../azure-monitor/platform/alerts-overview.md) vagy [a Nagios-ból vagy Zabbix importált](../../azure-monitor/learn/quick-collect-linux-computer.md)forrásokból származó forrásokat is. A megoldás a [csatlakoztatott System Center Operations Manager felügyeleti csoportok](../../azure-monitor/platform/om-agents.md)riasztásait is importálja.

## <a name="prerequisites"></a>Előfeltételek
A megoldás a Log Analytics-tárházban található összes rekordtal működik, és a **riasztások**típusával a rekordok összegyűjtéséhez bármilyen konfigurációt kell végrehajtania.

- Log Analytics riasztások esetén [hozzon létre riasztási szabályokat](../../azure-monitor/platform/alerts-overview.md) a riasztási rekordok közvetlen létrehozásához a tárházban.
- A Nagios-és Zabbix-riasztások esetében [konfigurálja ezeket a kiszolgálókat](../../azure-monitor/learn/quick-collect-linux-computer.md) a riasztások log Analyticsba küldéséhez.
- System Center Operations Manager riasztások esetén a [Operations Manager felügyeleti csoportot a log Analytics munkaterülethez kell kötni](../../azure-monitor/platform/om-agents.md).  A rendszer a System Center Operations Managerban létrehozott összes riasztást Log Analyticsba importálja.  

## <a name="configuration"></a>Konfiguráció
Adja hozzá a Alert Management megoldást a Log Analytics munkaterülethez a [megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című témakörben leírt eljárással. Nincs szükség további konfigurációra.

## <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoport csatlakozik a Log Analytics-munkaterülethez, a megoldás hozzáadásakor a következő felügyeleti csomagok lesznek telepítve System Center Operations Manager.  Nincs szükség a felügyeleti csomagok konfigurálására és karbantartására.

* Microsoft System Center Advisor Alert Management (Microsoft. IntelligencePacks. AlertManagement)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Adatgyűjtés
### <a name="agents"></a>Ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|:--- |:--- |:--- |
| [Windows-ügynökök](agent-windows.md) | Nem |A közvetlen Windows-ügynökök nem állítanak elő riasztásokat.  Log Analytics riasztások hozhatók létre a Windows-ügynökökből gyűjtött eseményekből és teljesítményadatokat. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem |A közvetlen Linux-ügynökök nem hoznak fel riasztásokat.  Log Analytics riasztások hozhatók létre a Linux-ügynökökből gyűjtött eseményekből és teljesítményadatokat.  A Nagios és a Zabbix riasztások gyűjtése a Linux-ügynököt igénylő kiszolgálókról történik. |
| [System Center Operations Manager felügyeleti csoport](../../azure-monitor/platform/om-agents.md) |Igen |Operations Manager ügynökön létrehozott riasztásokat a rendszer a felügyeleti csoportba küldi, majd továbbítja Log Analyticsnak.<br><br>Nem szükséges közvetlen kapcsolódás Operations Manager ügynököktől Log Analytics. A riasztási adatok továbbítása a felügyeleti csoportból a Log Analytics adattárba történik. |


### <a name="collection-frequency"></a>A gyűjtés gyakorisága
- A riasztási rekordok azonnal elérhetők a megoldásban, amint a tárházban tárolódnak.
- A riasztási adatok küldése a Operations Manager felügyeleti csoportból három percenként Log Analytics.  

## <a name="using-the-solution"></a>A megoldás használata
Amikor hozzáadja a Alert Management megoldást a Log Analytics munkaterülethez, a **Alert Management** csempe hozzá lesz adva az irányítópulthoz.  Ez a csempe az elmúlt 24 órában generált jelenleg aktív riasztások számát és grafikus ábrázolását jeleníti meg.  Ez az időtartomány nem módosítható.

![Alert Management csempe](media/alert-management-solution/tile.png)

A **Alert Management** csempére kattintva nyissa meg a **Alert Management** irányítópultot.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak.  Minden oszlop felsorolja az első 10 riasztást az oszlopnak a megadott hatókörhöz és időtartományhoz tartozó feltételeinek megfelelő számokkal.  Futtathat egy naplóbeli keresést, amely a teljes listát az oszlop alján **található az összes** megjelenítése elemre kattintva vagy az oszlop fejlécére kattintva jeleníti meg.

| Column | Leírás |
|:--- |:--- |
| Kritikus riasztások |A kritikus súlyosságú riasztások a riasztás neve szerint vannak csoportosítva.  Kattintson a riasztás nevére a riasztásra vonatkozó összes rekord visszaadására szolgáló napló kereséséhez. |
| Figyelmeztető riasztások |A figyelmeztetés súlyosságával rendelkező összes riasztást a riasztás neve szerint csoportosítva.  Kattintson a riasztás nevére a riasztásra vonatkozó összes rekord visszaadására szolgáló napló kereséséhez. |
| Aktív System Center Operations Manager riasztások |A Operations Managerból gyűjtött összes riasztás a riasztást generáló forrástól *eltérő* állapotú. |
| Minden aktív riasztás |Minden olyan riasztás, amelynek súlyossága a riasztás neve szerint van csoportosítva. Csak a *lezárt*állapotú Operations Manager riasztásokat tartalmazza. |

Ha a jobb oldali görgetésre kattint, az irányítópulton számos gyakori lekérdezés látható, melyre kattintva elvégezheti a riasztási adatok [naplóbeli keresését](../../azure-monitor/log-query/log-query-overview.md) .

![Alert Management irányítópult](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Alert Management megoldás bármilyen típusú **riasztással**rendelkező rekordot elemez.  A Log Analytics által létrehozott vagy a Nagios vagy Zabbix által gyűjtött riasztásokat a megoldás nem gyűjti közvetlenül.

A megoldás nem importálja a riasztásokat a System Center Operations Managerból, és létrehoz egy megfelelő rekordot a **riasztási** típussal és a **OpsManager**SourceSystem.  Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:  

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*Riasztás* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |A riasztás XML-formátumban való generálását okozó adatelem részletei. |
| `AlertDescription` |A riasztás részletes leírása. |
| `AlertId` |A riasztás GUID azonosítója. |
| `AlertName` |A riasztás neve. |
| `AlertPriority` |A riasztás prioritási szintje. |
| `AlertSeverity` |A riasztás súlyossági szintje. |
| `AlertState` |A riasztás legutóbbi feloldási állapota. |
| `LastModifiedBy` |Annak a felhasználónak a neve, aki utoljára módosította a riasztást. |
| `ManagementGroupName` |Annak a felügyeleti csoportnak a neve, amelyben a riasztás létrejött. |
| `RepeatCount` |Az egyazon figyelt objektumra vonatkozóan a megoldás óta létrejött azonos riasztások száma. |
| `ResolvedBy` |A riasztást feloldott felhasználó neve. Üres, ha a riasztás még nincs megoldva. |
| `SourceDisplayName` |A riasztást létrehozó figyelési objektum megjelenített neve. |
| `SourceFullName` |A riasztást létrehozó figyelési objektum teljes neve. |
| `TicketId` |A riasztáshoz tartozó jegy azonosítója, ha a System Center Operations Manager-környezet integrálva van egy olyan folyamattal, amellyel jegyek rendelhetők a riasztásokhoz.  Nincs megadva a jegy AZONOSÍTÓjának üres értéke. |
| `TimeGenerated` |A riasztás létrehozásának dátuma és időpontja. |
| `TimeLastModified` |A riasztás utolsó módosításának dátuma és időpontja. |
| `TimeRaised` |A riasztás létrehozásának dátuma és időpontja. |
| `TimeResolved` |A riasztás feloldásának dátuma és időpontja. Üres, ha a riasztás még nincs megoldva. |

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által gyűjtött riasztási rekordokra vonatkozó példákat tartalmaz: 

| Lekérdezés | Leírás |
|:---|:---|
| Riasztás &#124; helye: SourceSystem = = "OpsManager" és AlertSeverity = = "Error" és TimeRaised > ago (24 órás) |Az elmúlt 24 órában kiváltott kritikus riasztások |
| Riasztás &#124; helye: AlertSeverity = = "figyelmeztetés" és TimeRaised > ago (24 órás) |Az elmúlt 24 órában kiváltott figyelmeztető riasztások |
| Riasztás &#124; helye: SourceSystem = = "OpsManager" és AlertState! = "lezárva" és TimeRaised > ago ( &#124; 24 órás) összefoglalás száma = count () by SourceDisplayName |Az elmúlt 24 órában kiváltott aktív riasztásokkal rendelkező források |
| Riasztás &#124; helye: SourceSystem = = "OpsManager" és AlertSeverity = = "Error" és TimeRaised > ago (24h) és AlertState! = "lezárva" |Az elmúlt 24 órában kiváltott kritikus riasztások, amelyek még aktívak |
| Riasztás &#124; helye: SourceSystem = = "OpsManager" és TimeRaised > ago (24h) és AlertState = = "lezárva" |Az elmúlt 24 órában kiváltott riasztások mostantól lezárultak |
| Riasztás &#124; helye: SourceSystem = = "OpsManager" és TimeRaised > ago (1d &#124; ) összefoglalás Count = count () by AlertSeverity |Az elmúlt 1 nap során kiváltott riasztások súlyosságuk szerint csoportosítva |
| Riasztás &#124; , amelyben a SourceSystem = = "OpsManager" és a TimeRaised > ago &#124; (1d) rendezése RepeatCount desc alapján |Az elmúlt 1 nap során kiváltott riasztások az ismétlések száma érték szerint rendezve |



## <a name="next-steps"></a>Következő lépések
* A Log Analytics-riasztások létrehozásával kapcsolatos információkért lásd: [Riasztások a Log Analyticsben](../../azure-monitor/platform/alerts-overview.md).
