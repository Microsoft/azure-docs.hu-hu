---
title: Azure Traffic Analytics-séma | Microsoft Docs
description: A Traffic Analytics sémájának megismerése az Azure hálózati biztonsági csoport folyamatábráinak elemzéséhez.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 765a2728c5de49787a64fff723625e53e100e450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017782"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Sémák és adatösszesítések Traffic Analytics

Traffic Analytics egy felhőalapú megoldás, amely láthatóságot biztosít a felhasználói és alkalmazási tevékenységeknek a felhőalapú hálózatokban. Traffic Analytics elemzi Network Watcher hálózati biztonsági csoport (NSG) folyamatábráit, hogy betekintést nyújtson az Azure-felhőbe irányuló forgalomba. A Traffic Analytics segítségével a következőket teheti:

- Megjelenítheti a hálózati tevékenységeket az Azure-előfizetések között, és azonosíthatja a gyors helyeket.
- Azonosítsa a biztonsági fenyegetéseket, és gondoskodjon a hálózat biztonságáról, olyan információkkal, mint a nyílt portok, az internet-hozzáférést megkísérlő alkalmazások és a virtuális gépek (VM) a támadó hálózatokhoz való csatlakozáskor.
- Ismerje meg az Azure-régiók és az Internet forgalmi forgalmának mintáit a hálózati üzembe helyezés teljesítményének és kapacitásának optimalizálása érdekében.
- A hálózat hibás kapcsolataihoz vezető hálózati helytelen konfigurációkat.
- Hálózati használat a bájtokban, a csomagokban vagy a folyamatokban.

### <a name="data-aggregation"></a>Adatösszesítés

1. A (z) "FlowIntervalStartTime_t" és "FlowIntervalEndTime_t" közötti NSG lévő összes flow-naplót a rendszer a Storage-fiókban lévő, a Traffic Analytics általi feldolgozás előtt egy perces időközönként rögzíti.
2. Traffic Analytics alapértelmezett feldolgozási időköze 60 perc. Ez azt jelenti, hogy minden 60 perc Traffic Analytics a blobokat a tárolóból az összesítéshez. Ha a kiválasztott feldolgozási intervallum 10 perc, Traffic Analytics minden 10 percen belül kiválaszthatja a Storage-fiókból a blobokat.
3. Azonos forrás IP-címmel, célként megadott IP-címmel, Célportmal, NSG-névvel, NSG szabállyal, flow irányával és átviteli réteg protokollal (TCP vagy UDP) rendelkező folyamatok (Megjegyzés: a forrásport ki van zárva az összesítéshez) Traffic Analytics
4. Ezt az egyetlen rekordot rendezi a rendszer (részletek az alábbi szakaszban), és Traffic Analytics Log Analytics betöltötte. Ez a folyamat legfeljebb 1 órát vehet igénybe.
5. FlowStartTime_t mező jelzi, hogy az ilyen aggregált folyamat első előfordulása (ugyanaz a négy rekord) a folyamat naplójának feldolgozási intervallumában, a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" között.
6. A TA-ban lévő erőforrások esetében a felhasználói felületen jelzett folyamatok a NSG által látott összes folyamat, de Log Analytics felhasználó csak az egyetlen, kisebb rekordot fogja látni. Az összes folyamat megjelenítéséhez használja a blob_id mezőt, amely hivatkozhat a tárolóból. A rekord teljes folyamatábrája megegyezik a blobban látható egyes folyamatokkal.

Az alábbi lekérdezés segítségével megtekintheti az elmúlt 30 napban nem Azure-beli nyilvános IP-címekkel kommunikáló alhálózatokat.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
A fent említett lekérdezésben szereplő folyamatok blob elérési útjának megtekintéséhez használja az alábbi lekérdezést:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

A fenti lekérdezés egy URL-címet hoz létre a blob közvetlen eléréséhez. Az URL-cím az elhelyezési tulajdonosokkal:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Traffic Analytics sémában használt mezők
  > [!IMPORTANT]
  > A Traffic Analytics séma a 2019. augusztus 22-én frissült. Az új séma a forrás-és cél IP-címek külön eltávolítását is lehetővé teszi, hogy a lekérdezések egyszerűbben FlowDirection az elemzést. </br>
  > Az FASchemaVersion_s 1 és 2 között frissült. </br>
  > Elavult mezők: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Új mezők: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Az elavult mezők 2019 november 22-én lesznek elérhetők.

