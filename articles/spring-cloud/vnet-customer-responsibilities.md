---
title: A virtuális hálózatban Azure Spring Cloud ügyfelek feladatkörei
description: Ez a cikk a virtuális hálózatban Azure Spring Cloud ügyfelek feladatait ismerteti.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java
ms.openlocfilehash: a6b444092ec4e3588564a3f902b49c4ed3dc5fe5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376783"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Az ügyfelek feladatkörei a virtuális Azure Spring Cloud való futtatásért
Ez a dokumentum a virtuális Azure Spring Cloud való használatának specifikációit tartalmazza.

Amikor Azure Spring Cloud a virtuális hálózatban van telepítve, kimenő függőségei vannak a virtuális hálózaton kívüli szolgáltatásoktól. Felügyeleti és működési célból a Azure Spring Cloud portokhoz és teljes tartománynevekhez (FQDN-hez) kell hozzáférnie. Ezek a végpontok szükségesek az Azure Spring Cloud felügyeleti síkjával való kommunikációhoz, valamint a Kubernetes-fürt alapvető összetevőinek és biztonsági frissítésének letöltéséhez és telepítéséhez.

Alapértelmezés szerint a Azure Spring Cloud korlátlan kimenő (kimenő) internet-hozzáféréssel rendelkezik. Ez a hálózati hozzáférési szint lehetővé teszi, hogy a futtatott alkalmazások szükség szerint hozzáférjenek a külső erőforrásokhoz. Ha korlátozni szeretné a bejövő forgalmat, korlátozott számú portnak és címnek kell elérhetőnek lennie a karbantartási feladatokhoz. A kimenő címek biztonságossá tere a legegyszerűbb megoldás, ha olyan tűzfaleszközt használ, amely tartománynevek alapján képes szabályozni a kimenő forgalmat. Azure Firewall például korlátozhatja a kimenő HTTP- és HTTPS-forgalmat a cél teljes tartománya alapján. Az előnyben részesített tűzfal- és biztonsági szabályokat úgy is konfigurálhatja, hogy engedélyezték a szükséges portokat és címeket.

## <a name="azure-spring-cloud-resource-requirements"></a>Azure Spring Cloud erőforrás-követelmények 

Az alábbi lista az erőforrás-szolgáltatásokat Azure Spring Cloud sorolja fel. Általános követelmény, hogy ne módosítsa a hálózati erőforrások és Azure Spring Cloud által létrehozott erőforráscsoportokat.
- Ne módosítsa a felhasználók által létrehozott és birtokolt Azure Spring Cloud.
  - Alapértelmezés szerint ezek az erőforráscsoportok a következő néven vannak elnevezve: ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]* és ap_[SERVICE-INSTANCE-NAME]_[REGION]*.
- Ne módosítsa a felhasználók által használt Azure Spring Cloud.
- Ne hozzon létre egynél több Azure Spring Cloud szolgáltatáspéldányt ugyanabban az alhálózatban.
- Ha tűzfalat használ a  forgalom szabályozására, ne blokkolja a következő, a szolgáltatáspéldányt Azure Spring Cloud, karbantartó és támogató összetevőkre bejövő alábbi forgalmi forgalmat.

