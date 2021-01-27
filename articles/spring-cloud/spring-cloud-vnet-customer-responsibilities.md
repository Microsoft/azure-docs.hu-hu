---
title: Az Azure Spring Cloud-t futtató ügyfél-felelősség a vnet-ben
description: Ez a cikk az Azure Spring Cloud vnet-ben való futtatásával kapcsolatos felhasználói felelősségeket ismerteti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 63fbac0919e06b29377afacaaa5708d195c6b319
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98887251"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Az Azure Spring Cloud VNET való futtatásának felhasználói feladatai
Ez a dokumentum az Azure Spring Cloud virtuális hálózatban való használatának specifikációit tartalmazza.

Ha az Azure Spring Cloud üzembe helyezése a virtuális hálózaton történik, kimenő függőségei vannak a virtuális hálózaton kívüli szolgáltatásokon. A felügyeleti és működési célokra az Azure Spring Cloudnak bizonyos portokat és teljes tartományneveket (FQDN) kell elérnie. Ezek a végpontok az Azure Spring Cloud felügyeleti síkjával való kommunikációhoz, valamint az alapvető Kubernetes-fürt összetevőinek és biztonsági frissítéseinek letöltéséhez és telepítéséhez szükségesek.

Alapértelmezés szerint az Azure Spring Cloud korlátlan kimenő (kimenő) internet-hozzáféréssel rendelkezik. A hálózati hozzáférés ezen szintje lehetővé teszi a futtatott alkalmazások számára, hogy szükség szerint hozzáférjenek a külső erőforrásokhoz. Ha korlátozni szeretné a kimenő forgalom forgalmát, a karbantartási feladatokhoz korlátozott számú portnak és címnek kell elérhetőnek lennie. A kimenő címek biztonságossá tételének legegyszerűbb megoldása egy olyan tűzfal-eszköz használata, amely a tartománynevek alapján képes szabályozni a kimenő forgalmat. Azure Firewall például a célként megadott teljes tartománynév alapján korlátozhatja a kimenő HTTP-és HTTPS-forgalmat. Az előnyben részesített tűzfal-és biztonsági szabályokat is konfigurálhatja, hogy engedélyezze ezeket a szükséges portokat és címeket.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud-erőforrásokra vonatkozó követelmények 

Az alábbi lista az Azure Spring Cloud Services erőforrás-követelményeit sorolja fel. Általános követelmény, hogy ne módosítsa az Azure Spring Cloud és a mögöttes hálózati erőforrások által létrehozott erőforráscsoportokat.
- Ne módosítsa az Azure Spring Cloud által létrehozott és a tulajdonában lévő erőforráscsoportokat.
  - Alapértelmezés szerint ezek az erőforráscsoportok AP-SVC-rt_ [szolgáltatás-példány-név]_[régió] * és AP_[szolgáltatás-példány-név] _ [régió] *.
- Ne módosítsa az Azure Spring Cloud által használt alhálózatokat.
- Ne hozzon létre egynél több Azure Spring Cloud Service-példányt ugyanabban az alhálózatban.
- Ha tűzfalat használ a forgalom vezérlésére, ne tiltsa le a következő kimenő forgalmat az Azure Spring Cloud Components szolgáltatásban, amely a szolgáltatási példány működését, karbantartását és támogatását *végzi* .

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud Network-követelmények

  | Cél végpont | Port | Használat | Megjegyzés |
  |------|------|------|
  | *: 1194 *vagy* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud: 1194 | UDP: 1194 | A mögöttes Kubernetes-fürt kezelése. | |
  | *: 443 *vagy* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud: 443 | TCP: 443 | Azure Spring Cloud Service-kezelés. | A "requiredTraffics" szolgáltatási példány információi az erőforrás-adattartalomban, a "networkProfile" szakaszban olvashatók. |
  | *: 9000 *vagy* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureCloud: 9000 | TCP: 9000 | A mögöttes Kubernetes-fürt kezelése. |
  | *: 123 *vagy* NTP.Ubuntu.com:123 | UDP: 123 | NTP-idő szinkronizálása Linux-csomópontokon. | |
  | *. azure.io:443 *vagy* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry. | Lecserélhető a *Azure Container Registry* [szolgáltatási végpont engedélyezése a virtuális hálózatban](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). |
  | *. core.windows.net:443 és *. core.windows.net:445 *vagy* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -Storage: 443 és Storage: 445 | TCP: 443, TCP: 445 | Azure File Storage | Lecserélhető az *Azure Storage* [szolgáltatás végpontjának a virtuális hálózatban](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)való engedélyezésével. |
  | *. servicebus.windows.net:443 *vagy* [ServiceTag](https://docs.microsoft.com/azure/virtual-network/service-tags-overview#available-service-tags) -EventHub: 443 | TCP: 443 | Azure Event hub. | Lecserélhető az *Azure Event Hubs* [Service-végpont engedélyezése a virtuális hálózaton](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Azure Spring Cloud FQDN-követelmények/alkalmazás-szabályok

A Azure Firewall teljes tartománynevet (FQDN) tartalmazó **AzureKubernetesService** biztosít a következő konfigurációk egyszerűsítéséhez.

  | Cél teljes tartományneve | Port | Használat |
  |------|------|------|
  | *. azmk8s.io | HTTPS: 443 | A mögöttes Kubernetes-fürt kezelése. |
  | <i>mcr.microsoft.com</i> | HTTPS: 443 | Microsoft Container Registry (MCR). |
  | *. cdn.mscr.io | HTTPS: 443 | A Azure CDN által támogatott MCR-tároló. |
  | *. data.mcr.microsoft.com | HTTPS: 443 | A Azure CDN által támogatott MCR-tároló. |
  | <i>management.azure.com</i> | HTTPS: 443 | A mögöttes Kubernetes-fürt kezelése. |
  | <i>login.microsoftonline.com</i> | HTTPS: 443 | Azure Active Directory hitelesítés. |
  |<i>packages.microsoft.com</i>    | HTTPS: 443 | Microsoft-csomagok tárháza. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS: 443 | A szükséges bináris fájlok, például a kubenet és az Azure CNI telepítéséhez szükséges tárház. |

## <a name="see-also"></a>Lásd még
* [Hozzáférés az alkalmazáshoz egy magánhálózati hálózaton](spring-cloud-access-app-virtual-network.md)
* [Alkalmazások közzététele Application Gateway és Azure Firewall használatával](spring-cloud-expose-apps-gateway-azure-firewall.md) 