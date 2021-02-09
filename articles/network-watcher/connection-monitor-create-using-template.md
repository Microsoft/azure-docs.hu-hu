---
title: Ügyfélkapcsolat-figyelő – ARM-sablon létrehozása
titleSuffix: Azure Network Watcher
description: Megtudhatja, hogyan hozhat létre ARMClient a kapcsolódási figyelő használatával.
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
ms.openlocfilehash: 46bdaf932d4224bf97b46e7713d49d815ca1bcdd
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832997"
---
# <a name="create-a-connection-monitor-using-the-arm-template"></a>Figyelő létrehozása ARM-sablonnal

> [!IMPORTANT]
> 2021. július 1-től nem lehet új teszteket felvenni egy meglévő munkaterületre, vagy új munkaterületet engedélyezni Network Performance Monitor. Emellett nem adhat hozzá új kapcsolódási figyelőket a Csatlakozáskezelő (klasszikus) szolgáltatáshoz. Továbbra is használhatja a 2021. július 1. előtt létrehozott teszteket és kapcsolódási figyelőket. A szolgáltatás megszakadásának minimalizálásához a jelenlegi számítási [feladatokhoz telepítse át a teszteket Network Performance monitor ](migrate-to-connection-monitor-from-network-performance-monitor.md) vagy  [migrálja a kapcsolódási figyelőről (klasszikus)](migrate-to-connection-monitor-from-connection-monitor-classic.md) az Azure-Network Watcher új, a 2024. február 29. előtti kapcsolódási figyelője felé.

Ismerje meg, hogyan hozhat létre kapcsolati figyelőt az erőforrások közötti kommunikáció figyeléséhez a ARMClient használatával. Támogatja a hibrid és az Azure-beli Felhőbeli üzembe helyezést.


## <a name="before-you-begin"></a>Előkészületek 

A Csatlakozáskezelőben létrehozott kapcsolódó figyelők esetében a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A Csatlakozáskezelő a következő entitásokat tartalmazza:

* **Csatlakozáskezelő erőforrás** – régióra jellemző Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Endpoint (végpont** ) – a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Ilyen végpontok például az Azure-beli virtuális gépek, a helyszíni ügynökök, az URL-címek és az IP-címek.
* **Teszt konfiguráció** – egy adott protokollra jellemző konfiguráció. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Test Group (tesztelési csoport** ) – a forrás-végpontokat, a célként megadott végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Test (teszt** ) – a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

    ![A kapcsolati figyelőt bemutató diagram, a tesztelési csoportok és tesztek közötti kapcsolat meghatározása](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>A minta ARM-sablonnal létrehozandó lépések

A következő kód használatával hozzon létre egy ARMClient a kapcsolódási figyelő használatával.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'endpoint_workspace_machine',

type: 'MMAWorkspaceMachine',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

//Example 1: Choose a machine

address : '<non-Azure machine FQDN>'
}

//Example 2: Select IP from chosen machines

address : '<non-Azure machine FQDN>

"scope": {
      "include": [
            {
                  "address": "<IP belonging to machine chosen above>"  
        }
       ]
      }
   }    
   
name: 'endpoint_workspace_network',

type: 'MMAWorkspaceNetwork',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

 coverage level : 'high', //Optional
 
 //Include subnets. You can also exclude IPs from subnet to exclude from monitoring
 
 scope: {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28
            },
            {
                  "address": "<subnet 2 mask>" 
            }
      ],
      "exclude": [
            { 
            "address" : "<ip-from-included-subnets-that-should-be-excluded>"
        }
      ]
     }
},

