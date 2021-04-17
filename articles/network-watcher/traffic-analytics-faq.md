---
title: Azure Traffic Analytics – gyakori kérdések | Microsoft Docs
description: Választ kaphat a Traffic Analytics szolgáltatással kapcsolatos leggyakoribb kérdésekre.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: 98c0a6f88da717256e78a748902317a90a369a9c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533628"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Traffic Analytics gyakori kérdések

Ez a cikk egy helyen gyűjti össze az Azure-beli forgalomelemzéssel kapcsolatos gyakori kérdéseket Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>Mik a Traffic Analytics használatának előfeltételei?

Traffic Analytics előfeltételek a következők:

- Egy Network Watcher előfizetés.
- A figyelni kívánt NSG-k számára engedélyezett hálózati biztonsági csoportok (NSG-k) folyamatnaplói.
- Egy Azure Storage-fiók a nyers folyamatnaplók tárolásához.
- Egy Azure Log Analytics-munkaterület olvasási és írási hozzáféréssel.

A Forgalomelemzés engedélyezéséhez a fiókjának meg kell felelnie a következők egyikének:

- A fióknak az alábbi Azure-szerepkörök valamelyikével kell lennie az előfizetés hatókörében: tulajdonos, közreműködő, olvasó vagy hálózati közreműködő.
- Ha a fiókja nincs hozzárendelve a korábban felsorolt szerepkörök egyikéhez sem, akkor olyan egyéni szerepkörhöz kell hozzárendelni, amely az alábbi műveleteket rendeli hozzá az előfizetés szintjén.
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
Egy előfizetés felhasználóhoz rendelt szerepkörének ellenőrzése:

1. Jelentkezzen be az Azure-ba a **Login-AzAccount használatával.** 

2. Válassza ki a szükséges előfizetést a **Select-AzSubscription használatával.** 

3. Az adott felhasználóhoz rendelt összes szerepkör listázható a **Get-AzRoleAssignment -SignInName [felhasználói e-mail-cím] -IncludeClassicAdministrators használatával.** 

Ha nem lát kimenetet, lépjen kapcsolatba a megfelelő előfizetés-rendszergazdával, hogy hozzáférést kap a parancsok futtatásához. További részletekért lásd: [Azure-beli](../role-based-access-control/role-assignments-powershell.md)szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure PowerShell.

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>Az NSG-k, amelyekhez engedélyezem a folyamatnaplókat, eltérő régiókban lehet, mint a munkaterületem?

Igen, ezek az NSG-k eltérő régiókban is lehetnek, mint a Log Analytics-munkaterület.

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>Egyetlen munkaterületen belül több NSG is konfigurálható?

Igen.

## <a name="can-i-use-an-existing-workspace"></a>Használhatok meglévő munkaterületet?

Igen. Ha egy meglévő munkaterületet választ ki, győződjön meg arról, hogy az át lett telepítve az új lekérdezési nyelvre. Ha nem szeretné frissíteni a munkaterületet, létre kell hoznia egy újat. Az új lekérdezési nyelvvel kapcsolatos további információkért lásd a Azure Monitor [naplók új naplókeresésre való frissítését.](../azure-monitor/logs/log-query-overview.md)

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>Az Azure Storage-fiókom lehet egy előfizetésben, a Log Analytics-munkaterületem pedig egy másik előfizetésben?

Igen, az Azure Storage-fiók lehet egy előfizetésben, a Log Analytics-munkaterület pedig egy másik előfizetésben.

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>Tárolok nyers naplókat egy másik előfizetésben?

Igen. Beállíthatja, hogy az NSG-forgalom naplói egy másik előfizetésben található tárfiókba lesznek elküldve, feltéve, hogy rendelkezik a megfelelő jogosultságokkal, és hogy a tárfiók ugyanabban a régióban található, mint az NSG. Az NSG-nek és a cél tárfióknak is ugyanazt a bérlőt Azure Active Directory meg.

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>Mi történik, ha nem tudok NSG-t konfigurálni a Traffic Analyticshez egy "Nem található" hiba miatt?

Válasszon ki egy támogatott régiót. Ha nem támogatott régiót választ, a "Nem található" hibaüzenet jelenik meg. A támogatott régiók listája a cikk korábbi, listában szerepel.

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>Mi a helyzet, ha az NSG-forgalom naplói oldalon a "Sikertelen betöltés" állapotot kapom?

