---
title: A bejövő forgalom korlátozása Azure Kubernetes Service (AKS)
description: Megtudhatja, milyen portokra és címekre van szükség a bejövő forgalom vezérléséhez a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 39c0877b96a3e8c6c716c1ab9ae7ba11575990a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765592"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>A fürtcsomópontok (AKS) Azure Kubernetes Service forgalmának szabályozása

Ez a cikk azokat a szükséges részleteket tartalmazza, amelyek lehetővé teszik a kimenő forgalom biztonságossá Azure Kubernetes Service (AKS) felé. Tartalmazza az alap AKS-telepítés fürtkövetelményét, valamint az opcionális bővítmények és szolgáltatások további követelményeit. [A végén egy példát mutatunk be](#restrict-egress-traffic-using-azure-firewall)arról, hogyan konfigurálhatja ezeket a követelményeket a Azure Firewall. Ezeket az információkat azonban bármely kimenő korlátozási módszerre vagy berendezésre alkalmazhatja.

## <a name="background"></a>Háttér

Az AKS-fürtök egy virtuális hálózaton vannak telepítve. Ez a hálózat kezelhető (az AKS által létrehozva) vagy egyéni (a felhasználó által előre konfigurált) módon. A fürt mindkét esetben  kimenő függőségekkel rendelkezik a virtuális hálózaton kívüli szolgáltatásoktól (a szolgáltatásnak nincsenek bejövő függőségei).

Felügyeleti és működési célból az AKS-fürt csomópontjainak bizonyos portokhoz és teljes tartománynevekhez (FQDN) kell hozzáférniük. Ezek a végpontok szükségesek ahhoz, hogy a csomópontok kommunikáljanak az API-kiszolgálóval, vagy hogy letöltsék és telepítik a Kubernetes-fürt alapvető összetevőit és a csomópontok biztonsági frissítéseit. A fürtnek például alaprendszertároló-rendszerképeket kell lekérte a Microsoft Container Registry (MCR).

Az AKS kimenő függőségei szinte teljesen definiálva vannak FQDN-ekkel, amelyek mögött nincsenek statikus címek. A statikus címek hiánya azt jelenti, hogy a hálózati biztonsági csoportok nem használhatók az AKS-fürtből kimenő forgalom zárolásához. 

Alapértelmezés szerint az AKS-fürtök korlátlan kimenő (kimenő) internet-hozzáféréssel rendelkezik. Ez a hálózati hozzáférési szint lehetővé teszi, hogy a futtatott csomópontok és szolgáltatások szükség szerint hozzáférjenek a külső erőforrásokhoz. Ha korlátozni szeretné a bejövő forgalmat, korlátozott számú portnak és címnek kell elérhetőnek lennie a kifogástalan állapotú fürtkarbantartási feladatok fenntartásához. A kimenő címek biztonságossá tétele a legegyszerűbb megoldás, ha olyan tűzfaleszközt használ, amely tartománynevek alapján képes szabályozni a kimenő forgalmat. Azure Firewall például korlátozhatja a kimenő HTTP- és HTTPS-forgalmat a cél teljes tartománya alapján. Az előnyben részesített tűzfal- és biztonsági szabályokat úgy is konfigurálhatja, hogy engedélyezték a szükséges portokat és címeket.

> [!IMPORTANT]
> Ez a dokumentum csak azt fedi le, hogyan zárolhatja az AKS-alhálózatot elhagyó forgalmat. Az AKS-re alapértelmezés szerint nincsenek bejövő forgalomra vonatkozó követelmények.  A **belső alhálózati forgalom blokkolása** hálózati biztonsági csoportok (NSG-k) és tűzfalak használatával nem támogatott. A fürtön belüli forgalom szabályozásához és blokkolásához használja a [**_Hálózati házirendek használhatja a következőt:_**][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Szükséges kimenő hálózati szabályok és FQDN-ek az AKS-fürtökhöz

Az AKS-fürtökhöz a következő hálózati és FQDN-/alkalmazásszabályok szükségesek. Ezeket akkor használhatja, ha nem az Azure Firewall.

* Az IP-cím függőségei nem HTTP/S forgalomra (TCP- és UDP-forgalomra egyaránt) vannak beszűkve
* FQDN HTTP/HTTPS-végpontok elhelyezhetőek a tűzfaleszközön.
* A helyettesítő karakteres HTTP/HTTPS-végpontok olyan függőségek, amelyek az AKS-fürtön számos minősítő alapján változhatnak.
* Az AKS belépésvezérlővel injektálja az FQDN-t környezeti változóként a Kube-system és a gatekeeper-system környezetben üzemelő összes üzemelő példányba, ami biztosítja, hogy a csomópontok és az API-kiszolgáló közötti összes rendszerkommunikáció az API-kiszolgáló teljes tartománynévjét használja, és ne az API-kiszolgáló IP-címét. 
* Ha olyan alkalmazással vagy megoldással rendelkezik, amely az API-kiszolgálóval szeretne beszélni, hozzá kell adni egy további hálózati szabályt, amely engedélyezi a TCP-kommunikációt az *API-kiszolgáló IP-címének 443-as portjához.* 
* Ritka esetekben, ha karbantartási művelet történik, az API-kiszolgáló IP-címe megváltozhat. Az API-kiszolgáló IP-címét megváltoztatható tervezett karbantartási műveletekről mindig előre tájékoztatunk.


### <a name="azure-global-required-network-rules"></a>Az Azure globálisan szükséges hálózati szabályai

A szükséges hálózati szabályok és IP-címfüggőségek a következőek:

| Célvégpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Vagy* <br/> [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Vagy* <br/> [Regionális CIDR-ek](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Vagy* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | A csomópontok és a vezérlősík közötti bújtatásos biztonságos kommunikációhoz. Ez magánfürtök [esetén nem szükséges](private-clusters.md)|
| **`*:9000`** <br/> *Vagy* <br/> [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Vagy* <br/> [Regionális CIDR-ek](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Vagy* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | A csomópontok és a vezérlősík közötti bújtatásos biztonságos kommunikációhoz. Ez magánfürtök [esetén nem szükséges](private-clusters.md) |
| **`*:123`** vagy **`ntp.ubuntu.com:123`** (ha hálózati Azure Firewall használ)  | UDP      | 123     | A Network Time Protocol (NTP) időszinkronizálásához szükséges Linux-csomópontokon.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Ha egyéni DNS-kiszolgálókat használ, győződjön meg arról, hogy elérhetők a fürtcsomópontok számára. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Kötelező, ha olyan podokat/üzemelő példányokat futtat, amelyek hozzáférnek az API-kiszolgálóhoz, ezek a podok/üzemelő példányok az API IP-címét használják. Ez magánfürtök [esetén nem szükséges](private-clusters.md)  |

### <a name="azure-global-required-fqdn--application-rules"></a>Az Azure globálisan szükséges teljes tartománykészlete/alkalmazásszabálya 

A következő FQDN/ alkalmazásszabályok szükségesek:

| Cél FQDN                 | Port            | Használat      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | A Node < API-> kommunikációhoz szükséges. Cserélje *\<location\>* le a helyére az AKS-fürt üzembe helyezésének régióját. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | A rendszerképek eléréséhez szükséges a Microsoft Container Registry (MCR). Ez a beállításjegyzék belső rendszerképeket/diagramokat (például coreDNS stb.) tartalmaz. Ezek a rendszerképek szükségesek a fürt megfelelő létrehozásához és működéséhez, beleértve a méretezési és frissítési műveleteket is.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Az Azure Content Delivery Network (CDN) által háttérbeli MCR-tároláshoz szükséges. |
| **`management.azure.com`**       | **`HTTPS:443`** | Az Azure API-n keresztüli Kubernetes-műveletekhez szükséges. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | A hitelesítéshez Azure Active Directory szükséges. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Ez a cím a gyorsítótárazott apt-get műveletekhez használt *Microsoft-csomagok adattára.*  Ilyen csomagok például a Moby, a PowerShell és az Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Ez a cím az olyan szükséges bináris fájlok letöltéséhez és telepítéséhez szükséges adattárhoz szükséges, mint a kubenet és Azure CNI. |

### <a name="azure-china-21vianet-required-network-rules"></a>Azure China 21Vianet szükséges hálózati szabályok

A szükséges hálózati szabályok és IP-címfüggőségek a következőek:

| Célvégpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Vagy* <br/> [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Vagy* <br/> [Regionális CIDR-ek](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Vagy* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | A csomópontok és a vezérlősík közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:9000`** <br/> *Vagy* <br/> [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Vagy* <br/> [Regionális CIDR-ek](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Vagy* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | A csomópontok és a vezérlősík közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:22`** <br/> *Vagy* <br/> [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Vagy* <br/> [Regionális CIDR-ek](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Vagy* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | A csomópontok és a vezérlősík közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:123`** vagy **`ntp.ubuntu.com:123`** (ha hálózati Azure Firewall használ)  | UDP      | 123     | A Network Time Protocol (NTP) időszinkronizálásához szükséges Linux-csomópontokon.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Ha egyéni DNS-kiszolgálókat használ, győződjön meg arról, hogy elérhetők a fürtcsomópontok számára. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Az API-kiszolgálóhoz hozzáférő podok/üzemelő példányok futtatása esetén szükséges, hogy ezek a podok/üzemelő példányok az API IP-címét használják.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet FQDN/alkalmazásszabályok

A következő FQDN/ alkalmazásszabályok szükségesek:

| Cél FQDN                               | Port            | Használat      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | A Node < API-> kommunikációhoz szükséges. Cserélje *\<location\>* le a helyére az AKS-fürt üzembe helyezésének régióját. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | A Node < API-> kommunikációhoz szükséges. Cserélje *\<location\>* le a helyére az AKS-fürt üzembe helyezésének régióját. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | A rendszerképek eléréséhez szükséges a Microsoft Container Registry (MCR). Ez a beállításjegyzék belső rendszerképeket/diagramokat (például coreDNS stb.) tartalmaz. Ezek a rendszerképek szükségesek a fürt megfelelő létrehozásához és működéséhez, beleértve a méretezési és frissítési műveleteket is. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Az Azure Content Delivery Network (CDN) által Content Delivery Network MCR-tároláshoz szükséges. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Az Azure API-n keresztüli Kubernetes-műveletekhez szükséges. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | A hitelesítéshez Azure Active Directory szükséges. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Ez a cím a gyorsítótárazott apt-get műveletekhez használt *Microsoft-csomagok adattára.*  Ilyen csomagok például a Moby, a PowerShell és az Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Ez a cím az olyan szükséges bináris fájlok letöltéséhez és telepítéséhez szükséges adattárhoz szükséges, mint a kubenet és Azure CNI. |

### <a name="azure-us-government-required-network-rules"></a>Az Azure US Government megkövetelt hálózati szabályokat

A szükséges hálózati szabályok és IP-címfüggőségek a következőek:

| Célvégpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Vagy* <br/> [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Vagy* <br/> [Regionális CIDR-ek](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Vagy* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | A csomópontok és a vezérlősík közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:9000`** <br/> *Vagy* <br/> [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Vagy* <br/> [Regionális CIDR-ek](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Vagy* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | A csomópontok és a vezérlősík közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:123`** vagy **`ntp.ubuntu.com:123`** (ha hálózati Azure Firewall használ)  | UDP      | 123     | A Network Time Protocol (NTP) időszinkronizálásához szükséges Linux-csomópontokon.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Ha egyéni DNS-kiszolgálókat használ, győződjön meg arról, hogy azok elérhetők a fürtcsomópontok számára. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Az API-kiszolgálóhoz hozzáférő podok/üzemelő példányok futtatása esetén szükséges, hogy ezek a podok/üzemelő példányok az API IP-címét használják.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Az Azure US Government kötelező teljes tartománykészlete/alkalmazásszabálya 

A következő FQDN/alkalmazásszabályok szükségesek:

| Cél FQDN                                        | Port            | Használat      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | A Node < API-> kommunikációhoz szükséges. Cserélje *\<location\>* le a helyére az AKS-fürt üzembe helyezésének régióját.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | A rendszerképek eléréséhez szükséges a Microsoft Container Registry (MCR). Ez a beállításjegyzék belső rendszerképeket/diagramokat (például coreDNS stb.) tartalmaz. Ezek a rendszerképek szükségesek a fürt megfelelő létrehozásához és működéséhez, beleértve a méretezési és frissítési műveleteket is. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Az Azure Content Delivery Network (CDN) által háttérbeli MCR-tároláshoz szükséges. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Az Azure API-n keresztüli Kubernetes-műveletekhez szükséges. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | A hitelesítéshez Azure Active Directory szükséges. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Ez a cím a gyorsítótárazott apt-get műveletekhez használt *Microsoft-csomagok adattára.*  Ilyen csomagok például a Moby, a PowerShell és az Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Ez a cím az olyan szükséges bináris fájlok telepítéséhez szükséges adattárhoz szükséges, mint a kubenet Azure CNI. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>AKS-fürtökhöz ajánlott FQDN/alkalmazásszabályok opcionális használata

Az AKS-fürtökhöz a következő FQDN/ alkalmazásszabályok nem kötelezők, de ajánlottak:

| Cél FQDN                                                               | Port          | Használat      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Ez a cím lehetővé teszi, hogy a Linux-fürtcsomópontok letöltsék a szükséges biztonsági javításokat és frissítéseket. |

Ha úgy dönt, hogy letiltja vagy letiltja ezeket az FQDN-eket, [a](node-image-upgrade.md) csomópontok csak akkor kapják meg az operációsrendszer-frissítéseket, ha csomópont-rendszerkép-frissítést vagy fürtfrissítést [hoz létre.](upgrade-cluster.md)

## <a name="gpu-enabled-aks-clusters"></a>GPU-kompatibilis AKS-fürtök

### <a name="required-fqdn--application-rules"></a>Szükséges FQDN/alkalmazásszabályok

A GPU-t engedélyező AKS-fürtökhöz a következő teljes tartomány-/alkalmazásszabályok szükségesek:

| Cél FQDN                        | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Ez a cím az illesztőprogram megfelelő telepítéséhez és a GPU-alapú csomópontokon való működéshez használatos. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Ez a cím az illesztőprogram megfelelő telepítéséhez és a GPU-alapú csomópontokon való működéshez használatos. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Ez a cím az illesztőprogram megfelelő telepítéséhez és a GPU-alapú csomópontokon való működéshez használatos. |

## <a name="windows-server-based-node-pools"></a>Windows Server-alapú csomópontkészletek 

### <a name="required-fqdn--application-rules"></a>Szükséges FQDN/alkalmazásszabályok

A Windows Server-alapú csomópontkészletek használata esetén a következő teljes tartomány-/alkalmazásszabályok szükségesek:

| Cél FQDN                                                           | Port      | Használat      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Windowshoz kapcsolódó bináris fájlok telepítése |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Windowshoz kapcsolódó bináris fájlok telepítése |

## <a name="aks-addons-and-integrations"></a>AKS-bővítmények és -integrációk

### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

Két lehetőség van arra, hogy hozzáférést biztosítson a Azure Monitor tárolókhoz, engedélyezheti az Azure Monitor [ServiceTaget,](../virtual-network/service-tags-overview.md#available-service-tags)  vagy hozzáférést nyújthat a szükséges teljes tartománynévhez/alkalmazásszabályokhoz.

#### <a name="required-network-rules"></a>Szükséges hálózati szabályok

A következő FQDN/alkalmazásszabályok szükségesek:

| Célvégpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Ezzel a végponttal metrikaadatokat és naplókat küldhet a Azure Monitor Log Analyticsnek. |

#### <a name="required-fqdn--application-rules"></a>Szükséges FQDN/alkalmazásszabályok

A következő FQDN/alkalmazásszabályok szükségesek az olyan AKS-fürtökhöz, amelyeken engedélyezve van Azure Monitor tárolókhoz való Azure Monitor:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Ez a végpont metrikákhoz és telemetria monitorozáshoz használható Azure Monitor. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Ezt a végpontot a Azure Monitor log analytics-adatokhoz használja. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Ezt a végpontot az omsagent használja, amely a Log Analytics szolgáltatás hitelesítésére használatos. |
| *.monitoring.azure.com | **`HTTPS:443`** | Ezzel a végponttal metrikaadatokat küldhet a Azure Monitor. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Frissítse a tűzfalat vagy a biztonsági konfigurációt úgy, hogy engedélyezze a hálózati forgalmat az összes alábbi teljes tartomány és [Azure Dev Spaces infrastruktúra-szolgáltatás között.][dev-spaces-service-tags]

#### <a name="required-network-rules"></a>Szükséges hálózati szabályok

| Célvégpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag (Szolgáltatáscímke)](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Ezzel a végponttal metrikaadatokat és naplókat küldhet a Azure Monitor Log Analyticsbe. |

#### <a name="required-fqdn--application-rules"></a>Szükséges FQDN/alkalmazásszabályok 

Az alábbi FQDN/ alkalmazásszabályok szükségesek az olyan AKS-fürtökhöz, amelyeken engedélyezve van az Azure Dev Spaces:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Ez a cím a linux alpine és más Azure Dev Spaces-rendszerképek lekért címére szolgál |
| `gcr.io` | **`HTTPS:443`** | Ez a cím helm-/tiller-képek lekért címe |
| `storage.googleapis.com` | **`HTTPS:443`** | Ez a cím helm-/tiller-képek lekért címe |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Szükséges FQDN/alkalmazásszabályok 

Az alábbi FQDN/ alkalmazásszabályok szükségesek az olyan AKS-fürtökhöz, amelyeken engedélyezve van a Azure Policy engedélyezése.

| FQDN                                          | Port      | Használat      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Ezzel a címmel lekértük a Kubernetes-szabályzatokat, és jelentést készítünk a fürt megfelelőségi állapotáról a szabályzatszolgáltatásnak. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Ez a cím a beépített szabályzatok Gatekeeper-összetevőinek lekért címére használható. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | A rendszer ezt a címet használja a megfelelő Azure Policy.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Ezzel a címmel lekérhetők a beépített szabályzatok a GitHubról a szabályzatok megfelelő működésének Azure Policy. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy bővítmény, amely telemetriai adatokat küld az Applications Insights-végpontnak. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Azure China 21Vianet FQDN/alkalmazásszabályok 

Az alábbi FQDN-/alkalmazásszabályok szükségesek az olyan AKS-fürtökhöz, amelyeken engedélyezve van Azure Policy tartomány.

| FQDN                                          | Port      | Használat      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Ez a cím a Kubernetes-szabályzatok lekért és a fürt megfelelőségi állapotának a szabályzatszolgáltatásnak való jelentésére használatos. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Ez a cím a beépített szabályzatok Gatekeeper-összetevőinek lekért címére használható. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Az Azure US Government kötelező teljes tartománykészlete/alkalmazásszabálya

Az alábbi FQDN-/alkalmazásszabályok szükségesek az olyan AKS-fürtökhöz, amelyeken engedélyezve van Azure Policy tartomány.

| FQDN                                          | Port      | Használat      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Ez a cím a Kubernetes-szabályzatok lekért és a fürt megfelelőségi állapotának a szabályzatszolgáltatásnak való jelentésére használatos. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Ez a cím a beépített szabályzatok Gatekeeper-összetevőinek lekért címére használható. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>A bejövő forgalom korlátozása az Azure Firewall használatával

Azure Firewall a konfiguráció egyszerűsítése érdekében Azure Kubernetes Service ( `AzureKubernetesService` ) FQDN-címkét. 

> [!NOTE]
> Az FQDN címke tartalmazza a fent felsorolt összes FQDN-t, és automatikusan naprakészen marad.
>
> Az SNAT-portok kimerítési problémáinak elkerülése érdekében javasoljuk, hogy legalább 20 előtere IP-Azure Firewall az éles forgatókönyvekben.

Az alábbiakban az üzemelő példány architektúráját mutatjuk be:

![Zárolt topológia](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* A nyilvános bejövő forgalomnak tűzfalszűrőkön keresztül kell áthaladtatva
  * Az AKS-ügynökcsomópontok egy dedikált alhálózatban vannak elkülönítve.
  * [Azure Firewall](../firewall/overview.md) a rendszer a saját alhálózatán telepíti.
  * A DNAT-szabály lefordítja az FW nyilvános IP-címét az LB előtere IP-címére.
* A kimenő kérések az ügynökcsomópontoktól indulnak Azure Firewall belső [IP-cím](egress-outboundtype.md) felé egy felhasználó által megadott útvonal használatával
  * Az AKS-ügynökcsomópontoktól származó kérések egy UDR-t követnek, amely arra az alhálózatra lett helyezve, amelybe az AKS-fürt üzembe lett helyezve.
  * Azure Firewall a virtuális hálózatból egy nyilvános IP-előtereből kifelé
  * A nyilvános internethez vagy más Azure-szolgáltatásokhoz való hozzáférés a tűzfal előtéri IP-címére és onnan más felé áramlik
  * Ha szükséges, az AKS-vezérlősíkhoz való hozzáférést az [API-kiszolgáló](./api-server-authorized-ip-ranges.md)engedélyezett IP-címtartományai védik, amely tartalmazza a tűzfal nyilvános előtere IP-címét.
* Belső forgalom
  * Ehelyett vagy egy nyilvános [](load-balancer-standard.md) Load Balancer is használhat belső Load Balancer-t a belső forgalomhoz, amelyet a saját alhálózatán is elkülöníthet. [](internal-lb.md)


Az alábbi lépések az Azure Firewall FQDN címkét használják az AKS-fürt kimenő forgalmának korlátozására, és egy példát mutatnak be a nyilvános bejövő forgalom tűzfalon keresztüli `AzureKubernetesService` konfigurálására.


### <a name="set-configuration-via-environment-variables"></a>Konfiguráció beállítása környezeti változókon keresztül

Definiálja az erőforrás-létrehozáshoz használni szükséges környezeti változókat.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Több alhálózattal rendelkező virtuális hálózat létrehozása

Virtuális hálózat kiépítése két külön alhálózattal, egy a fürt számára, egy pedig a tűzfal számára. Ha szükséges, létrehozhat egyet a belső szolgáltatás bejövő forgalomhoz is.

![Üres hálózati topológia](media/limit-egress-traffic/empty-network.png)

Hozzon létre egy erőforráscsoportot az összes erőforráshoz.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Hozzon létre egy virtuális hálózatot két alhálózattal az AKS-fürt és az Azure Firewall. Mindegyiknek saját alhálózata lesz. Kezdjük az AKS-hálózattal.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Hozzon létre és állítson be Azure Firewall UDR-sel

Azure Firewall bejövő és kimenő szabályokat konfigurálni kell. A tűzfal fő célja, hogy lehetővé tegye a szervezetek számára az AKS-fürtbe és az AKS-fürtből ki- és becsatoló részletes bejövő és bejövő forgalomra vonatkozó szabályok konfigurálást.

![Tűzfal és UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Ha a fürt vagy alkalmazás nagy számú kimenő kapcsolatot hoz létre a célhely egy vagy kis részhalmazára irányítva, előfordulhat, hogy több tűzfal előtér-IP-címére van szükség, hogy elkerülje a portok maximális számát az egyes előtér-IP-címeken.
> Több IP-s Azure-tűzfal létrehozásáról itt található [ **további információ**](../firewall/quick-create-multiple-ip-template.md)

Hozzon létre egy standard termékváltozatú nyilvános IP-erőforrást, amely az elő Azure Firewall lesz.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Regisztrálja az előzetes verziójú cli-bővítményt egy új Azure Firewall.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
A korábban létrehozott IP-cím most már hozzárendelhető a tűzfal előterehez.

> [!NOTE]
> A nyilvános IP-cím beállítása a Azure Firewall eltarthat néhány percig.
> Ahhoz, hogy kihasználjuk a hálózati szabályok teljes tartománynevét, engedélyezni kell a DNS-proxyt, ha engedélyezve van, a tűzfal az 53-as porton figyel, és továbbítja a DNS-kéréseket a fent megadott DNS-kiszolgálónak. Ez lehetővé teszi, hogy a tűzfal automatikusan lefordítsa az FQDN-t.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Ha az előző parancs sikeres volt, mentse a tűzfal előtere IP-címét későbbi konfigurálásra.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Ha biztonságos hozzáférést használ az engedélyezett IP-címtartományokkal rendelkező AKS API-kiszolgálóhoz, hozzá kell adni a tűzfal nyilvános IP-címét a hitelesített IP-címtartományhoz. [](./api-server-authorized-ip-ranges.md)

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Hozzon létre egy UDR-t egy ugrással a Azure Firewall

Az Azure automatikusan elirányja a forgalmat az Azure-alhálózatok, virtuális hálózatok és helyszíni hálózatok között. Ha módosítani szeretné az Azure alapértelmezett útválasztását, hozzon létre egy útvonaltáblát.

Hozzon létre egy üres útvonaltáblát, amely egy adott alhálózathoz lesz társítva. Az útvonaltábla a következő ugrást a fent létrehozott Azure Firewall határozza meg. Mindegyik alhálózattal nulla vagy egy útvonaltábla társítható.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Tekintse [meg a virtuális hálózatok útválasztási](../virtual-network/virtual-networks-udr-overview.md#user-defined) táblázatának dokumentációját arról, hogyan bírálhatja felül az Azure alapértelmezett rendszerútvonalait, vagy hogyan adhat hozzá további útvonalakat egy alhálózat útválasztási táblázatához.

### <a name="adding-firewall-rules"></a>Tűzfalszabályok hozzáadása

Az alábbiakban három hálózati szabályt olvashat, amelyek konfigurálhatóak a tűzfalon. Előfordulhat, hogy ezeket a szabályokat az üzemelő példány alapján kell igazítania. Az első szabály engedélyezi a 9000-es port TCP-n keresztüli hozzáférését. A második szabály engedélyezi a hozzáférést az 1194-es és a 123-as porthoz UDP-n keresztül (ha az üzembe helyezést egy Azure China 21Vianet, többre lehet [szükség).](#azure-china-21vianet-required-network-rules) Mindkét szabály csak az általunk használt Azure-régió CIDR-be (ebben az esetben az USA keleti régiójába) tart forgalmat engedélyezi. Végül hozzáadunk egy harmadik hálózati szabályt, amely megnyitja a 123-as portot a teljes tartományn keresztül az UDP-n keresztül (a teljes tartomány tartományának hálózati szabályként való hozzáadása az Azure Firewall egyik specifikus szolgáltatása, és a saját beállításainak használata esetén alkalmazkodnia `ntp.ubuntu.com` kell).

A hálózati szabályok beállítása után egy olyan alkalmazásszabályt is hozzáadunk a használatával, amely lefedi a `AzureKubernetesService` 443-as és a 80-as TCP-porton keresztül elérhető összes szükséges teljes tartományt.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

A [Azure Firewall szolgáltatásról](../firewall/overview.md) további információt az Azure Firewall dokumentáció Azure Firewall jában talál.

### <a name="associate-the-route-table-to-aks"></a>Az útvonaltábla társítása az AKS-hez

Ahhoz, hogy a fürtöt a tűzfalhoz társítsa, a fürt alhálózatának dedikált alhálózatának hivatkozni kell a fent létrehozott útválasztási táblázatra. Társítást úgy lehet tenni, ha parancsot ad a fürtöt és a tűzfalat is tartó virtuális hálózathoz a fürt alhálózatának útválasztási táblázatának frissítéséhez.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>AKS üzembe helyezése kimenő UDR-típussal a meglévő hálózatra

Most már üzembe helyezhető egy AKS-fürt a meglévő virtuális hálózaton. A kimenő típust [ `userDefinedRouting` ](egress-outboundtype.md)is használjuk, ez a szolgáltatás biztosítja, hogy a kimenő forgalom a tűzfalon keresztül legyen kényszerítve, és ne létezik más kimenő útvonal (alapértelmezés szerint az Load Balancer kimenő típus használható).

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Szolgáltatásnév létrehozása a meglévő virtuális hálózaton belüli üzembe építéshez való hozzáféréssel

Az AKS egy fürtidentitást (felügyelt identitást vagy szolgáltatásnévt) használ a fürterőforrások létrehozásához. A létrehozáskor átadott szolgáltatásnév az AKS által használt mögöttes AKS-erőforrások, például az AKS által használt [](use-managed-identity.md) tárolási erőforrások, IP-k és terheléselosztások létrehozására használható (ehelyett felügyelt identitást is használhat). Ha nem rendelkezik az alábbi megfelelő engedélyekkel, nem fogja tudni kiépítni az AKS-fürtöt.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Most cserélje le az és az alábbi helyére az előző parancskimenet által automatikusan létrehozott egyszerű szolgáltatás appid és `APPID` `PASSWORD` szolgáltatásnév jelszavát. A virtuális hálózat erőforrás-azonosítójára hivatkozunk, hogy engedélyeket biztosítsunk a szolgáltatásnévnek, hogy az AKS erőforrásokat helyez üzembe benne.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

A szükséges részletes engedélyeket itt [ellenőrizheti.](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)

> [!NOTE]
> Ha a Kubenet hálózati beépülő modult használja, az AKS-szolgáltatásnévnek vagy a felügyelt identitásnak engedélyeket kell adnia az előre létrehozott útválasztási táblához, mivel a Kubenetnek útválasztási táblázatra van szüksége az új útválasztási szabályok hozzáadásához. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Az AKS üzembe helyezése

Végül az AKS-fürt üzembe helyezhető a fürt számára kijelölt meglévő alhálózaton. A cél alhálózatot, amelybe üzembe helyezni kell, a környezeti változóval van `$SUBNETID` definiálva. Az előző lépésekben nem `$SUBNETID` határoztunk meg változót. Az alhálózat azonosítójának beállítására a következő parancsot használhatja:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Meg kell határoznia a kimenő típust az alhálózaton már létező UDR használatára. Ez a konfiguráció lehetővé teszi, hogy az AKS kihagyja a terheléselosztás beállítását és IP-létesítését.

> [!IMPORTANT]
> Az UDR kimenő típussal kapcsolatos további információkért, beleértve a korlátozásokat, lásd: Kimenő [**forgalom UDR-típusa.**](egress-outboundtype.md#limitations)


> [!TIP]
> A fürttelepítéshez további funkciókat is hozzá lehet adni, például a [**privát fürtöt.**](private-clusters.md) 
>
> Az [**API-kiszolgáló**](api-server-authorized-ip-ranges.md) engedélyezett IP-címtartományok AKS szolgáltatása hozzáadható, hogy az API-kiszolgáló csak a tűzfal nyilvános végpontjára legyen korlátozva. Az engedélyezett IP-címtartományok funkció nem kötelezőként van megjegyzve a diagramon. Amikor engedélyezi az engedélyezett IP-címtartomány funkciót az API-kiszolgálók hozzáférésének korlátozására, a fejlesztői eszközöknek jumpboxot kell használniuk a tűzfal virtuális hálózatán, vagy az összes fejlesztői végpontot hozzá kell adni a hitelesített IP-címtartományhoz.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Fejlesztői hozzáférés engedélyezése az API-kiszolgálóhoz

Ha az előző lépésben hitelesített IP-címtartományokat használt a fürthöz, akkor hozzá kell adni a fejlesztői eszköz IP-címeket a jóváhagyott IP-tartományok AKS-fürtlistához, hogy onnan el tudja férni az API-kiszolgálót. Egy másik lehetőség egy jumpbox konfigurálása a szükséges eszközkészlettel a tűzfal virtuális hálózatának egy külön alhálózatán.

Adjon hozzá egy másik IP-címet a jóváhagyott tartományokhoz az alábbi paranccsal

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálja a parancsot az újonnan létrehozott `kubectl` Kubernetes-fürthöz való csatlakozáshoz. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Nyilvános szolgáltatás üzembe helyezése
Most már elkezdheti a szolgáltatások felfedése és alkalmazások üzembe helyezése a fürtön. Ebben a példában egy nyilvános szolgáltatást fogunk elérhetővé tenni, de belső szolgáltatást is elérhetővé tehet a belső [terheléselosztáson keresztül.](internal-lb.md)

![Nyilvános szolgáltatás DNAT-ja](media/limit-egress-traffic/aks-create-svc.png)

Az Azure-szavazóalkalmazás üzembe helyezéséhez másolja az alábbi yaml-fájlt egy nevű `example.yaml` fájlba.

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

A szolgáltatás üzembe helyezése a következő futtatásával:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>DNAT-szabály hozzáadása a Azure Firewall

> [!IMPORTANT]
> Ha a Azure Firewall használatával korlátozza a bejövő forgalmat, és létrehoz egy felhasználó által megadott útvonalat (UDR), amely az összes bejövő forgalmat kényszeríti, mindenképpen hozzon létre egy megfelelő DNAT-szabályt a tűzfalon a bejövő forgalom megfelelő engedélyezése érdekében. A Azure Firewall UDR-sel való használata megszakítja a bejövő forgalom beállítását az aszimmetrikus útválasztás miatt. (A probléma akkor merül fel, ha az AKS-alhálózat alapértelmezett útvonala a tűzfal magánhálózati IP-címére irányít, de Ön egy loadBalancer típusú nyilvános terheléselelítőt használ – bejövő vagy Kubernetes-szolgáltatást. Ebben az esetben a bejövő terheléselelosztási forgalom a nyilvános IP-címen keresztül érkezik, de a visszatérési útvonal a tűzfal magánhálózati IP-címére halad át. Mivel a tűzfal állapot-állapot szerinti, eldobja a visszatérő csomagot, mert a tűzfal nem tud egy már létrehozott munkamenetről. Ha meg szeretne ismerkedni a bejövő Azure Firewall vagy a szolgáltatás terheléselosztásával, olvassa el a Következőt: Integráció Azure Firewall [Azure standard Load Balancer.](../firewall/integrate-lb.md)


A bejövő kapcsolat konfiguráláshoz egy DNAT-szabályt kell írni a Azure Firewall. A fürthöz való csatlakozás teszteléséhez meg kell határozni egy szabályt a tűzfal előtere nyilvános IP-címéhez, amely a belső szolgáltatás által elérhetővé teszi a belső IP-címre való útválasztást.

A célcím testreszabható, mivel ez az a port a tűzfalon, amelyhez hozzá kell férni. A lefordított címnek a belső terheléselosztás IP-címének kell lennie. A lefordított portnak a Kubernetes-szolgáltatás elérhetővéált portjának kell lennie.

Meg kell adnia a Kubernetes-szolgáltatás által létrehozott terheléselosztáshoz rendelt belső IP-címet. A cím lekérése a következő futtatásával:

```bash
kubectl get services
```

A szükséges IP-cím az EXTERNAL-IP oszlopban lesz felsorolva, az alábbihoz hasonlóan.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Szerezze be a szolgáltatás IP-címét a következő futtatásával:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Adja hozzá a NAT-szabályt a következő futtatásával:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A kapcsolat ellenőrzéséhez lépjen Azure Firewall előtere IP-címére egy böngészőben.

Meg kell lennie az AKS szavazóalkalmazásnak. Ebben a példában a tűzfal nyilvános IP-címe `52.253.228.132` volt.


![Képernyőkép az A K S szavazóalkalmazásról, a Macskák, a Kutyák és a Visszaállítás gombokkal és az összegekkel.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure-erőforrások törléséhez törölje az AKS-erőforráscsoportot.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogy milyen portokat és címeket engedélyez, ha korlátozni szeretné a fürtre vonatkozó bejövő forgalmat. Azt is láthatta, hogyan biztosíthatja a kimenő forgalom biztonságossá Azure Firewall. 

Szükség esetén általánosíthatja a fenti lépéseket, hogy a forgalmat az előnyben részesített kimenő forgalmi megoldásnak továbbkérődjön a [kimenő típus `userDefinedRoute` dokumentációja alapján.](egress-outboundtype.md)

Ha korlátozni szeretné a podok egymás közötti kommunikációját és a fürtön belüli forgalomkorlátozásokatEast-West tekintse meg a Secure traffic between pods using network policies in AKS (A podok közötti forgalom biztonságossá használata hálózati szabályzatokkal az [AKS-ban) (][network-policy])

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
