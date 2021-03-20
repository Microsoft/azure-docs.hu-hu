---
title: Az Azure Kubernetes Service-fürthöz tartozó Application Gateway beáramlási vezérlő bővítményének letiltása és újbóli engedélyezése
description: Ez a cikk tájékoztatást nyújt arról, hogyan lehet letiltani és újból engedélyezni a AGIC-bővítményt az AK-fürthöz
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: fe4da0435731c536a723cb2cb43428166456360b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807954"
---
# <a name="disable-and-re-enable-agic-add-on-for-your-aks-cluster"></a>Az AGIC-bővítmény letiltása és újbóli engedélyezése az AK-fürthöz
Az AK-bővítményként telepített Application Gateway beléptetési vezérlő (AGIC) lehetővé teszi a bővítmény engedélyezését és letiltását egyetlen sorral az Azure CLI-ben. A Application Gateway életciklusa eltérő lesz a AGIC-bővítmény letiltásakor attól függően, hogy a Application Gateway a AGIC-bővítmény hozta-e létre, vagy ha az a AGIC-bővítménytől függetlenül lett telepítve. Ugyanezt a parancsot futtatva újra engedélyezheti a AGIC bővítményt, ha letiltotta, vagy ha a AGIC-bővítményt egy meglévő AK-fürtön és Application Gatewayon szeretné engedélyezni.

## <a name="disabling-agic-add-on-with-associated-application-gateway"></a>A AGIC-bővítmény letiltása a társított Application Gateway 
Ha a AGIC beépülő modul automatikusan központilag telepítette a Application Gatewayt, amikor először beállítja a mindent, akkor a AGIC bővítmény letiltásával alapértelmezés szerint egy pár feltétel alapján törli a Application Gateway. A AGIC-bővítmény két feltételt keres annak megállapításához, hogy a hozzá tartozó Application Gateway törli-e a Letiltáskor:
- A AGIC-bővítmény társítva van-e a MC_ * Node erőforráscsoporthoz? Application Gateway 
- A AGIC-bővítményhez társított Application Gateway rendelkezik a "created-by: inporting-appgw" címkével? A AGIC a címkét használja annak megállapítására, hogy a Application Gateway a bővítmény telepítette-e, vagy sem. 

Ha mindkét feltétel teljesül, a AGIC-bővítmény törli a bővítmény letiltásakor létrehozott Application Gateway. azonban nem törli a nyilvános IP-címet vagy azt az alhálózatot, amelyben a Application Gateway központilag telepítve lett. Ha az első feltételek nem teljesülnek, akkor nem számít, hogy a Application Gateway rendelkezik-e a "létrehozott: inappgw" címkével – a bővítmény letiltása nem törli a Application Gateway. Hasonlóképpen, ha a második feltétel nem teljesül, azaz a Application Gateway hiányzik a címke, akkor a bővítmény letiltása nem törli a Application Gateway a MC_ * Node erőforráscsoporthoz. 

> [!TIP] 
> Ha nem szeretné, hogy a rendszer törölje a Application Gateway a bővítmény letiltásakor, de mindkét feltételnek megfelel, akkor távolítsa el a "létrehozva: beáramló-appgw" címkét, hogy a bővítmény ne törölje a Application Gateway. 

A AGIC-bővítmény letiltásához futtassa a következő parancsot: 
```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a ingress-appgw 
```

## <a name="enable-agic-add-on-on-existing-application-gateway-and-aks-cluster"></a>AGIC-bővítmény engedélyezése meglévő Application Gateway és AK-fürtön
Ha letiltotta a AGIC bővítményt, és újra engedélyeznie kell a bővítményt, vagy egy meglévő Application Gateway-és AK-fürtön szeretné engedélyezni a bővítményt, futtassa a következő parancsot:

```azurecli-interactive
appgwId=$(az network application-gateway show -n <application-gateway-name> -g <resource-group-name> -o tsv --query "id") 
az aks enable-addons -n <AKS-cluster-name> -g <AKS-cluster-resource-group> -a ingress-appgw --appgw-id $appgwId
```

## <a name="next-steps"></a>Következő lépések
A AGIC-bővítmény meglévő Application Gateway és AK-fürttel való engedélyezésével kapcsolatos további információkért lásd: a [AGIC bővítmény rozsdaövezetek rehabilitálása telepítése](tutorial-ingress-controller-add-on-existing.md).