---
title: Naplók lekérdezése a VM-elemzésből
description: A VM-elemzési megoldás metrikákat és naplózási adatokat gyűjt, és ez a cikk ismerteti a rekordokat, és példákat tartalmaz a lekérdezésekre.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 28ee7f3d327c09f5837c7dc9e2f39c0f2ca4d888
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102046533"
---
# <a name="how-to-query-logs-from-vm-insights"></a>Naplók lekérdezése a VM-elemzésből

A VM-elemzések a teljesítmény és a kapcsolatok mérőszámait, a számítógép-és feldolgozási leltári adatokat, valamint az állapotadatok adatait és a Azure Monitor Log Analytics munkaterületére továbbítják.  Ezek az adatAzure Monitor [lekérdezésekhez](../logs/log-query-overview.md) érhetők el. Ezeket az információkat olyan forgatókönyvekre alkalmazhatja, amelyek tartalmazzák az áttelepítés megtervezését, a kapacitás elemzését, a felderítést és az igény szerinti teljesítménnyel kapcsolatos hibaelhárítást.

## <a name="map-records"></a>Rekordok leképezése

A rendszer óránként létrehoz egy rekordot minden egyes egyedi számítógéphez és folyamathoz, valamint a folyamat vagy számítógép indításakor vagy a virtuális gépekre vonatkozó Térkép funkcióba való bevezetéskor generált rekordokon kívül. Ezek a rekordok a következő táblákban található tulajdonságokkal rendelkeznek. A ServiceMapComputer_CL események mezői és értékei a ServiceMap Azure Resource Manager API-ban lévő számítógép-erőforrás mezőire vannak leképezve. A ServiceMapProcess_CL események mezői és értékei a ServiceMap Azure Resource Manager API-ban található folyamat-erőforrás mezőire vannak leképezve. A ResourceName_s mező megegyezik a megfelelő Resource Manager-erőforrásban található Name mezővel. 

Az egyedi folyamatok és számítógépek azonosításához belsőleg generált tulajdonságok tartoznak:

- Számítógép: a *ResourceId* vagy a *ResourceName_s* használatával egyedileg azonosíthatja a számítógépeket egy log Analytics munkaterületen belül.
- Folyamat: a *ResourceId* használatával egyedileg azonosíthatja a folyamatokat egy log Analytics munkaterületen belül. *ResourceName_s* a folyamat futása alatt álló számítógép kontextusában (MachineResourceName_s) egyedi. 

Mivel a megadott időtartományban több rekord is létezhet egy adott folyamat és számítógép esetében, a lekérdezések több rekordot is visszaadhatnak ugyanahhoz a számítógéphez vagy folyamathoz. Ha csak a legújabb rekordot szeretné felvenni, adja hozzá `| summarize arg_max(TimeGenerated, *) by ResourceId` a lekérdezéshez.

### <a name="connections-and-ports"></a>Kapcsolatok és portok

A kapcsolatok Metrikái szolgáltatás két új táblát vezet be Azure Monitor naplókba – VMConnection és VMBoundPort. Ezek a táblák a számítógépek kapcsolatairól (bejövő és kimenő), valamint a rajtuk nyitott/aktív kiszolgáló-portokról nyújtanak információt. A ConnectionMetrics API-kon keresztül is elérhetők, amelyek lehetővé teszik egy adott metrika egy adott időszakra való beszerzését. A figyelő szoftvercsatornán való *elfogadást* EREDMÉNYEZő TCP-kapcsolatok bejövőek, míg az adott IP-címhez *való csatlakozással* és a porttal való kapcsolat kimenő. A kapcsolatok irányát az Direction tulajdonság jelképezi, amely beállítható **bejövő** vagy **kimenő** értékre. 

A táblázatokban szereplő rekordok a Dependency Agent által jelentett adatokból jönnek létre. Minden rekord egy 1 perces időszakra vonatkozó megfigyelést jelöl. A TimeGenerated tulajdonság az időintervallum kezdetét jelzi. Minden rekord tartalmaz információt a megfelelő entitás, azaz a csatlakozás vagy a port, valamint az entitáshoz társított metrikák azonosítására. Jelenleg csak a TCP protokollt használó hálózati tevékenységek jelentik a jelentést. 

