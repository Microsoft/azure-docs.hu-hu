---
title: Csatlakozáskezelő létrehozása – PowerShell
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan hozhat létre egy Csatlakozáskezelőt a PowerShell használatával.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 7f175d82b650871437a506ea4513f0ae28360f68
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833031"
---
# <a name="create-a-connection-monitor-by-using-powershell"></a>Figyelő létrehozása a PowerShell használatával

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Emellett nem adhat hozzá új kapcsolódási figyelőket a Csatlakozáskezelő (klasszikus) szolgáltatáshoz. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket és kapcsolódási figyelőket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) vagy  [migrálja a kapcsolódási figyelőről (klasszikus)](migrate-to-connection-monitor-from-connection-monitor-classic.md) az Azure-Network Watcher új, a 2024. február 29. előtti kapcsolódási figyelője felé.


Megtudhatja, hogyan használhatja az Azure Network Watcher kapcsolatkezelő szolgáltatását az erőforrások közötti kommunikáció figyelésére.


## <a name="before-you-begin"></a>Előkészületek

A Csatlakozáskezelő használatával létrehozott kapcsolódó figyelők esetében a helyszíni gépeket és az Azure-beli virtuális gépeket (VM) forrásként is hozzáadhatja. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A kapcsolatok figyelője a következő entitásokat tartalmazza:

