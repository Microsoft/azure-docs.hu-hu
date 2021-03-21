---
title: Az Azure Spring Cloud-t futtató ügyfél-felelősség a vnet-ben
description: Ez a cikk az Azure Spring Cloud vnet-ben való futtatásával kapcsolatos felhasználói felelősségeket ismerteti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0c73d0394486472c2c3c92450aab6a1a0d329cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698212"
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
  | *: 1194 *vagy* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 1194 | UDP: 1194 | A mögöttes Kubernetes-fürt kezelése. | |
  | *: 443 *vagy* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 443 | TCP: 443 | Azure Spring Cloud Service-kezelés. | A "requiredTraffics" szolgáltatási példány információi az erőforrás-adattartalomban, a "networkProfile" szakaszban olvashatók. |
  | *: 9000 *vagy* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureCloud: 9000 | TCP: 9000 | A mögöttes Kubernetes-fürt kezelése. |
  | *: 123 *vagy* NTP.Ubuntu.com:123 | UDP: 123 | NTP-idő szinkronizálása Linux-csomópontokon. | |
  | *. azure.io:443 *vagy* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -AzureContainerRegistry: 443 | TCP: 443 | Azure Container Registry. | Lecserélhető a *Azure Container Registry* [szolgáltatási végpont engedélyezése a virtuális hálózatban](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *. core.windows.net:443 és *. core.windows.net:445 *vagy* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -Storage: 443 és Storage: 445 | TCP: 443, TCP: 445 | Azure File Storage | Lecserélhető az *Azure Storage* [szolgáltatás végpontjának a virtuális hálózatban](../virtual-network/virtual-network-service-endpoints-overview.md)való engedélyezésével. |
  | *. servicebus.windows.net:443 *vagy* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -EventHub: 443 | TCP: 443 | Azure Event hub. | Lecserélhető az *Azure Event Hubs* [Service-végpont engedélyezése a virtuális hálózaton](../virtual-network/virtual-network-service-endpoints-overview.md). |
  

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
  | *mscrl.microsoft.com* | HTTPS: 80 | A Microsoft tanúsítványlánc szükséges elérési útjai. |
  | *crl.microsoft.com* | HTTPS: 80 | A Microsoft tanúsítványlánc szükséges elérési útjai. |
  | *crl3.digicert.com* | HTTPS: 80 | harmadik féltől származó SSL-tanúsítványlánc elérési útjai. |

## <a name="see-also"></a>Lásd még
* [Hozzáférés az alkalmazáshoz egy magánhálózati hálózaton](spring-cloud-access-app-virtual-network.md)
* [Alkalmazások közzététele Application Gateway és Azure Firewall használatával](spring-cloud-expose-apps-gateway-azure-firewall.md)