#### <a name="common-fields-and-conventions"></a>Gyakori mezők és konvenciók 

A következő mezők és konvenciók a VMConnection és a VMBoundPort egyaránt érvényesek: 

- Számítógép: a jelentéskészítő gép teljes tartományneve 
- Ügynökazonosító: a Log Analytics ügynökkel rendelkező gép egyedi azonosítója  
- Machine (gép): a ServiceMap által elérhetővé tett gép Azure Resource Manager erőforrásának neve. Az *m-{GUID}* formában van, ahol a *GUID* ugyanaz a GUID, mint a ügynökazonosító  
- Folyamat: a ServiceMap által elérhetővé tett folyamat Azure Resource Manager erőforrásának neve. A *p-{hex string}* formátumú. A folyamat egyedi a számítógép hatókörén belül, és egyedi folyamat-azonosítót állít elő a gépek között, kombinálja a gép és a folyamat mezőit. 
- ProcessName: a jelentéskészítési folyamat végrehajtható fájljának neve.
- Az összes IP-cím IPv4 kanonikus formátumú karakterlánc, például *13.107.3.160* 

A költségeket és a bonyolultságot a kapcsolati rekordok nem jelölik az egyes fizikai hálózati kapcsolatokat. A fizikai hálózati kapcsolatok több logikai kapcsolatba vannak csoportosítva, amely az adott táblázatban látható.  Ez azt jelenti, hogy a *VMConnection* táblában lévő rekordok logikai csoportosítást jelölnek, nem pedig a megfigyelt egyedi fizikai kapcsolatokat. Egy adott egyperces időszakban a következő attribútumok esetében azonos értéket megosztó fizikai hálózati kapcsolatok egyetlen logikai rekordba vannak összesítve a *VMConnection*-ben. 

| Tulajdonság | Leírás |
|:--|:--|
|Irány |A kapcsolatok iránya, az érték *bejövő* vagy *kimenő* . |
|Gép |A számítógép teljes tartományneve |
|Folyamat |Folyamatok vagy csoportok identitása, a kapcsolatok kezdeményezése/elfogadása |
|SourceIp |A forrás IP-címe |
|DestinationIp |A cél IP-címe |
|DestinationPort |A célhely portszáma |
|Protokoll |A kapcsolathoz használt protokoll.  Az értékek a *TCP*. |

A csoportosítás hatásának kiszámításához a csoportosított fizikai kapcsolatok számával kapcsolatos információk a rekord következő tulajdonságaiban vannak megadva:

| Tulajdonság | Leírás |
|:--|:--|
|LinksEstablished |A jelentéskészítési idő ablakában létrehozott fizikai hálózati kapcsolatok száma |
|LinksTerminated |A jelentéskészítési idő ablakában leállított fizikai hálózati kapcsolatok száma |
|LinksFailed |Azon fizikai hálózati kapcsolatok száma, amelyek sikertelenek voltak a jelentéskészítési idő ablakában. Ez az információ jelenleg csak a kimenő kapcsolatok esetében érhető el. |
|LinksLive |A jelentéskészítési idő ablakának végén megnyitott fizikai hálózati kapcsolatok száma|

#### <a name="metrics"></a>Mérőszámok

A kapcsolatok számának mérőszámai mellett az adott logikai kapcsolatban vagy hálózati porton küldött és fogadott adatok mennyiségére vonatkozó információk is szerepelni fog a rekord alábbi tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
|BytesSent |A jelentéskészítési idő ablakában elküldhető bájtok teljes száma |
|BytesReceived |A jelentéskészítési idő ablakában fogadott bájtok teljes száma |
|Válaszok |A jelentéskészítési idő ablakában megfigyelt válaszok száma. 
|ResponseTimeMax |A jelentéskészítési idő ablakában megfigyelt legnagyobb válaszidő (ezredmásodpercben). Ha nincs érték, a tulajdonság üres.|
|ResponseTimeMin |A jelentéskészítési idő ablakában megfigyelt legkisebb válaszidő (ezredmásodpercben). Ha nincs érték, a tulajdonság üres.|
|ResponseTimeSum |A jelentéskészítési idő ablakában megfigyelt összes válaszidő (ezredmásodperc) összege. Ha nincs érték, a tulajdonság üres.|