Ahhoz, hogy a folyamatnaplózás megfelelően működjön, regisztrálni kell a Microsoft.Insights szolgáltatót. Ha nem biztos abban, hogy a Microsoft.Insights szolgáltató regisztrálva van-e az előfizetéséhez, cserélje le a *xxxxx-xxxxx-xxxxxx-xxxxx* helyére a következő parancsot, és futtassa a következő parancsokat a PowerShellből:

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>Konfiguráltam a megoldást. Miért nem látok semmit az irányítópulton?

Az irányítópult első megjelenése akár 30 percet is igénybe vehet. A megoldásnak először elegendő adatot kell összesítenie ahhoz, hogy értelmes elemzéseket nyerhető legyen. Ezután jelentéseket generál. 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>Mi a következő üzenet jelenik meg: "Nem található adat ezen a munkaterületen a kiválasztott időintervallumban. Próbálja meg megváltoztatni az időintervallumot, vagy válasszon másik munkaterületet."?

Próbálja ki a következő lehetőségeket:
- Módosítsa az időintervallumot a felső sávon.
- Válasszon egy másik Log Analytics-munkaterületet a felső sávon.
- Próbálja meg 30 perc után elérni a Forgalomelemzést, ha a közelmúltban engedélyezték.
    
Ha a problémák továbbra is fennállnak, problémákat vethet fel a [Felhasználói hang fórumon.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>Mi történik, ha ezt az üzenetet kapom: "Az NSG-forgalom naplóinak elemzése első alkalommal. A folyamat 20–30 percet is igénybe vehet. Egy idő után vissza kell néznie. 2) Ha a fenti lépés nem működik, és a munkaterülete az ingyenes termékváltozat alatt van, akkor ellenőrizze a munkaterület használatát itt a kvóta szerinti ellenőrzéshez, egyéb információért tekintse meg a gyakori kérdések között."

Ez az üzenet a következő miatt jelenik meg:
- Traffic Analytics nemrégiben engedélyezték, és előfordulhat, hogy még nem összesítve lett elegendő adat ahhoz, hogy értelmes elemzéseket nyerhető legyen.
- A Log Analytics-munkaterület ingyenes verzióját használja, és az túllépte a kvótakorlátokat. Előfordulhat, hogy nagyobb kapacitású munkaterületet kell használnia.
    
Ha a problémák továbbra is fennállnak, problémákat vethet fel [a Felhasználói hang fórumon.](https://feedback.azure.com/forums/217313-networking?category_id=195844)
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>Mi történik, ha ezt az üzenetet kapom: "Úgy tűnik, hogy vannak erőforrás-adatok (topológia), és nincsenek folyamatinformációk. Addig is kattintson ide az erőforrásadatokért, és tekintse meg a gyakori kérdések között a további információkat."

Az erőforrásokra vonatkozó információk adatokat látnak az irányítópulton; folyamatokkal kapcsolatos statisztikák azonban nincsenek. Előfordulhat, hogy az adatok azért nincsenek jelen, mert nincs kommunikációs folyamat az erőforrások között. Várjon 60 percet, és ellenőrizze újra az állapotot. Ha a probléma továbbra is fennáll, és biztos benne, hogy az erőforrások közötti kommunikáció létezik, jelentsen problémát a Felhasználói hang [fórumon.](https://feedback.azure.com/forums/217313-networking?category_id=195844)

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>Konfigurálható a forgalomelemzés a PowerShell vagy egy Azure Resource Manager sablon vagy ügyfél használatával?

A 6.2.1-es vagy újabb Windows PowerShell forgalomelemzés konfigurálható. Ha a Set parancsmaggal konfigurálja a folyamatnaplózást és a forgalomelemzést egy adott NSG-hez, tekintse meg a [Set-AzNetworkWatcherConfigFlowLog parancsmagot.](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) Egy adott NSG folyamatnaplózási és forgalomelemzési állapotának lekért lásd: [Get-AzNetworkWatcherFlowLogStatus.](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus)

Jelenleg nem használhat forgalomelemzési Azure Resource Manager sablont.

A forgalomelemzés ügyfélprogrammal való konfigurálás Azure Resource Manager tekintse meg az alábbi példákat.

**Példa a parancsmagok beállításra:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Példa lekért parancsmagra:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Hogyan lesz Traffic Analytics ára?

Traffic Analytics forgalmi díjas. A mérés a folyamatnapló-adatok szolgáltatás által történő feldolgozásán és az eredményül kapott bővített naplók Log Analytics-munkaterületen való tárolásán alapul. 

Például a díjszabási [](https://azure.microsoft.com/pricing/details/network-watcher/)csomagnak megfelelően, az USA nyugati középső régióját figyelembe véve, ha a flow-naplók az Traffic Analytics által feldolgozott tárfiókban tárolt adatokat 10 GB- és a Log Analytics-munkaterületen betöltött bővített naplókat tartalmaznak, a vonatkozó díjak a következőek: 10 x 2,3$ + 1 x 2,76$ = 25,76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Milyen gyakran Traffic Analytics adatokat?

Tekintse meg a séma- és [adatösszesítő](./traffic-analytics-schema.md#data-aggregation) dokumentum Traffic Analytics szakaszát

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Hogyan dönti Traffic Analytics, hogy egy IP-cím kártékony? 

Traffic Analytics microsoftos belső fenyegetésintelligencia-rendszerekre támaszkodik az IP-címek rosszindulatúként való tekinthetők. Ezek a rendszerek különféle telemetriaforrásokat, például Microsoft-termékeket és -szolgáltatásokat, a Microsoft Digital Crimes Unitot (DCU), az Microsoft Biztonsági reagálási központ-t (MSRC) és külső csatornákat vesznek igénybe, és rengeteg intelligenciát építenek ki. Ezen adatok némelyike a Microsoft belső szolgáltatása. Ha egy ismert IP-címet rosszindulatúként megjelöltek, a részletekért nyújtsa be a támogatási jegyet.

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Hogyan állíthatok be riasztásokat a Traffic Analytics adataihoz?

Traffic Analytics nem támogatja a riasztások beépített támogatását. Mivel azonban Traffic Analytics adatok a Log Analyticsben vannak tárolva, egyéni lekérdezéseket írhat, és riasztásokat állíthat be rájuk. Lépéseket:
- A Log Analytics rövid hivatkozását használhatja a Traffic Analytics. 
- Lekérdezések [írása az](traffic-analytics-schema.md) itt dokumentált sémával 
- Kattintson az "Új riasztási szabály" elemre a riasztás létrehozásához
- A riasztás [létrehozásához tekintse](../azure-monitor/alerts/alerts-log.md) meg a naplóriasztás dokumentációját

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>Hogyan ellenőrizni, hogy mely virtuális gépek kapják a legtöbb helyszíni forgalmat?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
| render timechart
```

  IP-k:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
| render timechart
```

Az időponthoz használja a következő formátumot: yyyy-mm-dd 00:00:00

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>Hogyan a virtuális gépek által a helyszíni gépektől fogadott forgalom szórását?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

IP-k:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>Hogyan ellenőrizni, hogy mely portok érjék el (vagy blokkolják) az NSG-szabályokkal párosított IP-párokat?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
| extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
| mvexpand SourceIp = sourceIPs to typeof(string)
| mvexpand DestIp = destIPs to typeof(string)
| project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
| summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s
```

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>Hogyan navigálok a billentyűzettel a geotérkép nézetben?

A geotérkép oldal két fő szakaszt tartalmaz:
    
- **Szalagcím:** A geotérkép tetején található szalagcím gombokat tartalmaz a forgalomelosztási szűrők kiválasztásához (például Üzembe helyezés, Országok/régiókból származó forgalom és Kártékony). Amikor kiválaszt egy gombot, a rendszer a megfelelő szűrőt alkalmazza a térképen. Ha például az Aktív gombot választja, a térkép kiemeli az üzemelő példányban lévő aktív adatközpontokat.
- **Térkép:** A szalagcím alatt a térkép szakasz az Azure-adatközpontok és országok/régiók közötti forgalomelosztást mutatja.
    
### <a name="keyboard-navigation-on-the-banner"></a>Billentyűzettel való navigálás a szalagcímen
    
- Alapértelmezés szerint a szalagcím geotérkép oldalán az "Azure-beli jelölő" szűrő van kijelölve.
- Egy másik szűrőre való ugráshoz használja a vagy a `Tab` `Right arrow` kulcsot. A visszalépéshez használja a vagy a `Shift+Tab` `Left arrow` kulcsot. Az előre navigáció balról jobbra, majd felülről lefelé halad.
- A kiválasztott szűrő alkalmazáshoz nyomja le a vagy `Enter` a `Down` nyílbillentyűt. A szűrési kiválasztás és az üzembe helyezés alapján a térkép szakasz alatt egy vagy több csomópont van kiemelve.
- A szalagcím és a térkép közötti váltáshoz nyomja le a `Ctrl+F6` billentyűt.
        
### <a name="keyboard-navigation-on-the-map"></a>Billentyűzetnavigációs térkép
    
- Miután kiválasztotta valamelyik szűrőt a szalagcímen, és lenyomta a gombot, a fókusz a térképnézet egyik kiemelt csomópontjára `Ctrl+F6` **(Azure-adatközpont** vagy **Ország/régió)** kerül.
- A térképen lévő többi kiemelt csomópontra való áthelyezéshez használja a vagy a kulcsot `Tab` `Right arrow` a továbbításhoz. Használja `Shift+Tab` a vagy a kulcsot a `Left arrow` visszamenőleges mozgáshoz.
- A térképen kiemelt csomópontok kiválasztásához használja a vagy a `Enter` `Down arrow` billentyűt.
- Az ilyen csomópontok kiválasztása esetén a fókusz a csomópont **információs** eszközkészletében van. Alapértelmezés szerint a fókusz az Information Tool Box zárt **gombjára kerül.** A Box nézetben a és a billentyűvel haladhat előre és  `Right arrow` `Left arrow` hátra. A billentyű lenyomása ugyanazzal a hatással bír, mint `Enter` az Information Tool Box **fókuszban lévő gombjának kiválasztása.**
- Ha megnyomja a billentyűt, miközben a fókusz az Információs eszközkészleten van, a fókusz a kijelölt csomóponttal azonos kontinensen található `Tab` végpontok felé kerül. A `Right arrow` végpontok között a és `Left arrow` a billentyűvel haladhat végig.
- Más folyamatvégpontra vagy kontinensfürtre való áthelyezéshez használja a et az előre és a `Tab` `Shift+Tab` visszafelé történő mozgáshoz.
- Ha a fókusz a kontinensfürtökön van, a vagy a nyílbillentyűkkel emelheti ki a kontinensfürtön `Enter` belüli `Down` végpontokat. A végpontok és a kontinensfürt információs dobozán található bezárás gombbal a vagy a billentyűvel haladhat `Right arrow` `Left arrow` előre és hátra. A használatával bármely végponton átválthat a kapcsolati vonalra a kiválasztott `Shift+L` csomópontról a végpontra. A kiválasztott `Shift+L` végpontra való áthelyezéshez nyomja le újra a billentyűt.
        
### <a name="keyboard-navigation-at-any-stage"></a>Billentyűzetnavigációs folyamat bármely fázisban
    
- `Esc` összecsukja a kibontott kijelölést.
- A `Up-arrow` kulcs ugyanazt a műveletet végzi el, mint a `Esc` . A `Down arrow` kulcs ugyanazt a műveletet végzi el, mint a `Enter` .
- A `Shift+Plus` nagyításhoz és `Shift+Minus` kicsinyítéshez használható.

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>Hogyan navigálok a billentyűzettel a virtuális hálózati topológia nézetben?

A virtuális hálózatok topológiája oldal két fő szakaszt tartalmaz:
    
- **Szalagcím:** A virtuális hálózatok topológiája tetején található szalagcím gombokkal választja ki a forgalomelosztási szűrőket (például csatlakoztatott virtuális hálózatokat, leválasztott virtuális hálózatokat és nyilvános IP-címeket). Amikor kiválaszt egy gombot, a rendszer a megfelelő szűrőt alkalmazza a topológiára. Ha például az Aktív gombot választja, a topológia kiemeli az üzemelő példány aktív virtuális hálózatait.
- **Topológia:** A szalagcím alatt a topológia szakasz a virtuális hálózatok közötti forgalomelosztást mutatja be.
    
### <a name="keyboard-navigation-on-the-banner"></a>Billentyűzettel való navigálás a szalagcímen
    
- Alapértelmezés szerint a szalagcím virtuális hálózati topológia lapján a "Csatlakoztatott virtuális hálózatok" szűrő van kijelölve.
- Egy másik szűrőre való ugráshoz használja `Tab` a kulcsot a továbblépéshez. A visszalépéshez használja a `Shift+Tab` kulcsot. Az előre navigáció balról jobbra, majd felülről lefelé halad.
- A kiválasztott `Enter` szűrő alkalmazáshoz nyomja le a billentyűt. A szűrési kijelölés és az üzembe helyezés alapján a topológia szakaszban egy vagy több csomópont (virtuális hálózat) van kiemelve.
- A szalagcím és a topológia közötti váltáshoz nyomja le a `Ctrl+F6` billentyűt.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűzettel való navigálás a topológiában
    
- Miután kiválasztotta valamelyik szűrőt a szalagcímen, és lenyomta a gombot, a fókusz a topológia nézet egyik kiemelt csomópontjára `Ctrl+F6` **(VNet)** kerül.
- A topológianézetben lévő többi kiemelt csomópontra való ugráshoz használja a kulcsot `Shift+Right arrow` a továbbításhoz. 
- A kiemelt csomópontokon a fókusz a csomópont **információs eszközkészletében** jelenik meg. Alapértelmezés szerint a fókusz az Information Tool Box További **részletek** **gombjára kerül.** A Box nézetben **a** és a billentyűvel haladhat előre `Right arrow` és `Left arrow` hátra. A billentyű lenyomása ugyanazzal a hatással bír, mint `Enter` az Information Tool Box **fókuszban lévő gombjának kiválasztása.**
- Az ilyen csomópontok kiválasztása után a billentyű lenyomásával felkeresheti az összes kapcsolatát, és csak `Shift+Left arrow` egyszer. A Fókusz a kapcsolat **Információs eszközkészletét** használja. A fókuszt bármikor vissza lehet tolni a csomópontra az `Shift+Right arrow` újranyomással.
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>Hogyan navigálok a billentyűzettel az alhálózati topológia nézetben?

A virtuális alhálózatok topológiája oldal két fő szakaszt tartalmaz:
    
- **Szalagcím:** A virtuális alhálózatok topológiája tetején található szalagcím gombokat biztosít a forgalomelosztási szűrők (például aktív, közepes és átjáró-alhálózatok) kiválasztásához. Amikor kiválaszt egy gombot, a rendszer a megfelelő szűrőt alkalmazza a topológiára. Ha például az Aktív gombot választja, a topológia kiemeli az üzemelő példány aktív virtuális alhálózatát.
- **Topológia:** A szalagcím alatt a topológia szakasz a virtuális alhálózatok közötti forgalomelosztást mutatja.
    
### <a name="keyboard-navigation-on-the-banner"></a>Billentyűzettel való navigálás a szalagcímen
    
- Alapértelmezés szerint a szalagcím virtuális alhálózatok topológiáját jelölő oldalon az "Alhálózatok" szűrő van kijelölve.
- Egy másik szűrőre való ugráshoz használja `Tab` a kulcsot a továbblépéshez. A visszalépéshez használja a `Shift+Tab` kulcsot. A továbbítási navigáció balról jobbra, majd felülről lefelé halad.
- A `Enter` kijelölt szűrő alkalmazáshoz nyomja le a billentyűt. A szűrő kiválasztása és üzembe helyezése alapján a topológia szakaszban egy vagy több csomópont (alhálózat) van kiemelve.
- A szalagcím és a topológia közötti váltáshoz nyomja le az `Ctrl+F6` billentyűt.
        
### <a name="keyboard-navigation-on-the-topology"></a>Billentyűzettel való navigálás a topológiában
    
- Miután kiválasztotta valamelyik szűrőt a szalagcímen, és lenyomta a gombot, a fókusz a topológia nézet egyik kiemelt csomópontjára `Ctrl+F6` **(Alhálózat)** kerül.
- A topológianézetben lévő többi kiemelt csomópontra való ugráshoz használja a kulcsot `Shift+Right arrow` a továbbításhoz. 
- A kiemelt csomópontokon a fókusz a csomópont **Információs eszközkészletében** jelenik meg. Alapértelmezés szerint a fókusz az Information Tool Box További **részletek** **gombjára kerül.** A Box nézetben a és a billentyűvel haladhat előre és  `Right arrow` `Left arrow` hátra. A `Enter` billentyű lenyomása ugyanazzal a hatással bír, mint az Information Tool Box **fókuszban lévő gombjának kiválasztása.**
- Az ilyen csomópontok kiválasztása után a billentyű lenyomásával az összes kapcsolatot külön-külön is `Shift+Left arrow` felkeresheti. A Fókusz a kapcsolat **Információs eszközkészletét** használja. A fókuszt bármikor vissza lehet tolni a csomópontra az `Shift+Right arrow` újranyomással.

## <a name="are-classic-nsgs-supported"></a>Támogatottak a klasszikus NSG-k?
Nem, Traffic Analytics nem támogatja a klasszikus NSG-t. Javasoljuk, hogy az IaaS-erőforrásokat klasszikusról Azure Resource Manager, mivel a klasszikus erőforrások [elavultak lesznek.](../virtual-machines/classic-vm-deprecation.md) A áttelepítésének mikéntjért tekintse meg [ezt a cikket.](../virtual-machines/migration-classic-resource-manager-overview.md)