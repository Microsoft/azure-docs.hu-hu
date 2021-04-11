---
title: Adatátviteli megoldás a Azure Monitorban | Microsoft Docs
description: A vezetékes adatok összevont hálózati és teljesítményadatokat Log Analytics ügynökökkel rendelkező számítógépekről. A hálózati adatok és a naplóadatok összevonása segít az adatok összevetésében.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732023"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Wire Data 2.0 (előzetes verzió) megoldás Azure Monitor (kivont)

![Wire Data-szimbólum](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>A vezetékes adatátviteli megoldás a [VM](../vm/vminsights-overview.md) -elemzésekkel és a [Service Map megoldással](../vm/service-map.md)lett lecserélve.  Mindkettő a Log Analytics-ügynök és a függőségi ügynök használatával gyűjti össze a hálózati kapcsolatok adatait a Azure Monitorba.
>
>Az adatátviteli megoldás támogatása 2022. **március 31-** én megszűnik.  A Wire Data 2.0 (előzetes verzió) megoldást használó meglévő ügyfelek továbbra is használhatják.
>
>Az új és a meglévő ügyfeleknek telepíteniük kell a virtuálisgép- [bepillantást](../vm/vminsights-enable-overview.md) vagy [Service Map megoldást](../vm/service-map.md).  A gyűjtött térképi adatkészletek összehasonlítják a Wire Data 2.0 (előzetes verzió) adatkészlettel.  A VM-elemzések a Service Map adatkészletet, valamint a további teljesítményadatokat és-funkciókat is tartalmazzák. Mindkét ajánlat [kapcsolódott az Azure sentinelhez](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options).
 

A vezetékes adatok összevont hálózati és teljesítményadatokat gyűjtenek a Windowshoz csatlakoztatott és a Linux rendszerű számítógépekről a Log Analytics ügynökkel, beleértve azokat is, amelyeket a környezetében Operations Manager figyel. A hálózati adatok és az egyéb naplóadatok összevonása segít az adatok összevetésében.

A Log Analytics ügynökön kívül az adatátviteli megoldás a Microsoft függőségi ügynököket is használja, amelyeket az informatikai infrastruktúrában lévő számítógépekre telepít. A függőségi ügynökök monitorozzák a számítógépek által fogadott és küldött adatokat az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model) szerinti 2. és 3. szintű hálózatokon, beleértve a különböző alkalmazott protokollokat és portokat. Ezután az ügynökök használatával küldi el az Azure Monitor.

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Migrálás Azure Monitor VM-bepillantást vagy Service Map