A jelentett harmadik adattípus a válaszidő – mennyi időt vesz igénybe a hívó a kapcsolaton keresztül küldött kérések feldolgozására, és a távoli végpont által válaszol. A jelentett válaszidő az alapul szolgáló alkalmazás protokolljának valós válaszideje. A rendszer a heurisztikus használatával számítja ki a fizikai hálózati kapcsolat forrás-és cél vége közötti adatáramlás megfigyelése alapján. Elméletileg az a különbség, hogy a kérelem utolsó bájtja mikor hagyja el a küldőt, valamint azt az időpontot, amikor a válasz utolsó bájtja vissza fog érkezni. Ez a két időbélyeg a kérések és válaszok eseményeinek egy adott fizikai kapcsolatban való kiválasztására szolgál. A kettő közötti különbség az egyetlen kérelem válaszideje. 

A szolgáltatás ezen első kiadásában az algoritmus egy olyan közelítés, amely az adott hálózati kapcsolathoz használt tényleges alkalmazási protokolltól függően különböző sikereket eredményezhet. Például az aktuális megközelítés jól működik a kérelem-válasz alapú protokollok, például a HTTP (S) esetében, de nem működik egyirányú vagy üzenetsor-alapú protokollokkal.

Néhány fontos szempontot figyelembe kell venni:

1. Ha egy folyamat ugyanazon az IP-címen, de több hálózati adapteren keresztül fogad kapcsolatokat, a rendszer minden egyes csatolóhoz külön rekordot fog jelenteni. 
2. A helyettesítő karakteres IP-címmel rendelkező rekordok nem tartalmaznak tevékenységet. A szolgáltatás tartalmazza azt a tényt, hogy a gépen lévő port nyitva van a bejövő forgalom számára.
3. A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő karakteres IP-címekkel rendelkező rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord van (ugyanahhoz a folyamathoz, porthoz és protokollhoz). Ha nincs megadva helyettesítő IP-rekord, a IsWildcardBind Record tulajdonsága az adott IP-címmel "true" (igaz) értékre lesz állítva, amely jelzi, hogy a port a jelentéskészítő gép összes felületén elérhetővé válik.
4. Azokat a portokat, amelyek csak egy adott csatolón vannak kötve, a IsWildcardBind *hamis* értékre van állítva.

#### <a name="naming-and-classification"></a>Elnevezés és besorolás

A kényelem érdekében a kapcsolatok távoli végének IP-címét a RemoteIp tulajdonság tartalmazza. A bejövő kapcsolatok esetében a RemoteIp ugyanaz, mint a SourceIp, míg a kimenő kapcsolatok esetében ugyanaz, mint a DestinationIp. A RemoteDnsCanonicalNames tulajdonság a gép által a RemoteIp számára jelentett DNS-kanonikus neveket jelöli. A RemoteDnsQuestions tulajdonság a gép által a RemoteIp számára jelentett DNS-kérdéseket jelöli. A RemoveClassification tulajdonság későbbi használatra van fenntartva. 

#### <a name="geolocation"></a>Földrajzi hely

A *VMConnection* az egyes kapcsolatok távoli végére vonatkozó térinformatikai információkat is tartalmaz a rekord alábbi tulajdonságaiban: 

| Tulajdonság | Leírás |
|:--|:--|
|RemoteCountry |A RemoteIp üzemeltető ország/régió neve.  Például *Egyesült Államok* |
|RemoteLatitude |A térinformatikai szélesség. Például *47,68* |
|RemoteLongitude |A térinformatikai hosszúság. Például: *-122,12* |

#### <a name="malicious-ip"></a>Kártékony IP-cím

A *VMConnection* táblában lévő összes RemoteIp-tulajdonságot a rendszer az ismert kártékony tevékenységgel rendelkező IP-címek készletében ellenőrzi. Ha a RemoteIp rosszindulatúként van azonosítva, a következő tulajdonságok lesznek feltöltve (ezek üresek, ha az IP nem minősül rosszindulatúnak) a rekord következő tulajdonságaiban:

| Tulajdonság | Leírás |
|:--|:--|
|MaliciousIp |A RemoteIp címe |
|IndicatorThreadType |Az észlelt veszélyforrás a következő értékek egyike: *botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *malware*, *phishing*, *proxy*, *PUA*, *List*.   |
|Description |A megfigyelt fenyegetés leírása. |
|TLPLevel |A forgalmi lámpa protokoll (TLP) szintje az egyik definiált érték, a *fehér*, a *zöld*, a *sárga* és a *vörös*. |
|Megbízhatóság |Az értékek *0 – 100*. |
|Súlyosság |Az értékek *0 – 5*, ahol az *5* a legsúlyosabb, a *0* pedig egyáltalán nem súlyos. Az alapértelmezett érték *3*.  |
|FirstReportedDateTime |Az első alkalommal, amikor a szolgáltató jelentette a kijelzőt. |
|LastReportedDateTime |A mutató a folyamat utolsó időpontjában volt látható. |
|IsActive |Azt jelzi, hogy a kijelzők inaktiválva vannak-e *igaz* vagy *hamis* értékkel. |
|ReportReferenceLink |Egy adott megfigyelhető jelentéshez kapcsolódó jelentések hivatkozásai. |
|AdditionalInformation |További információkat biztosít a megfigyelt fenyegetésről. |

### <a name="ports"></a>Portok 

Azok a portok, amelyek aktívan elfogadják a bejövő forgalmat, vagy képesek fogadni a forgalmat, de a jelentéskészítési idő ablakában inaktívak, a VMBoundPort táblába íródnak.  

A VMBoundPort összes rekordját a következő mezők azonosítják: 

| Tulajdonság | Leírás |
|:--|:--|
|Folyamat | Azon folyamat (vagy folyamatok csoportjai) identitása, amelyekhez a port társítva van.|
|IP | Port IP-címe (lehet helyettesítő IP-cím, *0.0.0.0*) |
|Port |A portszám |
|Protokoll | A protokoll.  Példa: *TCP* vagy *UDP* (csak a *TCP* jelenleg támogatott).|
 
Az identitás egy port a fenti öt mezőből származik, és a PortId tulajdonságban tárolódik. Ezzel a tulajdonsággal gyorsan megtalálhatja az adott port rekordjait az idő során. 

#### <a name="metrics"></a>Mérőszámok 

A portok rekordjai közé tartoznak a hozzájuk társított kapcsolatokra vonatkozó metrikák. Jelenleg a következő metrikákat mutatjuk be (az egyes mérőszámok részleteit az előző szakaszban találja): 

- BytesSent és BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Néhány fontos szempontot figyelembe kell venni:

- Ha egy folyamat ugyanazon az IP-címen, de több hálózati adapteren keresztül fogad kapcsolatokat, a rendszer minden egyes csatolóhoz külön rekordot fog jelenteni.  
- A helyettesítő karakteres IP-címmel rendelkező rekordok nem tartalmaznak tevékenységet. A szolgáltatás tartalmazza azt a tényt, hogy a gépen lévő port nyitva van a bejövő forgalom számára. 
- A részletesség és az adatmennyiség csökkentése érdekében a helyettesítő karakteres IP-címekkel rendelkező rekordok kimaradnak, ha egy adott IP-címmel rendelkező egyező rekord van (ugyanahhoz a folyamathoz, porthoz és protokollhoz). Helyettesítő karakteres IP-rekord kihagyása esetén az adott IP-címmel rendelkező rekord *IsWildcardBind* tulajdonsága *igaz* értékre lesz állítva.  Ez azt jelzi, hogy a port a jelentéskészítő gép minden felületén elérhető. 
- Azokat a portokat, amelyek csak egy adott csatolón vannak kötve, a IsWildcardBind *hamis* értékre van állítva. 

### <a name="vmcomputer-records"></a>VMComputer-rekordok

