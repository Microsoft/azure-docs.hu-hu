---
title: Az Azure-régiókhoz tartozó relatív késések megtekintése adott helyekről
description: Megtudhatja, hogyan tekintheti meg az internetes szolgáltatók relatív késéseit adott helyekről származó Azure-régiók között.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 938315dac80ae51984851083753a9cb8f77662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737903"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>Adott helyek és Azure-régiók közötti relatív késés megtekintése

> [!WARNING]
> Ez a funkció jelenleg előzetes verzióban érhető el, és még folyamatban van a stabilitás tesztelése.

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure [Network Watcher](network-watcher-monitoring-overview.md) szolgáltatást, hogy eldöntse, milyen Azure-régiót helyez üzembe az alkalmazás vagy szolgáltatás a-ben a felhasználó demográfiai adatai alapján. Emellett a segítségével kiértékelheti a szolgáltatók kapcsolatait az Azure-ban.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Network Watcher létrehozása

Ha már rendelkezik hálózati figyelővel legalább egy Azure- [régióban](https://azure.microsoft.com/regions), kihagyhatja a jelen szakasz feladatait. Hozzon létre egy erőforráscsoportot a Network Watcher számára. Ebben a példában az erőforráscsoport az USA keleti régiójában jön létre, de az erőforráscsoport bármely Azure-régióban létrehozható.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Hozzon létre egy hálózati figyelőt. Legalább egy Azure-régióban létre kell hoznia egy hálózati figyelőt. Ebben a példában egy Network Watcher jön létre az USA keleti régiójában.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Relatív hálózati késések összehasonlítása egyetlen Azure-régióval egy adott helyről

Kiértékelheti a szolgáltatókat, vagy egy olyan felhasználót érintő hibát, amely egy olyan problémát jelentett, mint például a "a hely lassú volt", egy adott helyről az Azure-régióba, ahol a szolgáltatás telepítve van. A következő parancs például a Egyesült Államok és az USA 2. nyugati régiójában lévő, a 2017. december 13-15. között található Washington államban az átlagosan használt internetes szolgáltatói késéseket adja vissza.

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Az előző parancsban megadott régiónak nem kell megegyeznie a Network Watcher beolvasásakor megadott régióval. Az előző parancshoz egyszerűen meg kell adnia egy meglévő hálózati figyelőt. A Network Watcher bármely régióban lehet. Ha a és a értékeit `-Country` `-State` is megadja, érvényesnek kell lennie. Az értékek megkülönböztetik a kis-és nagybetűket. Az adatmennyiség korlátozott számú országban, régióban, államban és városban is elérhető. Futtassa a parancsokat az [elérhető országok/régiók, Államok, városok és szolgáltatók nézetben](#view-available) , és tekintse meg az előző paranccsal használni kívánt országok/régiók, városok és Államok listáját. 

> [!WARNING]
> A és a esetében az elmúlt 30 napban kell megadnia a dátumot `-StartTime` `-EndTime` . Egy korábbi dátum megadásával a rendszer nem ad vissza adatvesztést.

Az előző parancs kimenete a következő:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

A visszaadott kimenetben a **pontszám** értéke a különböző régiók és szolgáltatók relatív késése. Az 1 pontszám a legrosszabb (legmagasabb) késés, míg a 100 a legalacsonyabb késés. A relatív késések a napi átlagra vonatkoznak. Az előző példában látható, hogy habár a késések mindkét nap megegyeznek, és a két szolgáltató késése között kis különbség van, azt is egyértelművé teszi, hogy mindkét szolgáltatónál a késések is alacsonyak az 1-100 skálán. Habár ez várható, mivel a Egyesült Államok Washington állama fizikailag az USA 2. nyugati régiójában, az Azure-régióban található, időnként nem várt eredmények. Minél nagyobb a megadott dátumtartomány, annál több időt vehet igénybe az átlagos késleltetés.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Relatív hálózati késések összehasonlítása az Azure-régiók között egy adott helyről

Ha egy adott hely és egy adott Azure-régió közötti relatív késések meghatározása helyett a-t használja `-Location` , akkor azt is megteheti, hogy az adott fizikai helyről az összes Azure-régióhoz viszonyított késést meghatározta. Például a következő parancs segítségével kiértékelheti, hogy milyen Azure-régiót kell üzembe helyezni a szolgáltatásban, ha az elsődleges felhasználók a Washington államban található Comcast-felhasználók:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Ha egyetlen helyet ad meg, és nem ad meg helyet, vagy több helyet ad meg, például "West RÉGIÓJA", "West US", meg kell adnia egy internetszolgáltatót a parancs futtatásakor. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Az elérhető országok/régiók, Államok, városok és szolgáltatók megtekintése

Az egyes internetszolgáltatók, országok/régiók, Államok és városok számára is elérhetők az egyes szolgáltatások. Az összes elérhető internetszolgáltató, ország/régió, állam és város listájának megtekintéséhez írja be a következő parancsot:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Az adatai csak az előző parancs által visszaadott országok/régiók, Államok és városok esetében érhetők el. Az előző parancshoz meg kell adnia egy meglévő hálózati figyelőt. A példában a *NetworkWatcher_eastus* Network Watcher egy *NetworkWatcherRG* nevű erőforráscsoporthoz lett megadva, de megadhatja a meglévő hálózati figyelőt. Ha nem rendelkezik meglévő hálózati figyelővel, hozzon létre egyet a [Network Watcher létrehozása](#create-a-network-watcher)című témakörben ismertetett feladatok végrehajtásával. 

Az előző parancs futtatása után szűrheti a visszaadott kimenetet, ha szükséges, az **ország**, az **állam** és a **város** érvényes értékének megadásával.  Ha például a Seattle-ben elérhető internetszolgáltatók listáját szeretné megtekinteni a Egyesült Államokban, írja be a következő parancsot:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> Az **ország** számára megadott értéknek kis-és nagybetűnek kell lennie. Az **állapot** és a **város** számára megadott értékeknek kisbetűnek kell lenniük. Az értékeknek szerepelniük kell a megadott kimenetben a parancs futtatása után az **ország**, **állam** és **város** érték nélkül. Ha a helytelen esetet adja meg, vagy olyan értéket ad meg az **ország**, **állam** vagy **város** számára, amely nem szerepel az adott tulajdonság értékeit nem tartalmazó parancs futtatása után, akkor a visszaadott kimenet üres.