A Traffic Analytics Log Analyticsra épül, így egyéni lekérdezéseket futtathat a Traffic Analytics által díszített és a riasztásokat is beállíthatja.

Alább láthatók a séma mezői és azok

| Mező | Formátum | Megjegyzések |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Traffic Analytics-adathalmazok táblázata
| SubType_s | FlowLog | A folyamat naplófájljainak altípusa. Csak a "FlowLog", a SubType_s egyéb értékeit a termék belső működéséhez használja a rendszer. |
| FASchemaVersion_s |   2   | Séma verziója. Nem tükrözi a NSG folyamat naplójának verzióját |
| TimeProcessed_t   | Dátum és idő (UTC)  | Az az idő, amikor a Traffic Analytics feldolgozta a nyers folyamat naplóit a Storage-fiókból |
| FlowIntervalStartTime_t | Dátum és idő (UTC) |  A folyamat naplójának feldolgozási intervallumának kezdési időpontja. Ez az idő, amelyből a flow intervalluma mérhető |
| FlowIntervalEndTime_t | Dátum és idő (UTC) | A folyamat naplójának feldolgozási időközének befejezési időpontja |
| FlowStartTime_t | Dátum és idő (UTC) |  A folyamat első előfordulása (amely összesítve lesz) a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti folyamat naplójának feldolgozási intervallumában. A folyamat összesítési logika alapján összesítve lesz. |
| FlowEndTime_t | Dátum és idő (UTC) | A folyamat utolsó előfordulása (amely összesítve lesz) a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti folyamat naplójának feldolgozási intervallumában. A flow log v2 esetében ez a mező azt az időpontot tartalmazza, amikor az azonos négy rekorddal elindított utolsó folyamat (a nyers flow-rekordban "B" jelölésű). |
| FlowType_s |  * IntraVNet <br> * Virtuális hálózatok közötti <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Ismeretlen privát <br> * Ismeretlen | A táblázat alatti megjegyzések definíciója |
| SrcIP_s | Forrás IP-címe | AzurePublic-és ExternalPublic-folyamatok esetén üres lesz |
| DestIP_s | Cél IP-cím | AzurePublic-és ExternalPublic-folyamatok esetén üres lesz |
| VMIP_s | A virtuális gép IP-címe | AzurePublic-és ExternalPublic-folyamatokhoz használatos |
| PublicIP_s | Nyilvános IP-címek | AzurePublic-és ExternalPublic-folyamatokhoz használatos |
| DestPort_d | Célport | A bejövő forgalomhoz használt port |
| L4Protocol_s  | * T <br> * U  | Átviteli protokoll. T = TCP <br> U = UDP |
| L7Protocol_s  | Protokoll neve | Cél portból származtatva |
| FlowDirection_s | * I = bejövő<br> * O = kimenő | A folyamat iránya a NSG-ben/folyamatban |
| FlowStatus_s  | * A = engedélyezett NSG-szabály szerint <br> * D = NSG szabály által megtagadva  | A flow által engedélyezett/nblocked állapota NSG szerint |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | A folyamathoz társított hálózati biztonsági csoport (NSG) |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  A folyamatot engedélyező vagy megtagadó NSG-szabály |
| NSGRule_s | NSG_RULENAME |  A folyamatot engedélyező vagy megtagadó NSG-szabály |
| NSGRuleType_s | * Felhasználó által definiált * alapértelmezett |   A folyamat által használt NSG-szabály típusa |
| MACAddress_s | MAC-cím | Azon hálózati adapter MAC-címe, amelyen a folyamat rögzített |
| Subscription_s | Az Azure virtuális hálózat/hálózati adapter/virtuális gép előfizetése fel van töltve ebben a mezőben | Csak a FlowType = S2S, a P2S, a AzurePublic, a ExternalPublic, a MaliciousFlow és a UnknownPrivate flow típusokra vonatkozik (a flow típusai, ahol csak az egyik oldal az Azure) |
| Subscription1_s | Előfizetés azonosítója | A virtuális hálózat/hálózati adapter/virtuális gép előfizetés-azonosítója, amelyhez a folyamathoz tartozó forrás IP-cím tartozik |
| Subscription2_s | Előfizetés azonosítója | A virtuális hálózat/hálózati adapter/virtuális gép előfizetés-azonosítója, amelyhez a folyamat cél IP-címe tartozik |
| Region_s | A virtuális hálózat/hálózati adapter/virtuális gép Azure-régiója, amelyhez a folyamathoz tartozó IP-cím tartozik | Csak a FlowType = S2S, a P2S, a AzurePublic, a ExternalPublic, a MaliciousFlow és a UnknownPrivate flow típusokra vonatkozik (a flow típusai, ahol csak az egyik oldal az Azure) |
| Region1_s | Azure-régió | A virtuális hálózat/hálózati adapter/virtuális gép Azure-régiója, amelyhez a folyamathoz tartozó forrás IP-cím tartozik |
| Region2_s | Azure-régió | A virtuális hálózat Azure-régiója, amelyhez a folyamat cél IP-címe tartozik |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  A forgalmat küldő vagy fogadó virtuális géphez társított hálózati adapter |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | A folyamathoz tartozó forrás IP-címhez társított hálózati adapter |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | A folyamat cél IP-címéhez társított hálózati adapter |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | A hálózati adapterhez társított virtuális gép NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | A folyamathoz tartozó forrás IP-címhez társított virtuális gép |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | A folyamat cél IP-címéhez társított virtuális gép |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | A NIC_shoz társított alhálózat |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | A folyamathoz tartozó forrás IP-címhez társított alhálózat |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | A folyamat cél IP-címéhez társított alhálózat |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | A folyamat forrás IP-címéhez társított Application Gateway |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | A folyamat cél IP-címéhez társított Application Gateway |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | A folyamathoz tartozó forrás IP-címhez tartozó terheléselosztó |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | A folyamat cél IP-címéhez társított Load Balancer |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | A folyamathoz tartozó forrás IP-címhez társított helyi hálózati átjáró |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | A folyamat cél IP-címéhez társított helyi hálózati átjáró |
| ConnectionType_s | A lehetséges értékek a következők VNetPeering, átjáróban és ExpressRoute |    Kapcsolattípus |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | A kapcsolatok neve. A flowtype P2S a következő formátumban lesz formázva: <gateway name> _<VPN Client IP> |
| ConnectingVNets_s | Virtuális hálózati nevek szóközzel tagolt listája | A hub és a küllős topológia esetében itt lesznek kitöltve a hub virtuális hálózatok |
| Country_s | Két betűs országkód (ISO 3166-1 Alpha-2) | Kitöltve a flow Type ExternalPublic. PublicIPs_s mezőben lévő összes IP-cím ugyanazt az országkódot fogja megosztani. |
| AzureRegion_s | Azure-régió helyei | Kitöltve a flow Type AzurePublic. PublicIPs_s mezőben lévő összes IP-cím az Azure-régiót fogja megosztani |
| AllowedInFlows_d | | Az engedélyezett bejövő folyamatok száma. Ez azoknak a folyamatoknak a számát jelöli, amelyek ugyanazt a négy rekordos bejövő forgalmat osztották meg a hálózati adapterhez, amelyen a folyamatot rögzítették |
| DeniedInFlows_d |  | A megtagadott bejövő folyamatok száma. (Bejövő arra a hálózati adapterre, amelyen a folyamatot rögzítették) |
| AllowedOutFlows_d | | Engedélyezett kimenő folyamatok száma (kimenő a folyamat által rögzített hálózati adapterre) |
| DeniedOutFlows_d  | | A megtagadott kimenő folyamatok száma (a folyamat által rögzített hálózati adapter felé irányuló kimenő forgalom) |
| FlowCount_d | Elavult. Az azonos négy rekordnak megfelelő összes folyamat. A ExternalPublic és a AzurePublic flow-típusok esetében a Count a különböző PublicIP-címekből származó folyamatokat is tartalmazza.
| InboundPackets_d | A rögzítettként fogadott csomagok a hálózati adapteren, ahol a NSG-szabály alkalmazva lett | Ez csak a NSG flow-napló sémájának 2. verziójára van feltöltve |
| OutboundPackets_d  | A rögzítettként küldött csomagok a hálózati adapteren, ahol a NSG-szabály alkalmazva lett | Ez csak a NSG flow-napló sémájának 2. verziójára van feltöltve |
| InboundBytes_d |  Fogadott bájtok száma a hálózati adapteren rögzített NSG-szabály alkalmazásakor | Ez csak a NSG flow-napló sémájának 2. verziójára van feltöltve |
| OutboundBytes_d | Rögzített bájtok a NSG-szabály alkalmazási helyéül szolgáló hálózati adapteren | Ez csak a NSG flow-napló sémájának 2. verziójára van feltöltve |
| CompletedFlows_d  |  | Ez a nullától eltérő értékkel van feltöltve, csak a NSG flow-napló sémájának 2. verziójára |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Oszlopok által elválasztott bejegyzések |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Oszlopok által elválasztott bejegyzések |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Oszlopok által elválasztott bejegyzések |

