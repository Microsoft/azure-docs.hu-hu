---
title: Mi az Azure Application Gateway inbehatolás vezérlő?
description: Ez a cikk bemutatja, hogy milyen Application Gateway beléptetési vezérlő.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714408"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Mi az Application Gateway beáramló vezérlő?
A Application Gateway beáramlási vezérlő (AGIC) egy Kubernetes-alkalmazás, amely lehetővé teszi, hogy az [Azure Kubernetes Service (ak)](https://azure.microsoft.com/services/kubernetes-service/) ügyfelei kihasználják az azure natív [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 Load-balancert a felhőalapú szoftverek interneten való elérhetővé tételéhez. A AGIC figyeli az üzemeltetett Kubernetes-fürtöt, és folyamatosan frissíti egy Application Gateway, hogy a kiválasztott szolgáltatások elérhetők legyenek az internethez.

A bejövő vezérlő a saját Pod-on fut az ügyfél AK-ban. A AGIC a Kubernetes-erőforrások egy részhalmazát figyeli a változásokhoz. Az AK-fürt állapota Application Gateway meghatározott konfigurációra van lefordítva, és a [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md)alkalmazásra lett alkalmazva.

## <a name="benefits-of-application-gateway-ingress-controller"></a>A Application Gateway beáramlási vezérlő előnyei
A AGIC segít kiküszöbölni, hogy szükség van-e egy másik terheléselosztó/nyilvános IP-cím létrehozására az AK-fürt előtt, és elkerüli a több ugrást a DataPath, mielőtt a kérelmek elérnék az AK-fürtöt. A Application Gateway a saját saját IP-címével beszél a hüvelyekről, és nem igényli a NodePort vagy a KubeProxy szolgáltatás használatát. Ez az üzembe helyezések jobb teljesítményét is lehetővé teszi.

A beáramló vezérlőt kizárólag Standard_v2 és WAF_v2 SKU támogatja, ami az automatikus skálázási előnyökkel is jár. Application Gateway reagálhat a forgalom terhelésének növekedésére vagy csökkenésére, és ennek megfelelően méretezhető, anélkül, hogy az AK-fürtből erőforrásokat kellene használnia.

A AGIC mellett a Application Gateway használatával a TLS-házirend és a webalkalmazási tűzfal (WAF) funkciójának segítségével is megvédheti az AK-fürtöt.

![Azure Application Gateway + AK](./media/application-gateway-ingress-controller-overview/architecture.png)

A AGIC a Kubernetes beáramlási [erőforrással](https://kubernetes.io/docs/user-guide/ingress/), valamint a szolgáltatás és az üzembe helyezések/hüvelyek használatával konfigurálható. Számos funkciót kínál, az Azure natív Application Gateway L7 terheléselosztó használatával. Csak néhányat említsünk:
  - URL-útválasztás
  - Cookie-alapú affinitás
  - TLS-lezárás
  - Végpontok közötti TLS
  - Nyilvános, privát és hibrid webhelyek támogatása
  - Integrált webalkalmazási tűzfal

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>A Helm üzembe helyezése és az AK Add-On közötti különbség
Kétféle módon telepíthet AGIC az AK-fürthöz. Az első módszer a Helm; a második az AK-n keresztül bővítmény. Az AGIC-k AK-bővítményként való üzembe helyezésének elsődleges előnye, hogy sokkal egyszerűbb, mint a Helmon keresztül történő üzembe helyezés. Új telepítés esetén telepíthet egy új Application Gatewayt és egy új AK-fürtöt, amely AGIC engedélyezhető az Azure CLI egyik sorában. A bővítmény egy teljes körűen felügyelt szolgáltatás is, amely további előnyöket biztosít, például az automatikus frissítéseket és a megnövekedett támogatást. A Microsoft teljes mértékben támogatja a AGIC (Helm és AK bővítmény) üzembe helyezésének mindkét módját. Emellett a bővítmény lehetővé teszi az AK-nal való jobb integrációt az első osztályú bővítményként.

A AGIC-bővítmény továbbra is Pod-ként van telepítve az ügyfél AK-beli fürtjében, de van néhány különbség a Helm üzembe helyezési verziója és a AGIC kiegészítő verziója között. Az alábbi lista a két verzió közötti különbségeket sorolja fel: 
  - A Helm központi telepítési értékei nem módosíthatók az AK bővítményben:
    - `verbosityLevel` Alapértelmezés szerint 5 értékre lesz állítva
    - `usePrivateIp` Alapértelmezés szerint hamis értékre lesz állítva. Ezt felülírhatja a [use-Private-IP Megjegyzés](ingress-controller-annotations.md#use-private-ip)
    - `shared` a bővítmény nem támogatja 
    - `reconcilePeriodSeconds` a bővítmény nem támogatja
    - `armAuth.type` a bővítmény nem támogatja
  - A Helm által központilag telepített AGIC támogatja a ProhibitedTargets-t, ami azt jelenti, hogy a AGIC a többi meglévő háttérrendszer befolyásolása nélkül konfigurálhatja a Application Gateway. A AGIC bővítmény jelenleg nem támogatja ezt. 
  - Mivel a AGIC-bővítmény felügyelt szolgáltatás, a rendszer automatikusan frissíti az ügyfeleket a AGIC-bővítmény legújabb verziójára, a AGIC-on keresztül központilag telepített, az ügyfél által manuálisan frissíteni kívánt AGIC. 

> [!NOTE]
> Az ügyfelek csak egy AGIC-bővítményt telepíthetnek, és az egyes AGIC-bővítmények jelenleg csak egy Application Gateway tudnak megcélozni. Olyan központi telepítések esetén, amelyeknek több AGIC vagy több AGICs kell megcéloznia egy Application Gateway, továbbra is használja a Helm használatával telepített AGIC. 

## <a name="next-steps"></a>Következő lépések
- [**Ak Add-On Greenfield üzembe helyezése**](tutorial-ingress-controller-add-on-new.md): útmutatás a AGIC-bővítmény, az AK és a Application Gateway telepítéséhez az üres-pala infrastruktúrán.
- [**Ak Add-On rozsdaövezetek rehabilitálása üzembe helyezése**](tutorial-ingress-controller-add-on-existing.md): telepítse a AGIC bővítményt egy AK-fürtön egy meglévő Application Gateway.
- [**Helm Greenfield üzembe helyezése**](ingress-controller-install-new.md): telepítse a AGIC-t a Helm, az új AK-fürt és az új Application Gateway az üres-pala infrastruktúrán keresztül.
- [**Helm rozsdaövezetek rehabilitálása üzembe helyezése**](ingress-controller-install-existing.md): AGIC üzembe helyezése egy meglévő AK-fürtön és Application Gateway.