A *VMComputer* rendelkező rekordok a függőségi ügynökkel rendelkező kiszolgálók leltározási adataival rendelkeznek. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
|TenantId | A munkaterület egyedi azonosítója |
|SourceSystem | *Insights* | 
|TimeGenerated | A rekord időbélyegzője (UTC) |
|Computer | A számítógép teljes tartományneve | 
|Ügynökazonosító | A Log Analytics ügynök egyedi azonosítója |
|Gép | A ServiceMap által elérhetővé tett számítógép Azure Resource Manager erőforrásának neve. Az *m-{GUID}* formátumú, ahol a *GUID* ugyanaz, mint a ügynökazonosító. | 
|DisplayName | Megjelenített név | 
|FullDisplayName | Teljes megjelenítendő név | 
|HostName | A tartománynév nélküli számítógép neve |
|BootTime | A gép rendszerindítási ideje (UTC) |
|TimeZone | A normalizált időzóna |
|VirtualizationState | *virtuális*, *hypervisor*, *fizikai* |
|Ipv4Addresses | IPv4-címek tömbje | 
|Ipv4SubnetMasks | IPv4 alhálózati maszkok tömbje (ugyanabban a sorrendben, mint a Ipv4Addresses). |
|Ipv4DefaultGateways | IPv4-átjárók tömbje | 
|Ipv6Addresses | IPv6-címek tömbje | 
|MacAddresses | MAC-címek tömbje | 
|DnsNames | A géphez társított DNS-nevek tömbje. |
|DependencyAgentVersion | A számítógépen futó függőségi ügynök verziója. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | Az operációs rendszer teljes neve | 
|PhysicalMemoryMB | A fizikai memória megabájtban | 
|Processzorok | Processzorok száma | 
|CpuSpeed | A processzor sebessége (MHz) | 
|VirtualMachineType | *HyperV*, *VMware*, *Xen* |
|VirtualMachineNativeId | A virtuális gép azonosítója, amelyet a hypervisor rendel hozzá | 
|VirtualMachineNativeName | A virtuális gép neve |
|VirtualMachineHypervisorId | A virtuális gépet működtető hypervisor egyedi azonosítója |
|HypervisorType | *HyperV* |
|HypervisorId | A hypervisor egyedi azonosítója | 
|HostingProvider | *Azure* |
|_ResourceId | Egy Azure-erőforrás egyedi azonosítója |
|AzureSubscriptionId | Az előfizetést azonosító globálisan egyedi azonosító | 
|AzureResourceGroup | Azon Azure-erőforráscsoport neve, amely a gép tagja. |
|AzureResourceName | Az Azure-Erőforrás neve |
|AzureLocation | Az Azure-erőforrás helye |
|AzureUpdateDomain | Az Azure frissítési tartomány neve |
|AzureFaultDomain | Az Azure-beli tartalék tartomány neve |
|AzureVmId | Az Azure-beli virtuális gép egyedi azonosítója |
|AzureSize | Az Azure-beli virtuális gép mérete |
|AzureImagePublisher | Az Azure VM-közzétevő neve |
|AzureImageOffering | Az Azure-beli virtuális gép ajánlati típusának neve | 
|AzureImageSku | Az Azure VM-rendszerkép SKU-jának | 
|AzureImageVersion | Az Azure VM-rendszerkép verziója | 
|AzureCloudServiceName | Az Azure Cloud Service neve |
|AzureCloudServiceDeployment | A Cloud Service üzembe helyezési azonosítója |
|AzureCloudServiceRoleName | Cloud Service-szerepkör neve |
|AzureCloudServiceRoleType | Cloud Service szerepkör típusa: *Worker* vagy *web* |
|AzureCloudServiceInstanceId | Cloud Service-szerepkör példányának azonosítója |
|AzureVmScaleSetName | A virtuálisgép-méretezési csoport neve |
|AzureVmScaleSetDeployment | Virtuálisgép-méretezési csoport központi telepítési azonosítója |
|AzureVmScaleSetResourceId | A virtuálisgép-méretezési csoport erőforrásának egyedi azonosítója.|
|AzureVmScaleSetInstanceId | A virtuálisgép-méretezési csoport egyedi azonosítója |
|AzureServiceFabricClusterId | Az Azure Service Fabric-fürt egyedi termékazonosító | 
|AzureServiceFabricClusterName | Az Azure Service Fabric-fürt neve |