## <a name="azure-spring-cloud-network-requirements"></a>Azure Spring Cloud hálózati követelmények

  | Célvégpont | Port | Használat | Megjegyzés |
  |------|------|------|------|
  | *:1194 *vagy* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – AzureCloud:1194 | UDP:1194 | Mögöttes Kubernetes-fürtök kezelése. | |
  | *:443 *Vagy Szolgáltatáscímke* [](../virtual-network/service-tags-overview.md#available-service-tags) – AzureCloud:443 | TCP:443 | Azure Spring Cloud Szolgáltatáskezelés. | A "requiredTraffics" szolgáltatáspéldány adatai az erőforrás hasznos adataiban, a "networkProfile" szakaszban ismertek. |
  | *:9000 *vagy Szolgáltatáscímke* [](../virtual-network/service-tags-overview.md#available-service-tags) – AzureCloud:9000 | TCP:9000 | Mögöttes Kubernetes-fürtök kezelése. |
  | *:123 *Vagy ntp.ubuntu.com:123* | UDP:123 | NTP időszinkronizálás Linux-csomópontokon. | |
  | *.azure.io:443 Or  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – AzureContainerRegistry:443 | TCP:443 | Azure Container Registry. | A virtuális hálózatban  Azure Container Registry [szolgáltatásvégpont engedélyezésével cserélhető le.](../virtual-network/virtual-network-service-endpoints-overview.md) |
  | *.core.windows.net:443 és *.core.windows.net:445 *Or* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – Storage:443 és Storage:445 | TCP:443, TCP:445 | Azure File Storage | Az Azure *Storage* szolgáltatásvégpont engedélyezésével [helyettesíthető a virtuális hálózatban.](../virtual-network/virtual-network-service-endpoints-overview.md) |
  | *.servicebus.windows.net:443 Or  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) – EventHub:443 | TCP:443 | Azure Event Hub. | A szolgáltatásvégpont  engedélyezésével Azure Event Hubs [lecserélhető a virtuális hálózatban.](../virtual-network/virtual-network-service-endpoints-overview.md) |
  

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Azure Spring Cloud FQDN-követelmények/alkalmazásszabályok

Azure Firewall **AzureKubernetesService** FQDN címkét biztosítja a következő konfigurációk egyszerűsítése érdekében:

  | Cél FQDN | Port | Használat |
  |------|------|------|
  | *.azmk8s.io | HTTPS:443 | Mögöttes Kubernetes-fürtök kezelése. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Microsoft Container Registry (MCR). |
  | *.cdn.mscr.io | HTTPS:443 | A tárfiók által Azure CDN. |
  | *.data.mcr.microsoft.com | HTTPS:443 | A tárfiók által Azure CDN. |
  | <i>management.azure.com</i> | HTTPS:443 | Mögöttes Kubernetes-fürtök kezelése. |
  | <i>*login.microsoftonline.com</i> | HTTPS:443 | Azure Active Directory hitelesítést. |
  | <i>*login.microsoft.com</i> | HTTPS:443 | Azure Active Directory hitelesítést. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Microsoft Packages repository. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Az olyan szükséges bináris fájlok telepítéséhez szükséges adattár, mint a kubenet és Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS:80 | Szükséges Microsoft-tanúsítványlánc elérési útjai. |
  | *crl.microsoft.com* | HTTPS:80 | Szükséges Microsoft-tanúsítványlánc elérési útjai. |
  | *crl3.digicert.com* | HTTPS:80 | Harmadik féltől származó SSL-tanúsítványlánc elérési útjai. |
  
## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Azure Spring Cloud nem kötelező FQDN a külső alkalmazásteljesítmény-kezeléshez

Azure Firewall **AzureKubernetesService** FQDN címkét biztosítja a következő konfigurációk egyszerűsítése érdekében:

  | Cél FQDN | Port | Használat                                                          |
  | ---------------- | ---- | ------------------------------------------------------------ |
  | collector*.newrelic.com | TCP:443/80 | Az USA New Relic APM-ügynökök szükséges hálózatait lásd még: APM Agents Networks ( [APM-ügynökök hálózata).](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents) |
  | collector*.eu01.nr-data.net | TCP:443/80 | Az APM-New Relic szükséges hálózata az EU-régióból, lásd még: [APM-ügynökök hálózata.](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents) |

## <a name="see-also"></a>Lásd még
* [Az alkalmazás elérése magánhálózaton](access-app-virtual-network.md)
* [Alkalmazások elérhetővé Application Gateway és Azure Firewall](expose-apps-gateway-azure-firewall.md)