### <a name="notes"></a>Jegyzetek

1. AzurePublic-és ExternalPublic-folyamatok esetén az Azure-beli virtuális gép IP-címe VMIP_s mezőben van feltöltve, míg a nyilvános IP-címek a PublicIPs_s mezőben vannak feltöltve. A két folyamat esetében az SrcIP_s és a DestIP_s mezők helyett VMIP_s és PublicIPs_st kell használnia. A AzurePublic-és ExternalPublicIP-címek esetében a rendszer tovább összesíti az adatokat, így az ügyfél log Analytics-munkaterületre betöltött rekordok száma minimális. (Ez a mező hamarosan elavult lesz, és a SrcIP_t kell használnia, és DestIP_s attól függően, hogy az Azure-beli virtuális gép a forrás vagy a cél volt a folyamatban)
1. A flow típusaival kapcsolatos részletek: a folyamatba bevont IP-címek alapján kategorizáljuk a folyamatokat a következő típusú folyamatokhoz:
1. IntraVNet – a folyamat IP-címei ugyanabban az Azure-Virtual Network találhatók.
1. Virtuális hálózatok közötti – a folyamat IP-címei a két különböző Azure-beli virtuális hálózatban találhatók.
1. S2S – (helyek közötti) az egyik IP-cím az Azure Virtual Networkhoz tartozik, míg a másik IP-cím az Azure Virtual Networkhoz a VPN Gateway vagy az Express Route használatával csatlakoztatott ügyfél hálózat (hely) közé tartozik.
1. P2S – (pont – hely) az egyik IP-cím az Azure Virtual Networkhoz tartozik, míg a másik IP-cím az Azure Virtual Networkhoz a VPN-átjárón keresztül csatlakozó ügyfél hálózat (hely) közé tartozik.
1. AzurePublic – az egyik IP-cím az Azure Virtual Networkhoz tartozik, míg a másik IP-cím a Microsoft tulajdonában lévő Azure belső nyilvános IP-címekhez tartozik. Az ügyfél nyilvános IP-címei nem részei ennek a folyamatnak. Előfordulhat például, hogy bármely, az Azure-szolgáltatásba (Storage-végpontba) forgalmat küldő ügyfél tulajdonában lévő virtuális gép a folyamat típusa szerint lesz kategorizálva.
1. ExternalPublic – az egyik IP-cím az Azure Virtual Networkhoz tartozik, míg a másik IP-cím olyan nyilvános IP-cím, amely nem az Azure-ban található, nem a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti feldolgozási időközre Traffic Analytics felhasználható ASC-hírcsatornákban szerepel.
1. MaliciousFlow – az IP-címek egyike az Azure Virtual Networkhöz tartozik, míg a másik IP-cím egy olyan nyilvános IP-cím, amely nem az Azure-ban található, és a rendszer kártékonyként jelenti a "FlowIntervalStartTime_t" és a "FlowIntervalEndTime_t" közötti feldolgozási időközre Traffic Analytics.
1. UnknownPrivate – az IP-címek egyike az Azure Virtual Networkhoz tartozik, míg a másik IP-cím a 1918-as számú RFC-dokumentumban meghatározott magánhálózati IP-tartományhoz tartozik, és nem képezhető le Traffic Analytics egy ügyfél tulajdonában lévő webhelyhez vagy Azure-Virtual Network.
1. Ismeretlen – a folyamatokban lévő IP-címek egyikét nem lehet leképezni az Azure-beli ügyfél-topológiával, valamint a helyszínen (hely).
1. Egyes mezők neve a következővel van hozzáfűzve: \_ s vagy \_ d. Ezek nem jelentik a forrás és a cél jelölését, hanem az adattípusokat, illetve a decimális karakterláncot.

### <a name="next-steps"></a>Következő lépések
A gyakori kérdésekre adott válaszokért lásd: [Traffic Analytics – gyakori](traffic-analytics-faq.md) kérdések a funkciók részleteiről: [Traffic Analytics – dokumentáció](traffic-analytics.md)
