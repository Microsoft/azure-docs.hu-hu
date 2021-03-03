---
title: Azure hálózatkezelési elemzési megoldás a Azure Monitorban | Microsoft Docs
description: A Azure Monitor Azure Networking Analytics megoldásával áttekintheti az Azure hálózati biztonsági csoportok naplóit és az Azure Application Gateway naplóit.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: b8b03378e82810bc2b9680805bacf8360f322a94
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708135"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Azure hálózatkezelési figyelési megoldások Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor a következő megoldásokat kínálja a hálózatok figyelésére:
* Network Performance Monitor (NPM)
    * A hálózat állapotának figyelése
* Az Azure Application Gateway Analytics áttekintése
    * Azure Application Gateway-naplók
    * Azure Application Gateway mérőszámok
* Megoldások a hálózati tevékenységek figyelésére és naplózására a felhőalapú hálózaton
    * [Traffic Analytics](../../networking/network-monitoring-overview.md#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

A [Network Performance monitor](../../networking/network-monitoring-overview.md) felügyeleti megoldás egy olyan hálózati figyelési megoldás, amely figyeli a hálózatok állapotát, rendelkezésre állását és elérhetőségét.  A következők közötti kapcsolat figyelésére használható:

* Nyilvános felhő és helyszíni
* Adatközpontok és felhasználói helyszínek (fiókirodák)
* Többrétegű alkalmazások különböző szintjeinek üzemeltetésére szolgáló alhálózatok.

További információ: [Network Performance monitor](../../networking/network-monitoring-overview.md).

## <a name="network-security-group-analytics"></a>Hálózati biztonsági csoport elemzése

1. Adja hozzá a felügyeleti megoldást Azure Monitorhoz, és
2. A diagnosztika engedélyezése a diagnosztika a Azure Monitor Log Analytics munkaterületre való irányításához. A naplókat nem szükséges az Azure Blob Storage-ba írni.

Ha a diagnosztikai naplók nincsenek engedélyezve, az adott erőforráshoz tartozó irányítópult-pengék üresek, és hibaüzenetet jelenítenek meg.

## <a name="azure-application-gateway-analytics"></a>Azure Application Gateway Analitika

1. A diagnosztika engedélyezése a diagnosztika a Azure Monitor Log Analytics munkaterületre való irányításához.
2. Használja fel az erőforrás részletes összegzését a Application Gateway munkafüzet-sablonjának használatával.

Ha a diagnosztikai naplók nincsenek engedélyezve a Application Gateway esetében, csak az alapértelmezett metrikai adatok lesznek kitöltve a munkafüzetben.


> [!NOTE]
> Január 2017-án az Application Gateway és a hálózati biztonsági csoportok naplóinak az Log Analytics munkaterületre való küldésének támogatott módja megváltozott. Ha az **Azure Networking Analytics (elavult)** megoldást látja, tekintse meg az [áttelepítés a régi hálózatkezelési elemzési megoldásból](#migrating-from-the-old-networking-analytics-solution) című témakört a követendő lépésekhez.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Az Azure Networking-adatok gyűjtésével kapcsolatos részletek áttekintése
Az Azure Application Gateway Analytics és a hálózati biztonsági csoport Analytics-kezelési megoldásai diagnosztikai naplókat gyűjtenek közvetlenül az Azure Application Gateway és a hálózati biztonsági csoportokból. A naplókat nem szükséges az Azure Blob Storage-ba írni, és nem szükséges ügynök az adatgyűjtés során.

Az alábbi táblázat az adatok gyűjtési módszereit és az Azure Application Gateway Analytics és a hálózati biztonsági csoport elemzésének az adatainak gyűjtésével kapcsolatos egyéb részleteket mutatja be.

| Platform | Közvetlen ügynök | System Center Operations Manager Agent | Azure | Operations Manager kötelező? | A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |naplózott |


### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Az Azure Application Gateway Diagnostics engedélyezése a portálon

1. A Azure Portal navigáljon a figyelni kívánt Application Gateway erőforráshoz.
2. Válassza a *diagnosztikai beállítások* elemet a következő oldal megnyitásához.

   ![A Application Gateway erőforrás diagnosztikai beállításainak konfigurációjának képernyőképe.](media/azure-networking-analytics/diagnostic-settings-1.png)

   [![A diagnosztikai beállítások konfigurálására szolgáló lap képernyőképe.](media/azure-networking-analytics/diagnostic-settings-2.png)](media/azure-networking-analytics/application-gateway-diagnostics-2.png#lightbox)

5. Kattintson a *küldés log Analyticsre* jelölőnégyzetre.
6. Válasszon ki egy meglévő Log Analytics munkaterületet, vagy hozzon létre egy munkaterületet.
7. A begyűjteni kívánt naplózási típusoknál kattintson a jelölőnégyzetre a **log (napló** ) területen.
8. A *Mentés* gombra kattintva engedélyezheti a diagnosztika naplózását Azure monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Az Azure hálózati diagnosztika engedélyezése a PowerShell használatával

A következő PowerShell-parancsfájl egy példát mutat be az Application Gateway erőforrás-naplózásának engedélyezésére.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

#### <a name="accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights"></a>Az Azure Application Gateway Analytics elérése Azure Monitor hálózati elemzések segítségével

Az Application ininsights a Application Gateway-erőforráson belüli adatellenőrzések lapon keresztül érhető el.

![Képernyőkép a Application Gateway-bepillantásokról ](media/azure-networking-analytics/azure-appgw-insights.png
)

A "részletes mérőszámok megtekintése" lapon megnyílik az előre feltöltött munkafüzet, amely összefoglalja az adatokat a Application Gateway.

[![Képernyőkép Application Gateway munkafüzetről](media/azure-networking-analytics/azure-appgw-workbook.png)](media/azure-networking-analytics/application-gateway-workbook.png#lightbox)

### <a name="new-capabilities-with-azure-monitor-network-insights-workbook"></a>Új képességek Azure Monitor hálózati adatáttekintési munkafüzettel

> [!NOTE]
> Azure Monitor bepillantást nem tartalmazó munkafüzethez nem jár további költségek. Log Analytics munkaterület továbbra is használati díjként lesz számlázva.

A hálózati áttekintési munkafüzet lehetővé teszi a Azure Monitor és Log Analytics legújabb képességeinek kihasználását, beleértve a következőket:

* Központosított konzol figyeléshez és hibaelhárításhoz mind a [metrikai](../insights/network-insights-overview.md#resource-health-and-metrics) , mind a naplózási adatokkal.

* Rugalmas vászon az egyéni Rich [vizualizációk](../visualize/workbooks-overview.md#visualizations)létrehozásának támogatásához.

* A [munkafüzet-sablonok](../visualize/workbooks-overview.md#workbooks-versus-workbook-templates) szélesebb közösséggel való felhasználása és megosztása.

Az új munkafüzet-megoldás képességeivel kapcsolatos további információkért tekintse meg a [munkafüzetek – áttekintés](../visualize/workbooks-overview.md)

## <a name="migrating-from-azure-gateway-analytics-solution-to-azure-monitor-workbooks"></a>Migrálás az Azure Gateway Analytics megoldásból Azure Monitor munkafüzetekbe

> [!NOTE]
> A Application Gateway erőforrásaihoz a metrika és a log Analytics eléréséhez ajánlott megoldás Azure Monitor hálózati elemzési munkafüzet.

1. Győződjön meg arról, hogy a [diagnosztikai beállítások engedélyezve vannak](#enable-azure-application-gateway-diagnostics-in-the-portal) a naplók log Analytics munkaterületre való tárolásához. Ha már be van állítva, Azure Monitor hálózati elemzések munkafüzete ugyanazon a helyen fogja tudni használni az adatok felhasználását, és nincs szükség további módosításokra.

> [!NOTE]
> Az összes korábbi adatok már elérhetők a munkafüzeten belül a pont diagnosztikai beállításainál eredetileg engedélyezve lettek. Nincs szükség adatátvitelre.

2. A Application Gateway-erőforráshoz tartozó [alapértelmezett adatáttekintési munkafüzet](#accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights) elérése. A Application Gateway Analytics-megoldás által támogatott összes meglévő elemzés már jelen lesz a munkafüzetben. Ezt kiterjesztheti olyan egyéni [vizualizációk](../visualize/workbooks-overview.md#visualizations) hozzáadásával, amelyek metrikai & naplózási adatokon alapulnak.

3. Miután megtekintheti az összes mérőszámot és naplózási információt, hogy kiürítse az Azure Gateway Analytics megoldást a munkaterületről, törölheti a megoldást a megoldás erőforrás-oldaláról.

[![Képernyőkép az Azure Application Gateway Analytics megoldás törlési lehetőségéről.](media/azure-networking-analytics/azure-appgw-analytics-delete.png)](media/azure-networking-analytics/application-gateway-analytics-delete.png#lightbox)

## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Azure hálózati biztonsági csoport elemzési megoldása Azure Monitor

![Azure Network Security Group Analytics szimbólum](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> A hálózati biztonsági csoport elemzési megoldása a közösségi támogatásra vált, mivel a funkcióját [Traffic Analytics](../../network-watcher/traffic-analytics.md)váltotta fel.
> - A megoldás mostantól elérhető az [Azure Gyorsindítás sablonjaiban](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) , és hamarosan nem lesz elérhető az Azure piactéren.
> - Azok a meglévő ügyfelek, akik már felvettek a megoldást a munkaterületre, továbbra is a módosítások nélkül fognak működni.
> - A Microsoft továbbra is támogatja a NSG-erőforrás naplófájljainak a munkaterületre való küldését a diagnosztikai beállítások használatával.

Hálózati biztonsági csoportok esetén a következő naplók támogatottak:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
Az Azure Networking Analytics megoldás telepítéséhez és konfigurálásához kövesse az alábbi utasításokat:

1. Engedélyezze az Azure hálózati biztonsági csoport analitikai megoldását a [Solutions Gallery Azure monitor-megoldások hozzáadása](./solutions.md)című témakörben ismertetett eljárással.
2. Engedélyezze a diagnosztikai naplózást a figyelni kívánt [hálózati biztonsági csoport](../../virtual-network/virtual-network-nsg-manage-log.md) erőforrásaihoz.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Az Azure hálózati biztonsági csoport diagnosztika engedélyezése a portálon

1. A Azure Portal navigáljon a hálózati biztonsági csoport erőforrásához a figyeléshez.
2. Válassza ki a *diagnosztikai naplókat* a következő oldal megnyitásához

   ![Képernyőfelvétel a hálózati biztonsági csoport erőforrásaihoz tartozó diagnosztikai naplók lapról, amely a diagnosztika bekapcsolásának lehetőségét mutatja.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Kattintson a *diagnosztika bekapcsolása* elemre a következő oldal megnyitásához

   ![A diagnosztikai beállítások konfigurálására szolgáló lap képernyőképe. Az állapot be értékre van állítva, a Küldés Log Analytics van kiválasztva, és két naplózási típus van kiválasztva.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. A diagnosztika bekapcsolásához kattintson *az* *állapot* elemre.
5. Kattintson a *küldés log Analyticsre* jelölőnégyzetre.
6. Válasszon ki egy meglévő Log Analytics munkaterületet, vagy hozzon létre egy munkaterületet
7. A begyűjteni kívánt naplózási típusok esetében kattintson a jelölőnégyzetre a **napló** alatt
8. Kattintson a *Save (Mentés* ) gombra a diagnosztika naplózásának engedélyezéséhez log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Az Azure hálózati diagnosztika engedélyezése a PowerShell használatával

A következő PowerShell-parancsfájl egy példát mutat be a hálózati biztonsági csoportok erőforrás-naplózásának engedélyezésére.
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Az Azure hálózati biztonsági csoport elemzésének használata
Miután rákattintott az **Azure hálózati biztonsági csoport Analytics** csempére az áttekintésben, megtekintheti a naplók összefoglalóit, és a következő kategóriák részleteit részletezheti:

* Hálózati biztonsági csoport letiltott folyamatai
  * Hálózati biztonsági csoport szabályai letiltott folyamatokkal
  * MAC-címek letiltott folyamatokkal
* Hálózati biztonsági csoport engedélyezett forgalma
  * Hálózati biztonsági csoport szabályai engedélyezett folyamatokkal
  * MAC-címek engedélyezett folyamatokkal

![Képernyőfelvétel a hálózati biztonsági csoport letiltott folyamatainak adatait tartalmazó csempéről, beleértve a letiltott folyamatokkal és a blokkolt folyamatokkal rendelkező MAC-címekkel kapcsolatos szabályokat.](media/azure-networking-analytics/log-analytics-nsg01.png)

![Képernyőkép a hálózati biztonsági csoport által engedélyezett folyamatokkal rendelkező csempéről, beleértve az engedélyezett folyamatokkal rendelkező, engedélyezett folyamatokkal és MAC-címekkel rendelkező szabályokat is.](media/azure-networking-analytics/log-analytics-nsg02.png)

Az **Azure hálózati biztonsági csoport** elemzése irányítópulton tekintse át az összegzési információkat az egyik pengében, majd kattintson az egyikre a részletes információk megtekintéséhez a naplóbeli keresés oldalon.

Bármelyik naplóbeli keresési oldalon megtekintheti az eredményeket idő szerint, részletes eredményekkel és a naplóbeli keresési előzményekkel. Az eredmények szűkítéséhez az aspektusok alapján is szűrheti az eredményeket.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrálás a régi hálózati elemzési megoldásból
Január 2017-án az Azure Application Gateway és az Azure hálózati biztonsági csoportok naplófájljainak a Log Analytics munkaterületre való küldésének támogatott módja megváltozott. Ezek a változások a következő előnyöket nyújtják:
+ A naplók közvetlenül a Azure Monitorba vannak írva, anélkül, hogy Storage-fiókot kellene használni
+ Kevesebb késés attól az időponttól kezdve, amikor a naplók generálva lesznek Azure Monitor
+ Kevesebb konfigurációs lépés
+ Az Azure Diagnostics összes típusának általános formátuma

A frissített megoldások használata:

1. [A diagnosztika konfigurálása, hogy közvetlenül az Azure Application Gateway-Azure Monitor legyen elküldve](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [A diagnosztika konfigurálása az Azure-beli hálózati biztonsági csoportokból Azure Monitor közvetlenül küldendő](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. A *Azure Application Gateway Analytics* és a *Azure Network Security Group Analytics* megoldás engedélyezéséhez használja az [Azure Monitor-megoldások hozzáadása a Solutions Galleryből](solutions.md) című témakörben leírt eljárást.
3. A mentett lekérdezések, irányítópultok vagy riasztások frissítése az új adattípus használatára
   + A típus a következő: AzureDiagnostics. A ResourceType használatával szűrheti az Azure hálózati naplóit.

     | ahelyett, hogy: | Használja |
     | --- | --- |
     | NetworkApplicationgateways &#124;, ahol OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124;, ahol ResourceType = = "APPLICATIONGATEWAYS" és OperationName = = "ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124;, ahol OperationName = = "ApplicationGatewayPerformance" | AzureDiagnostics &#124;, ahol ResourceType = = "APPLICATIONGATEWAYS" és OperationName = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124;, ahol ResourceType = = "NETWORKSECURITYGROUPS" |

   + Minden olyan mezőnél, amely \_ \_ a névben s, d vagy g utótaggal rendelkezik \_ , módosítsa az első karaktert a kisbetű értékre.
   + Minden olyan mezőnél, amelynél a \_ név utótagja szerepel, az adat a beágyazott mezők nevei alapján egyedi mezőkbe van bontva.
4. Távolítsa el az *Azure Networking Analytics (elavult)* megoldást.
   + Ha a PowerShellt használja, használja a `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

A módosítás előtt gyűjtött adatok nem láthatók az új megoldásban. Továbbra is lekérdezheti ezeket az adattípusokat a régi típusú és mezőnevek használatával.

## <a name="troubleshooting"></a>Hibaelhárítás
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Következő lépések
* Az Azure-diagnosztika részletes adatait [a Azure monitorban található naplók](../logs/log-query-overview.md) használatával tekintheti meg.