### <a name="vmprocess-records"></a>VMProcess-rekordok

A *VMProcess* rendelkező rekordok a függőségi ügynökkel rendelkező kiszolgálókon a TCP-kapcsolattal rendelkező folyamatok leltározási adataival rendelkeznek. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
|:--|:--|
|TenantId | A munkaterület egyedi azonosítója |
|SourceSystem | *Insights* | 
|TimeGenerated | A rekord időbélyegzője (UTC) |
|Computer | A számítógép teljes tartományneve | 
|Ügynökazonosító | A Log Analytics ügynök egyedi azonosítója |
|Gép | A ServiceMap által elérhetővé tett számítógép Azure Resource Manager erőforrásának neve. Az *m-{GUID}* formátumú, ahol a *GUID* ugyanaz, mint a ügynökazonosító. | 
|Folyamat | Az Service Map folyamat egyedi azonosítója. A formátuma *p-{GUID}*. 
|ExecutableName | A folyamat végrehajtható fájljának neve | 
|DisplayName | Folyamat megjelenítendő neve |
|Szerepkör | Szerepkör feldolgozása: *webkiszolgáló*, *appServer*, *databaseServer*, *ldapServer*, *smbServer* |
|Group | A feldolgozó csoport neve. Az ugyanabban a csoportban lévő folyamatok logikailag kapcsolódnak egymáshoz, például ugyanannak a terméknek vagy rendszerösszetevőnek a része. |
|StartTime | A folyamat készletének kezdési ideje |
|FirstPid | A folyamat első PID-je |
|Description | A folyamat leírása |
|CompanyName | A vállalat neve |
|InternalName | A belső név |
|TermékNév | A termék neve |
|ProductVersion | A termék verziója |
|FileVersion | A fájl verziója |
|ExecutablePath |A végrehajtható fájl elérési útja |
|CommandLine | A parancssor |
|WorkingDirectory | A munkakönyvtár |
|Szolgáltatások | Azon szolgáltatások tömbje, amelyeken a folyamat végrehajtása történik |
|Felhasználónév | Az a fiók, amelyben a folyamat végre van hajtva |
|UserDomain | Az a tartomány, amelyben a folyamat végrehajtás alatt áll |
|_ResourceId | A munkaterületen belüli folyamat egyedi azonosítója |


## <a name="sample-map-queries"></a>Minta-hozzárendelési lekérdezések

### <a name="list-all-known-machines"></a>Az összes ismert gép listázása

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Mikor volt utoljára újraindítva a virtuális gép

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Azure-beli virtuális gépek összefoglalása rendszerkép, hely és SKU alapján

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Az összes felügyelt számítógép fizikai memória-kapacitásának listázása

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Számítógép nevének, DNS-címének, IP-címének és operációs rendszerének listázása

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Az összes folyamat megkeresése az "SQL" paranccsal a parancssorban

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Számítógép (legfrissebb rekord) keresése erőforrás neve alapján

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Számítógép keresése (legfrissebb rekord) IP-cím szerint

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>A megadott gépen lévő összes ismert folyamat listázása

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Az összes SQL Server rendszert futtató számítógép listázása

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>A curl összes egyedi termék-verziójának listázása az adatközpontban

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>A CentOS-t futtató összes számítógép számítógépcsoport létrehozása

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Küldött és fogadott bájtok száma

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Mely Azure-beli virtuális gépek továbbítják a legtöbb bájtot

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Kapcsolatok állapotának trendjei

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>A sikertelen kapcsolatok trendje

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Kötött portok

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>A nyitott portok száma a gépek között

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>A munkaterületen a megnyitott portok számával szerzi be a folyamatokat

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Az egyes portok összesített viselkedése

