---
title: Migrálás az Azure Application Gateway a beáramlási vezérlő élén a AGIC bővítménybe
description: Ez a cikk útmutatást nyújt arról, hogyan lehet migrálni a Helm használatával központilag telepített AGIC az AGIC-bővítménybe
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050834"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migrálás a AGIC Helm-ből a AGIC-bővítménybe 

Ha a AGIC már telepítve van a Helm-on keresztül, de az AK-bővítményként üzembe helyezett AGIC kíván migrálni, az alábbi lépések segítséget nyújtanak az áttelepítési folyamaton. 

## <a name="prerequisites"></a>Előfeltételek 
Az áttelepítési folyamat megkezdése előtt néhány dolgot ellenőriznie kell. 
  - A AGIC Helm olyan szolgáltatásait használja, amelyek [jelenleg nem támogatottak a AGIC bővítménnyel](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)?
  - Több AGIC Helm-telepítést használ egy AK-fürtön? 
  - Több AGIC Helm-telepítést használ egy Application Gateway megcélzásához? 

Ha az Igen értékre válaszol a fenti kérdésekre, a AGIC bővítmény nem támogatja a használati esetet, így az a legjobb megoldás, ha továbbra is a AGIC Helm használatát fogja használni. Ellenkező esetben folytassa az alábbi áttelepítési folyamattal a munkaidőn kívüli időpontokban. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Azon Application Gateway erőforrás-azonosító megkeresése, amelyet a AGIC Helm jelenleg céloz 
Navigáljon ahhoz a Application Gatewayhoz, amelyet a AGIC Helm üzembe helyezése céloz. Másolja és mentse az adott Application Gateway erőforrás-AZONOSÍTÓját. Egy későbbi lépésben szüksége lesz az erőforrás-AZONOSÍTÓra. Az erőforrás-azonosító a portálon, a Application Gateway Tulajdonságok lapján vagy az Azure CLI-n keresztül érhető el. A következő példa menti a Application Gateway erőforrás-azonosítót a *appgwId* nevű átjáróra a *myResourceGroup* erőforráscsoport *myApplicationGateway* .

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>AGIC-Helm törlése az AK-fürtből
Az Azure CLI-n keresztül törölje a AGIC Helm telepítését a fürtből. Először törölnie kell a AGIC Helm-telepítést, mielőtt engedélyezi a AGIC AK-bővítményt. Vegye figyelembe, hogy a AGIC Helm-környezet törlésének időpontja és az AGIC-bővítmény engedélyezésének időpontja közötti változás nem tükröződik a Application Gatewayon, ezért az áttelepítési folyamatot a munkaidőn kívül kell elvégezni, hogy a lehető legkevesebb hatást csökkentse. A Application Gateway továbbra is a AGIC által alkalmazott utolsó konfigurációt fogja alkalmazni, így a meglévő útválasztási szabályok nem lesznek hatással. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>AGIC-bővítmény engedélyezése meglévő Application Gateway használatával 
Mostantól engedélyezheti a AGIC-bővítményt az AK-fürtben, hogy a meglévő Application Gateway az Azure CLI-n vagy a portálon keresztül célozza meg. Futtassa az alábbi Azure CLI-parancsot, hogy engedélyezze a AGIC-bővítményt az AK-fürtben. A példa a *myCluster* nevű fürtben engedélyezi a bővítményt egy *myResourceGroup* nevű erőforráscsoport esetében, amely Application Gateway az előző lépésben a fentiekben mentett *appgwId* erőforrás-azonosítót használja. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Azt is megteheti, hogy a portálon megnyithatja az AK-fürtöt [, és](https://portal.azure.com/?feature.aksagic=true) engedélyezheti a AGIC bővítményt a fürt hálózatkezelés lapján. Válassza ki a meglévő Application Gateway a legördülő menüből, amikor kiválasztja, hogy melyik Application Gateway kell megcéloznia a bővítményt. 

![Application Gateway bejövő adatkezelő portál](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Következő lépések
- A [**bejövő Application Gateway-vezérlővel kapcsolatos hibaelhárítás**](ingress-controller-troubleshoot.md): a AGIC hibaelhárítási útmutatója 
- [**Bejövő Application Gateway-vezérlő megjegyzései**](ingress-controller-annotations.md): a AGIC található jegyzetek listája 