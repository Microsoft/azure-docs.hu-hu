---
title: Agent Health megoldás a Azure Monitorban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használhatja ezt a megoldást az ügynökök állapotának figyelésére közvetlenül Log Analytics vagy System Center Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: a498c229acce9359acfb4593ec5f833000ca2c39
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573371"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Agent Health megoldás a Azure Monitor
A Agent Health megoldás az Azure-ban segít megérteni, hogy az összes ügynök, amely közvetlenül a Log Analytics munkaterületen jelent meg Azure Monitor vagy egy Azure Monitorhoz csatlakoztatott System Center Operations Manager felügyeleti csoport, amely nem válaszol, és nem küldi el a működési adatmennyiséget.  Azt is nyomon követheti, hogy hány ügynök lett üzembe helyezve, és milyen a földrajzi eloszlásuk, illetve egyéb lekérdezéseket hajthat végre, amelyekkel megismerheti az Azure-ban, egyéb felhőkörnyezetekben, illetve helyszínen üzembe helyezett ügynökök eloszlását.    

## <a name="prerequisites"></a>Előfeltételek
A megoldás üzembe helyezése előtt ellenőrizze, hogy jelenleg támogatottak-e a Log Analytics munkaterületre jelentett [Windows-ügynökök](../agents/agent-windows.md) , vagy a munkaterülethez integrált [Operations Manager felügyeleti csoportnak](../agents/om-agents.md) jelentenek-e jelentéseket.

## <a name="solution-components"></a>Megoldás-összetevők
Ez a megoldás a következő erőforrásokból áll, amelyek a munkaterületéhez lesznek hozzáadva, és ügynökökhöz vagy az Operations Managerhez kapcsolt felügyeleti csoporthoz lesznek közvetlenül hozzákapcsolva.

### <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoport egy Log Analytics munkaterülethez csatlakozik, a következő felügyeleti csomagok lesznek telepítve a Operations Managerban.  Ezeket a felügyeleti csomagokat a megoldás hozzáadását követően a rendszer a közvetlenül kapcsolódó Windows rendszerű számítógépekre is telepíti. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../agents/om-agents.md).

## <a name="configuration"></a>Konfiguráció
Adja hozzá a Agent Health megoldást a Log Analytics munkaterülethez a [megoldások hozzáadása](solutions.md)című témakörben leírt eljárással. Nincs szükség további konfigurációra.


## <a name="data-collection"></a>Adatgyűjtés
### <a name="supported-agents"></a>Támogatott ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatott | Description |
| --- | --- | --- |
| Windows-ügynökök | Yes | A szívverés eseményeket a rendszer a közvetlen Windows-ügynököktől gyűjti össze.|
| System Center Operations Manage felügyeleti csoport | Yes | A szívverési események gyűjtése a felügyeleti csoportnak jelentést küldő ügynököktől 60 másodpercenként történik, majd a Azure Monitorba továbbítva. Nem szükséges közvetlen kapcsolódás Operations Manager ügynököktől Azure Monitor. A szívverési esemény adatait a rendszer a felügyeleti csoportból továbbítja a Log Analytics munkaterületre.|

