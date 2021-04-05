---
title: Az Azure Firewall használata az Azure Kubernetes Service (AKS) üzemelő példányainak védelmére
description: Ismerje meg, hogyan használhatja a Azure Firewallt az Azure Kubernetes-szolgáltatás (ak) üzembe helyezéséhez
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 01/11/2021
ms.author: victorh
ms.openlocfilehash: 51af9ff4972f5edef02426a6e81e8582123c9a7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98107854"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Az Azure Firewall használata az Azure Kubernetes Service (AKS) üzemelő példányainak védelmére

Az Azure Kubernetes Service (ak) felügyelt Kubernetes-fürtöt biztosít az Azure-ban. Ez csökkenti a Kubernetes kezelésének összetettségét és működési terhelését azáltal, hogy az Azure-ba való felelősség nagy részét kiszervezi. Az AK kritikus fontosságú feladatokat kezel, például az állapot figyelését és karbantartását, és egy nagyvállalati szintű és biztonságos fürtöt biztosít az egyszerűsített irányítással.

A Kubernetes összehangolja a virtuális gépek fürtöket, és a tárolókat a rendelkezésre álló számítási erőforrások és az egyes tárolók erőforrásaira vonatkozó követelmények alapján ütemezheti a virtuális gépeken való futtatásra. A tárolók hüvelybe vannak csoportosítva, a Kubernetes alapszintű működési egysége és a hüvelyek méretezése a kívánt állapotba.

A felügyeleti és működési célokra az AK-fürtök csomópontjainak bizonyos portokhoz és teljes tartománynevek (FQDN) eléréséhez kell rendelkezniük. Ezek a műveletek az API-kiszolgálóval való kommunikációra, illetve az alapvető Kubernetes-fürt összetevőinek és a csomópontok biztonsági frissítéseinek letöltésére és telepítésére is használhatók. Azure Firewall segíthet a környezet zárolásában és a kimenő forgalom szűrésében.

A jelen cikkben ismertetett útmutatást követve további védelmet biztosíthat az Azure Kubernetes-fürt számára a Azure Firewall használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy üzembe helyezett Azure Kubernetes-fürt futó alkalmazással.

   További információ: [oktatóanyag: Azure Kubernetes Service (ak) fürt üzembe helyezése](../aks/tutorial-kubernetes-deploy-cluster.md) és [oktatóanyag: alkalmazások futtatása az Azure Kubernetes Service-ben (ak)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>AK biztonságossá tétele

Azure Firewall a konfiguráció leegyszerűsítése érdekében egy AK FQDN-címkét biztosít. A következő lépésekkel engedélyezheti a kimenő AK-platform forgalmát:

- Ha Azure Firewall használatával korlátozza a kimenő forgalmat, és egy felhasználó által megadott útvonalat (UDR) hoz létre az összes kimenő forgalom irányításához, akkor győződjön meg arról, hogy megfelelő DNAT-szabályt hoz létre a tűzfalon a bejövő forgalom megfelelő engedélyezéséhez. 

   A Azure Firewall használata UDR megszakítja a bejövő beállítást az aszimmetrikus útválasztás miatt. A probléma akkor fordul elő, ha az AK-alhálózat alapértelmezett útvonala a tűzfal magánhálózati IP-címére mutat, de nyilvános Load balancert használ. Például *terheléselosztó* típusú bejövő vagy Kubernetes szolgáltatás.

   Ebben az esetben a bejövő terheléselosztó forgalma a nyilvános IP-címén keresztül érkezik, a visszatérési útvonal azonban a tűzfal magánhálózati IP-címén halad át. Mivel a tűzfal állapot-nyilvántartó, eldobja a visszaadott csomagot, mert a tűzfal nem ismeri a létesített munkamenetet. Ha szeretné megtudni, hogyan integrálhatja a Azure Firewallt a bemenő vagy a Service Load balancerrel, tekintse meg a [Azure Firewall integrálása az Azure standard Load Balancer](integrate-lb.md)-nal című
- Hozzon létre egy alkalmazás-szabálygyűjtemény-gyűjteményt, és adjon hozzá egy szabályt az *AzureKubernetesService* FQDN címke engedélyezéséhez. A forrás IP-címtartomány a gazdagépek virtuális hálózata, a protokoll pedig HTTPS, a cél pedig AzureKubernetesService.
- A következő kimenő portok/hálózati szabályok szükségesek egy AK-fürthöz:

   - 443-as TCP-port
   - TCP [*IPAddrOfYourAPIServer*]: 443 szükséges, ha van olyan alkalmazás, amelynek meg kell beszélnie az API-kiszolgálóval. Ezt a módosítást a fürt létrehozása után lehet beállítani.
   - A 9000-es TCP-port és a 1194-es UDP-port az alagút elülső Pod-portjához az API-kiszolgáló alagút végével való kommunikációhoz.

      Részletesebben lásd a következő táblázatban található címeket:

   | Cél végpont                                                             | Protokoll | Port    | Használat  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Vagy* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. |
   | **`*:9000`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Vagy* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. |

   - Az 123-es UDP-port a Network Time Protocol (NTP) időszinkronizálásához (Linux-csomópontok).
   - A DNS esetében a 53-es UDP-portra akkor is szükség van, ha közvetlenül az API-kiszolgálóhoz fér hozzá.

   További információ: a [kimenő forgalom szabályozása a fürtcsomópontok számára az Azure Kubernetes szolgáltatásban (ak)](../aks/limit-egress-traffic.md).
- Konfigurálja a AzureMonitor és a Storage szolgáltatás címkéit. Azure Monitor fogadja a log Analytics-adatgyűjtést.

   A munkaterület URL-címét külön is engedélyezheti: `<worksapceguid>.ods.opinsights.azure.com` és `<worksapceguid>.oms.opinsights.azure.com` . Ezt a következő módszerek egyikével kezelheti:

    - HTTPS-hozzáférés engedélyezése a gazdaszámítógép-alhálózatról a és a rendszerre `*. ods.opinsights.azure.com` `*.oms. opinsights.azure.com` . Ezek a helyettesítő karakterek teljes tartománynevei lehetővé teszik a szükséges hozzáférést, de kevésbé korlátozóak.
    - A következő log Analytics-lekérdezéssel sorolja fel a szükséges teljes tartományneveket, majd explicit módon engedélyezze őket a tűzfal alkalmazási szabályaiban:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Következő lépések

- További információ az Azure Kubernetes Service-ről: [Az Azure Kubernetes Service (ak) Kubernetes alapvető fogalmai](../aks/concepts-clusters-workloads.md).