* **Csatlakozáskezelő erőforrás**: régióra jellemző Azure-erőforrás. A következő entitások mindegyike a Csatlakozáskezelő erőforrásának tulajdonságait képezi.
* **Végpont**: a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Ilyen végpontok például az Azure-beli virtuális gépek, a helyszíni ügynökök, az URL-címek és az IP-címek.
* **Tesztelési konfiguráció**: a tesztek protokoll-specifikus konfigurációja. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Test Group**: a forrás-végpontokat, a cél végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Teszt**: a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

    ![A kapcsolati figyelőt bemutató diagram, a tesztelési csoportok és tesztek közötti kapcsolat meghatározása.](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-a-connection-monitor"></a>A kapcsolódási figyelő létrehozásának lépései

A következő parancsokkal hozhat létre egy kapcsolódási figyelőt a PowerShell használatával.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Tulajdonságok leírása

* **ConnectionMonitorName**: a Csatlakozáskezelő erőforrás neve.

* **Sub**: az előfizetés azon előfizetési azonosítója, amelyben létre kívánja hozni a kapcsolódási figyelőt.

* **NW**: Network Watcher erőforrás-azonosító, amelyben a rendszer létrehoz egy összekötő-figyelőt.

* **Hely**: az a régió, amelyben a rendszer létrehoz egy összekötő-figyelőt.

* **Végpontok**
    * **Name**: az egyes végpontok egyedi neve.
    * **Erőforrás-azonosító**: az Azure-végpontokhoz az erőforrás-azonosító a virtuális gépek Azure Resource Manager erőforrás-azonosítóját jelöli. Nem Azure-végpontok esetén az erőforrás-azonosító a nem Azure-ügynökökhöz társított Log Analytics munkaterület Azure Resource Manager erőforrás-AZONOSÍTÓját jelöli.
    * **Címe**: csak akkor alkalmazható, ha az erőforrás-azonosító nincs megadva, vagy ha az erőforrás-azonosító a log Analytics munkaterületen található. Ha erőforrás-azonosító nélkül használja, akkor ez lehet bármely nyilvános végpont URL-címe vagy IP-címe. Ha Log Analytics erőforrás-AZONOSÍTÓval használja, ez a figyelési ügynök teljes tartománynevére vonatkozik.
    * **Szűrő**: a nem Azure-végpontok esetében szűrők használatával válassza ki a figyelési ügynököket a log Analytics munkaterületről a Csatlakozáskezelő erőforrásban. Ha a szűrők nincsenek beállítva, akkor a Log Analytics munkaterülethez tartozó összes ügynök használható figyelésre.
        * **Típus**: állítsa az **ügynök címe** értéket.
        * **Címe**: állítsa be a helyszíni ügynök teljes tartománynevét.

* **Tesztelési csoportok**
    * **Név**: nevezze el a tesztelési csoportot.
    * **Források**: válassza ki a korábban létrehozott végpontokat. Az Azure-alapú forrás-végpontoknak telepítve kell lennie az Azure Network Watcher bővítménynek; a nem Azure-alapú forrás-végpontoknak telepítve kell lennie egy Azure Log Analytics-ügynöknek. A forráshoz tartozó ügynök telepítéséhez tekintse meg a [figyelési ügynökök telepítése](./connection-monitor-overview.md#install-monitoring-agents)című témakört.
    * **Célhelyek**: válassza ki a korábban létrehozott végpontokat. Az Azure-beli virtuális gépekhez vagy bármely végponthoz (nyilvános IP-címhez, URL-címhez vagy FQDN-hez) való csatlakozást célhelyként megadható módon figyelheti. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, Office 365 URL-címeket, Dynamics 365 URL-címeket és egyéni végpontokat adhat hozzá.
    * **Letiltás**: tiltsa le az összes olyan forrás és cél figyelését, amelyet a tesztelési csoport meghatároz.

* **Tesztelési konfigurációk**
    * **Név**: nevezze el a teszt konfigurációját.
    * **TestFrequencySec**: adja meg, hogy a források milyen gyakran pingek a megadott protokollon és porton. 30 másodpercet, 1 percet, 5 percet, 15 percet vagy 30 percet is kiválaszthat. A források a választott érték alapján próbálnak meg kapcsolódni a célhelyekhez. Ha például 30 másodpercet választ, a források legalább egyszer megkeresik a kapcsolatot egy 30 másodperces időszakon belül.
    * **Protokoll**: válassza a TCP, ICMP, http vagy https lehetőséget. A protokolltól függően a következő protokoll-specifikus konfigurációkat is kiválaszthatja:
        * **preferHTTPS**: adja meg, hogy HTTPS protokollt használ-e http-n keresztül.
        * **port**: adja meg az Ön által választott cél portot.
        * **disableTraceRoute**: a források leállítása a topológia és a hop-by-hop RTT használatával. Ez a TCP vagy ICMP protokollt alkalmazó tesztelési csoportokra vonatkozik.
        * **metódus**: válassza a HTTP-kérelem módszert (Get vagy post). Ez a HTTP-vel végzett tesztelési konfigurációkra vonatkozik.
        * **elérési út**: határozza meg az URL-címhez hozzáfűzni kívánt elérésiút-paramétereket.
        * **validStatusCodes**: válassza ki a megfelelő állapotkódot. Ha a válasz kódja nem egyezik, diagnosztikai üzenet jelenik meg.
        * **requestHeaders**: adja meg az egyéni kérelem fejlécének karakterláncait, amelyek a célhelyre adnak át.
    * **Sikerességi küszöb**: állítsa be a küszöbértékeket a következő hálózati paraméterekre:
        * **checksFailedPercent**: adja meg a sikertelen ellenőrzések százalékos arányát, ha a források a megadott feltételekkel ellenőrzik a csatlakozást a célhelyekhez. A TCP vagy az ICMP protokoll esetében a sikertelen ellenőrzések százalékos aránya a csomagok elvesztésének százalékos arányát is elérheti. HTTP protokoll esetén ez a mező a nem válaszoló HTTP-kérelmek százalékos arányát jelöli.
        * **roundTripTimeMs**: azt határozza meg, hogy a források mennyi ideig tarthatnak a célhoz a tesztelési konfigurációban ezredmásodpercben.

## <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 20

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan elemezheti a figyelési információkat, és hogyan állíthat be riasztásokat](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts).
* Ismerje meg [, hogyan diagnosztizálhatja a hálózattal kapcsolatos problémákat](./connection-monitor-overview.md#diagnose-issues-in-your-network).