## <a name="using-the-solution"></a>A megoldás használata
Amikor hozzáadja a megoldást a Log Analytics munkaterülethez, a rendszer hozzáadja a **Agent Health** csempét az irányítópulthoz. Ezen a csempén látható az ügynökök teljes száma és az elmúlt 24 órában nem válaszoló ügynökök száma.<br><br> ![Ügynökállapot megoldás csempe az irányítópulton](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Kattintson az **Ügynökállapot** csempére az **Ügynökállapot** irányítópult megnyitásához.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlop felsorolja azt a tíz eseményt, amelyek a legjobban megfelelnek az adott oszlop időtartományi feltételének. Az oszlopok alján jobb oldalon található **Az összes megtekintése** elemet vagy az oszlopok fejlécét kiválasztva a teljes listát lefedő keresést végezhet a naplóban.

| Oszlop | Leírás |
|--------|-------------|
| Ügynökök darabszáma egységidő alatt | Az ügynökök számának trendje egy hét napos időszakra vetítve, a Linux- és Windows-ügynököket is beleértve.|
| Nem válaszoló ügynökök száma | Azon ügynökök listája, amelyek az elmúlt 24 órában nem küldtek szívverést.|
| Eloszlás operációsrendszer-típusok szerint | A környezetben található Windows- és Linux-ügynökök számának eloszlása.|
| Eloszlás ügynökverzió szerint | A környezetben telepített különböző ügynökverziók eloszlása és mennyisége.|
| Eloszlás ügynökkategória szerint | A szívverés eseményeket küldő különböző ügynökök kategóriáinak eloszlása: közvetlen ügynökök, OpsMgr-ügynökök és az OpsMgr felügyeleti kiszolgáló.|
| Eloszlás felügyeleti csoport szerint | A környezetben lévő különböző Operations Manager felügyeleti csoportok partíciója.|
| Az ügynökök földrajzi helye | A különböző országok/régiók partíciója, ahol az ügynökök és az egyes országokban/régiókban telepített ügynökök számának teljes száma.|
| Telepített átjárók száma | Azon kiszolgálók száma, amelyeken telepítve van a Log Analytics átjáró, valamint a kiszolgálók listája.|

![Ügynökállapot megoldás irányítópultja – példa](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Naplóbejegyzések Azure Monitor
A megoldás egy típusú rekordot hoz létre a Log Analytics munkaterületen.  

### <a name="heartbeat-records"></a>Szívverés rekordok
Egy **Szívverés** típusú rekord készül.  Ezen rekordok tulajdonságait az alábbi táblázat ismerteti.  

| Tulajdonság | Leírás |
| --- | --- |
| `Type` | *Szívverés*|
| `Category` | Az érték lehet *Direct Agent* (Közvetlen ügynök), *SCOM Agent* (SCOM-ügynök) vagy *SCOM Management Server* (SCOM felügyeleti kiszolgáló).|
| `Computer` | A számítógép neve.|
| `OSType` | Windows vagy Linux operációs rendszer.|
| `OSMajorVersion` | Az operációs rendszer főverziója.|
| `OSMinorVersion` | Az operációs rendszer alverziója.|
| `Version` | Log Analytics ügynök vagy Operations Manager ügynök verziója.|
| `SCAgentChannel` | Az érték *Direct* (Közvetlen) és/vagy *SCManagementServer*.|
| `IsGatewayInstalled` | Ha Log Analytics átjáró van telepítve, az érték *true (igaz*), különben az érték *hamis*.|
| `ComputerIP` | A számítógép nyilvános IP-címe. Azure-beli virtuális gépeken ez a nyilvános IP-címet jeleníti meg, ha van ilyen. A magánhálózati IP-címeket használó virtuális gépek esetében ez az Azure SNAT-címet (nem a magánhálózati IP-címet) jeleníti meg. |
| `RemoteIPCountry` | A földrajzi hely, ahol a számítógép üzemel.|
| `ManagementGroupName` | Az Operations Manager felügyeleti csoportjának neve.|
| `SourceComputerId` | A számítógép egyedi azonosítója.|
| `RemoteIPLongitude` | A számítógép földrajzi helyének hosszúsági koordinátája.|
| `RemoteIPLatitude` | A számítógép fölrajzi helyének szélességi koordinátája.|

Minden Operations Manager felügyeleti kiszolgálónak küldött ügynök két szívverést küld, a SCAgentChannel tulajdonság értéke pedig a **közvetlen** és a **SCManagementServer** is, attól függően, hogy milyen adatforrásokat és figyelési megoldásokat engedélyezett az előfizetésében. Ha felidézi, a megoldásokból származó adatokat a rendszer közvetlenül egy Operations Manager felügyeleti kiszolgálóról küldi el Azure Monitor vagy az ügynökön összegyűjtött adatok mennyisége miatt, közvetlenül az ügynöktől a Azure Monitorig küldi el. Az **SCManagementServer** értékű szívverések esetében a ComputerIP értéke a felügyeleti kiszolgáló IP-címe, mivel ez tölti fel az adatokat.  Azoknál a szívveréseknél, ahol az SCAgentChannel beállítása **Direct** (Közvetlen), ez az ügynök nyilvános IP-címe.  

## <a name="sample-log-searches"></a>Naplókeresési minták
A következő táblázat a megoldás által összegyűjtött rekordokkal kapcsolatos naplókeresési mintákat tartalmazza.

| Lekérdezés | Description |
|:---|:---|
| Heartbeat &#124; distinct Computer |Az ügynökök száma összesen |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Az elmúlt 24 órában nem válaszoló ügynökök száma |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Az elmúlt 15 percben nem válaszoló ügynökök száma |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Online számítógépek száma (az elmúlt 24 órában) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Az elmúlt 30 percben offline ügynökök száma (az elmúlt 24 órára vonatkozóan) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Az ügynökök számának trendje időbeli alakulásának lekérése operációsrendszer-típusonként|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Eloszlás operációsrendszer-típusok szerint |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Eloszlás ügynökverzió szerint |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Eloszlás ügynökkategória szerint |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Eloszlás felügyeleti csoport szerint |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Az ügynökök földrajzi helye |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Telepített Log Analytics-átjárók száma |




## <a name="next-steps"></a>Következő lépések

* További információ a [Azure monitor riasztásokról](../alerts/alerts-overview.md) : riasztások létrehozásának részletei a naplók lekérdezésében. 