Sok esetben azt láthatjuk, hogy az ügyfeleknek gyakran Wire Data 2.0 (előzetes verzió) és virtuálisgép-információk, vagy [Service Map megoldás](../vm/service-map.md) már engedélyezve van ugyanazon a virtuális [gépen](../vm/vminsights-overview.md) .  Ez azt jelenti, hogy a helyettesítő ajánlat engedélyezve van a virtuális gépen.  Egyszerűen [távolítsa el a Wire Data 2.0 (előzetes verzió) megoldást a log Analytics munkaterületről](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

Ha olyan virtuális gépekkel rendelkezik, amelyeken engedélyezve van a Wire Data 2.0 (előzetes verzió), akkor a [virtuális](../vm/vminsights-enable-overview.md) gépeket üzembe helyezheti a VM-adatokban vagy [Service Map megoldásban](../vm/service-map.md) , majd [eltávolíthatja a Wire Data 2.0 (előzetes verzió) megoldást az log Analytics munkaterületről](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution).

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>A lekérdezések áttelepíthetők a VMConnection táblába Azure Monitor VM-adatokból

### <a name="agents-providing-data"></a>Adatkezelési ügynökök

#### <a name="wire-data-20-query"></a>Wire Data 2.0 lekérdezés

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>VM-ismeretek és Service Map-lekérdezés

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>Az adatkezelési ügynökök IP-címei

#### <a name="wire-data-20-query"></a>Wire Data 2.0 lekérdezés

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM-ismeretek és Service Map-lekérdezés

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>Minden kimenő kommunikáció távoli IP-cím szerint

#### <a name="wire-data-20-query"></a>Wire Data 2.0 lekérdezés

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM-ismeretek és Service Map-lekérdezés

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>A protokoll neve által fogadott bájtok száma

#### <a name="wire-data-20-query"></a>Wire Data 2.0 lekérdezés

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>VM-ismeretek és Service Map-lekérdezés

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>Hálózati forgalom (bájtban) mennyisége folyamat szerint

#### <a name="wire-data-20-query"></a>Wire Data 2.0 lekérdezés

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>VM-ismeretek és Service Map-lekérdezés

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>További példák a lekérdezésekre

További példákért tekintse meg a virtuálisgép-információk [naplójának keresési dokumentációját](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search) és a VM-információk [riasztási dokumentációját](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries) .

## <a name="uninstall-wire-data-20-solution"></a>Wire Data 2.0 megoldás eltávolítása

Wire Data 2.0 eltávolításához egyszerűen el kell távolítania a megoldást a Log Analytics-munkaterületről.  Ennek eredményeképpen a következők fognak megjelenni:

* a munkaterülethez csatlakozó virtuális gépekről eltávolított adatkezelés csomag 
* a drót adattípusa már nem jelenik meg a munkaterületen

Kövesse [ezeket az utasításokat](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution) az adatátviteli megoldás eltávolításához.

>[!NOTE]
>Ha a munkaterületen a Service Map vagy a VM-alapú adatellenőrzési megoldással rendelkezik, akkor a felügyeleti csomag nem lesz eltávolítva, mivel ezek a felügyeleti csomagok is ezt a csomagot használják.

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 felügyeleti csomagok

Ha az átviteli adatok aktiválva vannak a Log Analytics-munkaterületen, a rendszer egy 300 KB méretű felügyeleti csomagot küld a munkaterület Windows-kiszolgálóinak. Ha System Center Operations Manager-ügynököt használ egy [csatlakoztatott felügyeleti csoportban](../agents/om-agents.md), a Függőségfigyelő felügyeleti csomag a System Center Operations Managerből lesz telepítve. Ha az ügynökök közvetlenül vannak csatlakoztatva, Azure Monitor biztosítja a felügyeleti csomagot.

A felügyeleti csomag neve Microsoft.IntelligencePacks.ApplicationDependencyMonitor. A következő helyre írja a rendszer: %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs. A felügyeleti csomag az alábbi adatforrást használja: %Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="uninstall-the-dependency-agent"></a>A függőségi ügynök eltávolítása

>[!NOTE]
>Ha Service Map vagy virtuálisgép-elemzéssel szeretné lecserélni a vezetékes adatátvitelt, ne távolítsa el a függőségi ügynököt.

A következő fejezetei segítséget nyújtanak a függőségi ügynök eltávolításához.  

### <a name="uninstall-the-dependency-agent-on-windows"></a>A függőségi ügynök eltávolítása Windows rendszeren

A rendszergazdák a Vezérlőpulton keresztül eltávolíthatják a Windows függőségi ügynököt.

A rendszergazdák az%Programfiles%\Microsoft függőségi Agent\Uninstall.exe is futtathatják a függőségi ügynök eltávolításához.

### <a name="uninstall-the-dependency-agent-on-linux"></a>A függőségi ügynök eltávolítása Linux rendszeren

A függőségi ügynök Linux rendszerből való teljes eltávolításához el kell távolítania magát az ügynököt és az összekötőt, amely automatikusan települ az ügynökkel. Az alábbi egyszerű parancs futtatásával mindkettőt eltávolíthatja:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>A Wire Data 2.0 megoldás használata

Az Azure Portalon a Log Analytics-munkaterület **Áttekintés** területén kattintson a **Wire Data 2.0** csempére az Átviteli adatok irányítópult megnyitásához. Az irányítópulton az alábbi táblázatban felsorolt panelek találhatók. Minden panelen legfeljebb 10 olyan elem jelenik meg, amely megfelel a panel hatóköri és időtartományi kritériumainak. A panel alján található **Az összes megtekintése** elemre vagy a panel fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

| **Panel** | **Leírás** |
| --- | --- |
| Hálózati forgalmat rögzítő ügynökök | A hálózati forgalmat rögzítő ügynökök számát és a forgalmat rögzítő első 10 számítógép listáját jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Kattintson a listában található egyik számítógépre egy olyan naplókeresés futtatásához, amely a rögzített bájtok számát adja vissza. |
| Helyi alhálózatok | Az ügynök által felderített helyi alhálózatok számát mutatja.  Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>. Ez az alhálózatokat és az egyes alhálózatokon átküldött bájtok számát jeleníti meg. Kattintson a listában található alhálózatra egy olyan naplókeresés futtatásához, amely az alhálózaton küldött bájtok teljes számát adja vissza. |
| Alkalmazásszintű protokollok | Az ügynökök által felderített, használatban lévő alkalmazásszintű protokollok számát jeleníti meg. Kattintson a számra a következőre vonatkozó naplókeresés futtatásához: <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Kattintson a protokollra egy olyan naplókeresés futtatásához, amely a protokoll használatával küldött bájtok számát adja vissza. |

![Átviteli adatok irányítópult](./media/wire-data/wire-data-dash.png)

A **Hálózati forgalmat rögzítő ügynökök** panellel megállapíthatja, mekkora hálózati sávszélességet használnak fel a számítógépek. A panel segítségével könnyen megtalálhatja a hálózat _legforgalmasabb_ számítógépét. Előfordulhat, hogy ezek a számítógépek túlterheltek, rendellenesen működnek vagy az átlagosnál több hálózati erőforrást használnak.

![Képernyőfelvétel: az ügynökök a hálózati forgalom panelt rögzítik a Wire Data 2.0 irányítópultján, amely az egyes számítógépek által felhasznált hálózati sávszélességet mutatja.](./media/wire-data/log-search-example01.png)

Ehhez hasonlóan a **Helyi alhálózatok** panel segítségével megállapíthatja, mekkora hálózati forgalom halad át az alhálózatokon. A felhasználók gyakran az alkalmazásaikhoz tartozó kritikus területek körül adnak meg alhálózatokat. Ez a panel ezekbe a területekbe kínál betekintést.

![Képernyőkép a Wire Data 2.0 irányítópult helyi alhálózatok paneljéről, amely az egyes LocalSubnet által felhasznált hálózati sávszélességet mutatja.](./media/wire-data/log-search-example02.png)

Az **Alkalmazásszintű protokollok** panel hasznos, mivel fontos lehet annak ismerete, hogy mely protokollok vannak használatban. Lehetséges például, hogy arra számít, hogy az SSH nincs használatban a hálózati környezetben. A panelen megjelenő információk ezt gyorsan megerősíthetik vagy megcáfolhatják.

![Képernyőkép a Wire Data 2.0-irányítópulton az alkalmazás szintű protokollok panelről, amely az egyes protokollok által felhasznált hálózati sávszélességet mutatja.](./media/wire-data/log-search-example03.png)

Ez akkor is hasznos, ha szeretné tudni, hogy a hálózati forgalom idővel növekszik vagy csökken. Például nem árt tudnia, vagy említésre méltónak találhatja, ha egy alkalmazás által továbbított adatok mennyisége növekszik.

## <a name="input-data"></a>Bemeneti adatok

A Wire Data az engedélyezett ügynökök használatával metaadatokat gyűjt a hálózati forgalomról. Minden ügynök körülbelül 15 másodpercenként küld adatokat.

## <a name="output-data"></a>Kimeneti adatok

A bemeneti adatok minden típusához létrejön egy _WireData_ típusú rekord. A WireData-rekordok tulajdonságai az alábbi táblázatban láthatók:

| Tulajdonság | Leírás |
|---|---|
| Computer | A számítógép neve, ahol az adatgyűjtés történt |
| TimeGenerated | A rekord létrehozásának időpontja |
| LocalIP | A helyi számítógép IP-címe |
| SessionState | Csatlakoztatva vagy leválasztva |
| ReceivedBytes | A fogadott bájtok mennyisége |
| ProtocolName | A használt hálózati protokoll neve |
| IPVersion | IP-cím verziója |
| Irány | Bemeneti vagy kimeneti |
| MaliciousIP | Ismert kártevő forrás IP-címe |
| Súlyosság | Gyanús kártevő súlyossága |
| RemoteIPCountry | A távoli IP-cím országa/régiója |
| ManagementGroupName | Az Operations Manager felügyeleti csoportjának neve |
| SourceSystem | A gyűjtött adatok forrása |
| SessionStartTime | A munkamenet kezdési időpontja |
| SessionEndTime | A munkamenet befejezési időpontja |
| LocalSubnet | Az alhálózat, ahol az adatgyűjtés történt |
| LocalPortNumber | Helyi portszám |
| RemoteIP | A távoli számítógép által használt távoli IP-cím |
| RemotePortNumber | A távoli IP-cím által használt portszám |
| SessionID | A két IP-cím közötti kommunikációs munkameneteket azonosító egyedi érték |
| SentBytes | Az elküldött bájtok száma |
| TotalBytes | A munkamenet során elküldött bájtok száma összesen |
| ApplicationProtocol | A használt hálózati protokoll típusa   |
| ProcessID | Windows-folyamat azonosítója |
| ProcessName | A folyamat elérési útja és neve |
| RemoteIPLongitude | IP-cím hosszúsági értéke |
| RemoteIPLatitude | IP-cím szélességi értéke |

## <a name="next-steps"></a>Következő lépések

- A virtuális gépek figyelésének engedélyezéséhez tekintse meg a VM-információk [üzembe helyezésével](./vminsights-enable-overview.md) kapcsolatos követelményeket és módszereket.
- [Keresés a naplókban](../logs/log-query-overview.md) az átviteli adatokhoz kapcsolódó részletes keresési rekordok megtekintéséhez.