Ezzel a lekérdezéssel a portok tevékenység alapján, például a legtöbb bejövő/kimenő forgalomtal rendelkező portok, a legtöbb kapcsolattal rendelkező portok használhatók.
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>A kimenő kapcsolatok összefoglalása a számítógépek csoportjából

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>Teljesítményadatokat
A *InsightsMetrics* rendelkező rekordok a virtuális gép vendég operációs rendszeréről származó teljesítményadatokat rendelkeznek. Ezek a rekordok a következő táblázatban szereplő tulajdonságokkal rendelkeznek:


| Tulajdonság | Leírás |
|:--|:--|
|TenantId | A munkaterület egyedi azonosítója |
|SourceSystem | *Insights* | 
|TimeGenerated | Az érték gyűjtésének időpontja (UTC) |
|Computer | A számítógép teljes tartományneve | 
|Forrás | *vm.azm.ms* |
|Névtér | A teljesítményszámláló kategóriája | 
|Name | A teljesítményszámláló neve |
|Val | Összegyűjtött érték | 
|Címkék | A rekorddal kapcsolatos részletek. Az alábbi táblázatban láthatja a különböző bejegyzéstípusokkal használt címkéket.  |
|Ügynökazonosító | Az egyes számítógépek ügynökének egyedi azonosítója |
|Típus | *InsightsMetrics* |
|_ResourceId_ | A virtuális gép erőforrás-azonosítója |

A jelenleg a *InsightsMetrics* táblába összegyűjtött teljesítményszámlálók az alábbi táblázatban láthatók:

| Névtér | Név | Leírás | Unit (Egység) | Címkék |
|:---|:---|:---|:---|:---|
| Computer    | Szívverés             | Számítógép szívverése                        | | |
| Memória      | AvailableMB           | Rendelkezésre álló memória bájtjai                    | Megabájtban      | memorySizeMB – teljes memória mérete|
| Network (Hálózat)     | WriteBytesPerSecond   | Hálózati írási bájtok másodpercenként            | BytesPerSecond | NetworkDeviceId – az eszköz azonosítója<br>bájtok – elküldési bájtok összesen |
| Network (Hálózat)     | ReadBytesPerSecond    | Hálózati olvasási bájtok másodpercenként             | BytesPerSecond | networkDeviceId – az eszköz azonosítója<br>bájtok – fogadott bájtok összesen |
| Processzor   | UtilizationPercentage | Processzor kihasználtsága (%)          | Százalék        | totalCpus – processzorok összesen |
| LogicalDisk | WritesPerSecond       | A logikai lemez írása másodpercenként            | CountPerSecond | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | WriteLatencyMs        | Logikai lemez írási késése (ezredmásodperc)    | Ezredmásodpercben   | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | WriteBytesPerSecond   | Logikai lemez írási sebessége (bájt/s)       | BytesPerSecond | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | TransfersPerSecond    | Logikai lemez átvitele másodpercenként         | CountPerSecond | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | TransferLatencyMs     | Logikai lemez átvitelének késése (ezredmásodperc) | Ezredmásodpercben   | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | ReadsPerSecond        | Logikai lemez olvasási másodpercenként             | CountPerSecond | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | ReadLatencyMs         | Logikai lemez olvasási késése (ezredmásodperc)     | Ezredmásodpercben   | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | ReadBytesPerSecond    | Logikai lemez olvasási bájtjai másodpercenként        | BytesPerSecond | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | FreeSpacePercentage   | Logikai lemez – szabad terület százalékos aránya        | Százalék        | az eszköz mountId-csatlakoztatási azonosítója |
| LogicalDisk | FreeSpaceMB           | Logikai lemez – szabad terület bájtjai             | Megabájtban      | az eszköz mountId-csatlakoztatási azonosítója<br>diskSizeMB – teljes lemez mérete |
| LogicalDisk | BytesPerSecond        | Logikai lemez bájtjai másodpercenként             | BytesPerSecond | az eszköz mountId-csatlakoztatási azonosítója |


## <a name="next-steps"></a>Következő lépések

* Ha a Azure Monitor naplóbeli lekérdezések írásakor új, tekintse át a következő [témakört: log Analytics használata](../logs/log-analytics-tutorial.md) a Azure Portal a naplók írásához.

* További információ a [keresési lekérdezések írásához](../logs/get-started-queries.md).