---
title: Csere/váltás két Azure-Cloud Services között (bővített támogatás)
description: Csere/váltás két Azure-Cloud Services között (bővített támogatás)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: 6f96656af9afd9874cc6273a9cea9ed43e8c69cc
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294311"
---
# <a name="swapswitch-between-two-azure-cloud-services-extended-support"></a>Csere/váltás két Azure-Cloud Services között (bővített támogatás)
A Cloud Services szolgáltatással (kiterjesztett támogatással) két független felhőalapú szolgáltatás üzemelő példánya között lehet cserélni. A Cloud Services (klasszikus) szolgáltatástól eltérően a tárolóhelyek fogalma nem létezik a Azure Resource Manager modellel. Ha úgy dönt, hogy egy felhőalapú szolgáltatás új kiadását (kiterjesztett támogatás) helyezi üzembe, akkor a "lecserélhető" állapotot egy másik meglévő felhőalapú szolgáltatással (kiterjesztett támogatással) is elvégezheti, amely lehetővé teszi az új kiadás előkészítését és tesztelését ezzel az üzembe helyezéssel. A Cloud Service "cserélhető" lehet egy másik felhőalapú szolgáltatással a második felhőalapú szolgáltatás (pár) üzembe helyezésének időpontjában. Ha az ARM sablon alapú telepítési módszert használja, ezt a SwappableCloudService tulajdonságnak a Cloud Service-objektum hálózati profilján belüli beállításával, a párosított felhőalapú szolgáltatás AZONOSÍTÓjának használatával végezheti el. 

```
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
```
> [!Note] 
> A Cloud Service (klasszikus) és a Cloud Service (kiterjesztett támogatás) között nem lehet cserélni.

Használja a **swap** szolgáltatást a két felhőalapú szolgáltatás által kezelt URL-címek átváltásához, ami hatással van egy új felhőalapú szolgáltatás (szakaszos) éles kiadásra való előléptetésére.
A központi telepítéseket a Cloud Services vagy az irányítópulton cserélheti le.

1. A [Azure Portal](https://portal.azure.com)válassza ki a frissíteni kívánt felhőalapú szolgáltatást. Ez a lépés megnyitja a Cloud Service-példány panelt.
2. A panelen válassza a **képcserélés** 
    :::image type="content" source="media/swap-cloud-service-1.png" alt-text="lehetőséget a Cloud Service swap lehetőségének megjelenítése":::
   
3. Megnyílik a következő megerősítő üzenet
   
   :::image type="content" source="media/swap-cloud-service-2.png" alt-text="A rendszerkép a Cloud Service cseréjét mutatja be":::
   
4. Az üzembe helyezési információk ellenőrzése után kattintson az OK gombra az üzemelő példányok kicseréléséhez.
A csere gyorsan történik, mert az egyetlen dolog, ami megváltoztatja a két felhőalapú szolgáltatás virtuális IP-címeit (VIP).

A számítási költségek mentéséhez törölheti az egyik felhőalapú szolgáltatást (amely átmeneti környezetként van kijelölve az alkalmazás telepítéséhez), miután meggyőződött arról, hogy a felcserélt felhőalapú szolgáltatás a várt módon működik.

A REST API, amely a "swap" műveletet hajtja végre két felhőalapú szolgáltatás kiterjesztett támogatási környezete között, az alábbiakban látható:
```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```
```
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
```
## <a name="common-questions-about-swapping-deployments"></a>Gyakori kérdések az üzemelő példányok cseréjével kapcsolatban

### <a name="what-are-the-prerequisites-for-swapping-between-two-cloud-services"></a>Mik a két felhőalapú szolgáltatás közötti váltás előfeltételei?
A felhőalapú szolgáltatás (kiterjesztett támogatás) sikeres felváltásának két fő előfeltétele van:
* Ha az egyik cserélhető felhő-szolgáltatáshoz statikus/fenntartott IP-címet szeretne használni, a másik felhőalapú szolgáltatásnak fenntartott IP-címet is használnia kell. Ellenkező esetben a swap sikertelen lesz.
* A felcserélés végrehajtása előtt a szerepkörök összes példányának futnia kell. A példányok állapotát a Azure Portal áttekintés paneljén tekintheti meg. Másik lehetőségként használhatja a Get-AzRole parancsot a Windows PowerShellben.

A vendég operációs rendszer frissítései és a szolgáltatás-javító műveletek miatt a telepítési swap-feladatok sikertelenek lehetnek. További információ: a Cloud Service üzembe helyezési problémáinak elhárítása.

### <a name="can-i-perform-a-vip-swap-in-parallel-with-another-mutating-operation"></a>Párhuzamosan is elvégezhető VIP-csere egy másik mutációs művelettel?
Nem. A VIP-swap szolgáltatás csak olyan hálózatkezelési változást tartalmaz, amelyet el kell végeznie ahhoz, hogy más számítási műveletet hajtson végre a Cloud Service (k) on. Frissítési, törlési vagy automatikus skálázási művelet végrehajtása a Cloud Service-ben, miközben egy VIP-swap folyamatban van, vagy VIP-swapot indít el, míg egy másik számítási művelet folyamatban van, a Cloud Service-t nem kívánt állapotba hagyhatja, amelyből előfordulhat, hogy a helyreállítás nem lehetséges. 

### <a name="does-a-swap-incur-downtime-for-my-application-how-should-i-handle-it"></a>Felmerül a swap-állásidő az alkalmazásom esetében? Hogyan kezelhető?
Az előző szakaszban leírtaknak megfelelően a Cloud Service swap általában gyors, mert csak az Azure Load Balancer konfigurációjának változása. Bizonyos esetekben 10 vagy több másodpercet is igénybe vehet, és átmeneti csatlakoztatási hibákat eredményezhet. Az ügyfelekre gyakorolt hatás korlátozásához érdemes lehet megvalósítani az ügyfél újrapróbálkozási logikáját.

## <a name="next-steps"></a>Következő lépések 
- Tekintse át a Cloud Services [üzembe helyezésének előfeltételeit](deploy-prerequisite.md) (kiterjesztett támogatás).
- Tekintse át a Cloud Servicesra vonatkozó [gyakori kérdéseket](faq.md) (kiterjesztett támogatás).
- A [Azure Portal](deploy-portal.md), a [PowerShell](deploy-powershell.md), a [sablon](deploy-template.md) vagy a [Visual Studio](deploy-visual-studio.md)használatával üzembe helyezhet egy felhőalapú szolgáltatást (kiterjesztett támogatás).
