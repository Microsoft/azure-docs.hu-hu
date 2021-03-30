---
title: Klasszikus Azure-beli üzembe helyezési erőforrások áthelyezése
description: A Azure Resource Manager használatával helyezheti át a klasszikus üzembe helyezési erőforrásokat egy új erőforráscsoporthoz vagy előfizetésbe.
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: 78b9769a31fa0c96c12e18d05cb9c484aa52a1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75485285"
---
# <a name="move-guidance-for-classic-deployment-model-resources"></a>Útmutató a klasszikus üzemi modell erőforrásainak üzembe helyezéséhez

A klasszikus modellen keresztül üzembe helyezett erőforrások áthelyezésének lépései attól függően változnak, hogy egy előfizetésen belül vagy egy új előfizetésben helyezi át az erőforrásokat.

## <a name="move-in-the-same-subscription"></a>Áthelyezés ugyanabba az előfizetésbe

Ha az erőforrásokat az egyik erőforráscsoporthoz egy másik erőforráscsoporthoz helyezi át ugyanazon az előfizetésen belül, a következő korlátozások érvényesek:

* A virtuális hálózatok (klasszikus) nem helyezhetők át.
* A virtuális gépeket (klasszikus) át kell helyezni a Cloud Service-be.
* A Cloud Service csak akkor helyezhető át, ha az áthelyezés magában foglalja az összes virtuális gépet.
* Egyszerre csak egy felhőalapú szolgáltatás helyezhető át.
* Egyszerre csak egy Storage-fiók (klasszikus) helyezhető át.
* A Storage-fiók (klasszikus) nem helyezhető át ugyanabban a műveletben egy virtuális géppel vagy egy felhőalapú szolgáltatással.

Ha a klasszikus erőforrásokat egy új erőforráscsoporthoz szeretné áthelyezni ugyanabba az előfizetésbe, használja a [standard Move műveletet](../move-resource-group-and-subscription.md) a portálon, Azure PowerShellon, az Azure CLI-n vagy a REST APIon keresztül. Ugyanazokat a műveleteket használja, mint a Resource Manager-erőforrások áthelyezéséhez.

## <a name="move-across-subscriptions"></a>Áthelyezés az előfizetések között

Az erőforrások új előfizetésre való áthelyezésekor a következő korlátozások érvényesek:

* Az előfizetésben lévő összes klasszikus erőforrást ugyanabban a műveletben kell áthelyezni.
* A cél-előfizetés nem tartalmazhat más klasszikus erőforrásokat.
* Az áthelyezést csak külön REST API keresztül lehet kérni klasszikus Áthelyezésekhez. A hagyományos Resource Manager-áthelyezési parancsok nem működnek, ha a klasszikus erőforrásokat új előfizetésre helyezi át.

A klasszikus erőforrások új előfizetésre való áthelyezéséhez használja a klasszikus erőforrásokra jellemző REST-műveleteket. A REST használatához hajtsa végre a következő lépéseket:

1. Ellenőrizze, hogy a forrás-előfizetés részt vehet-e az előfizetések közötti áthelyezésben. Használja a következő műveletet:

   ```HTTP
   POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     A kérelem törzsében adja meg a következőket:

   ```json
   {
    "role": "source"
   }
   ```

     Az érvényesítési műveletre adott válasz formátuma a következő:

   ```json
   {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
   }
   ```

1. Ellenőrizze, hogy a cél-előfizetés részt vehet-e az előfizetések közötti áthelyezésben. Használja a következő műveletet:

   ```HTTP
   POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
   ```

     A kérelem törzsében adja meg a következőket:

   ```json
   {
    "role": "target"
   }
   ```

     A válasz formátuma megegyezik a forrás-előfizetés ellenőrzésével.
1. Ha mindkét előfizetés átadja az ellenőrzést, helyezze át az összes klasszikus erőforrást egy előfizetésből egy másik előfizetésbe a következő művelettel:

   ```HTTP
   POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
   ```

    A kérelem törzsében adja meg a következőket:

   ```json
   {
    "target": "/subscriptions/{target-subscription-id}"
   }
   ```

A művelet több percig is futhat.

## <a name="next-steps"></a>Következő lépések

Ha problémája van a klasszikus erőforrások áthelyezésekor, forduljon az [ügyfélszolgálathoz](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Az erőforrások áthelyezésére szolgáló parancsokért lásd: [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../move-resource-group-and-subscription.md).