//Use a Azure VM as an endpoint
{

name: 'endpoint_virtualmachine',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

//Use an Azure VNET or Subnet as an endpoint

 {

name: 'endpoint_vnet_subnet',

resourceId: '<resource id of VNET or subnet'
coverage level: 'high' //Optional

//Scope is optional.

  "scope": {
      "include": [
            {
                  "address": "<subnet 1 mask>" // Eg: 10.10.1.0/28 .This subnet should match with any existing subnet in vnet
            }
      ],
    "exclude": [
            {
                  "address": "<ip-from-included-subnets-that-should-be-excluded>" // If used with include, IP should be part of the subnet defined above. Without include, this could be any address within vnet range or any specific subnet range as a whole.
            }
      ]
  }
   },

//Endpoint as a URL
{

name: 'azure portal'

address: '<URL>'

   },

//Endpoint as an IP 
 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {
    
     port: '<port of choice>'
  
    preferHTTPS: true // If port chosen is not 80 or 443
    
    method: 'GET', //Choose GET or POST
    
    path: '/', //Specify path for request
         
    requestHeaders: [
            {
              "name": "Content-Type",
              "value": "appication/json"
            }
          ],
          
    validStatusCodeRanges: [ "102", "200-202", "3xx" ], //Samples
          
    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, 
   {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Az üzembe helyezési parancs a következő:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Tulajdonságok leírása

* connectionMonitorName – a Csatlakozáskezelő erőforrás neve

* Annak az előfizetésnek az előfizetési azonosítója, amelyben a kapcsolódási figyelőt létre szeretné hozni

* NW – Network Watcher erőforrás-azonosító, amelyben a rendszer létrehozza a CM-t 

* hely – régió, amelyben a rendszer létrehozza a figyelőt

* Végpontok
    * név – az egyes végpontok egyedi neve
    * resourceId – az Azure-végpontok esetében az erőforrás-azonosító a virtuális gépek Azure Resource Manager erőforrás-AZONOSÍTÓJÁRA hivatkozik. Nem Azure-végpontok esetén az erőforrás-azonosító a nem Azure-ügynökökhöz társított Log Analytics munkaterület Azure Resource Manager erőforrás-AZONOSÍTÓját jelöli.
    * címe – csak akkor alkalmazható, ha nincs megadva erőforrás-azonosító, vagy ha az erőforrás-azonosító Log Analytics munkaterület. Ha Log Analytics erőforrás-AZONOSÍTÓval használja, akkor a figyeléshez használható ügynök teljes tartománynevére utal. Ha erőforrás-azonosító nélkül használja, akkor ez lehet bármely nyilvános végpont URL-címe vagy IP-címe.
    * szűrő – nem Azure-végpontok esetén a szűrő használatával válassza ki az ügynököket Log Analytics munkaterületről, amelyet a rendszer a Csatlakozáskezelő erőforrás figyeléséhez fog használni. Ha a szűrők nincsenek beállítva, akkor a Log Analytics munkaterülethez tartozó összes ügynök használható figyelésre.
        * típus – típus megadása "ügynök címe"
        * címterület – a helyszíni ügynök teljes tartománynevének megadása

* Tesztelési csoportok
    * név – a tesztelési csoport neve.
    * testConfigurations – tesztelési konfigurációk, amelyek alapján a forrás-végpontok a cél végpontokhoz csatlakoznak
    * források – válasszon a fent létrehozott végpontok közül. Az Azure-alapú forrás-végpontoknak telepíteniük kell az Azure Network Watcher bővítményt, és a nem Azure-alapú forrás-végpontoknak telepíteniük kell a haveAzure Log Analytics-ügynököt. A forráshoz tartozó ügynök telepítéséhez tekintse meg a [figyelési ügynökök telepítése](./connection-monitor-overview.md#install-monitoring-agents)című témakört.
    * célhelyek – válasszon a fent létrehozott végpontok közül. Az Azure-beli virtuális gépekhez vagy bármely végponthoz (nyilvános IP-címhez, URL-címhez vagy FQDN-hez) való csatlakozást célhelyként megadható módon figyelheti. Egyetlen tesztelési csoportban Azure-beli virtuális gépeket, Office 365 URL-címeket, Dynamics 365 URL-címeket és egyéni végpontokat adhat hozzá.
    * letiltás – ezzel a mezővel letilthatja az összes olyan forrás és cél figyelését, amelyet a tesztelési csoport meghatároz.

* Tesztelési konfigurációk
    * név – a teszt konfigurációjának neve.
    * testFrequencySec – azt határozza meg, hogy a források milyen gyakran fognak pingelni a protokollon és a megadott porton. 30 másodpercet, 1 percet, 5 percet, 15 percet vagy 30 percet is kiválaszthat. A források a választott érték alapján próbálják meg a célokhoz való kapcsolódást. Ha például 30 másodpercet választ, a források legalább egyszer ellenőrizhetik a kapcsolatot a célhoz, 30 másodpercen belül.
    * protokoll – a TCP, az ICMP, a HTTP vagy a HTTPS lehetőség közül választhat. A protokolltól függően egyes protokoll-specifikus konfigurációk is megadhatók
    
        * preferHTTPS – adja meg, hogy HTTPS protokollt használ-e HTTP-n keresztül, ha a használt port értéke sem 80, sem 443
        * Port – adja meg az Ön által választott cél portot.
        * disableTraceRoute – olyan tesztelési konfigurációkra vonatkozik, amelyek TCP vagy ICMP protokollal rendelkeznek. Leállítja a forrásokat a topológia és a hop-by-hop RTT felfedéséhez.
        * metódus – a rendszer a HTTP protokollt használó tesztelési konfigurációkat alkalmazza. Válassza ki a HTTP-kérelem módszert – vagy GET vagy POST
        * elérési út – az URL-címhez hozzáfűzni kívánt elérésiút-paraméterek megadása
        * validStatusCodes – válassza ki a megfelelő állapotkódot. Ha a válasz kódja nem egyezik a listával, a rendszer diagnosztikai üzenetet kap
        * requestHeaders – adja meg az egyéni kérelmek fejlécének azon karakterláncait, amelyeket át kell adni a célhelynek
        
    * successThreshold – a következő hálózati paraméterek küszöbértékeit állíthatja be:
        * checksFailedPercent – az ellenőrzések százalékos arányának beállítása, ha a forrás a megadott feltételek alapján ellenőrzi a csatlakozást a célhelyekhez. A TCP vagy az ICMP protokoll esetében a sikertelen ellenőrzések százalékaránya a csomagok elvesztésének százalékában is egyenlő lehet. HTTP protokoll esetén ez a mező a nem válaszoló HTTP-kérelmek százalékos arányát jelöli.
        * roundTripTimeMs – állítsa be a RTT ezredmásodpercben, hogy mennyi ideig tart a források kapcsolódása a célhoz a tesztelési konfiguráción keresztül.

## <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 20 ARMClient-n keresztül

## <a name="next-steps"></a>Következő lépések

* Ismerje meg [, hogyan elemezheti a figyelési és riasztási információkat](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Útmutató a [hálózatban felmerülő problémák diagnosztizálásához](./connection-monitor-overview.md#diagnose-issues-in-your-network)
