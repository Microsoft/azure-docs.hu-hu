---
title: Kimenő forgalom korlátozása az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogy mely portokra és címekre van szükség a kimenő forgalom vezérléséhez az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 01/12/2021
author: palma21
ms.openlocfilehash: 9e65e2736578ce04dfa79d5a7827e190d47fb312
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573829"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>A fürtcsomópontok kimenő forgalmának szabályozása az Azure Kubernetes szolgáltatásban (ak)

Ez a cikk azokat a szükséges adatokat tartalmazza, amelyekkel biztonságossá teheti a kimenő forgalmat az Azure Kubernetes szolgáltatásból (ak). Tartalmazza az alapszintű AK központi telepítésének a fürtre vonatkozó követelményeit, valamint a választható bővítmények és funkciók további követelményeit. [A követelmények Azure Firewall használatával történő konfigurálásának végén egy példa jelenik meg](#restrict-egress-traffic-using-azure-firewall). Ezt az információt azonban bármely kimenő korlátozási módszerre vagy berendezésre alkalmazhatja.

## <a name="background"></a>Háttér

Az AK-fürtök üzembe helyezése egy virtuális hálózaton történik. Ezt a hálózatot felügyelheti (ak által létrehozott) vagy egyéni (a felhasználó előre konfigurálta). Mindkét esetben a fürt **kimenő** függőségekkel rendelkezik az adott virtuális hálózaton kívüli szolgáltatásokon (a szolgáltatás nem rendelkezik bejövő függőségekkel).

A felügyeleti és működési célokra az AK-fürtök csomópontjainak bizonyos portokhoz és teljes tartománynevek (FQDN) eléréséhez kell rendelkezniük. Ezek a végpontok szükségesek ahhoz, hogy a csomópontok kommunikáljanak az API-kiszolgálóval, vagy le kell tölteniük és telepíteniük kell az alapvető Kubernetes-fürt összetevőit és a csomópontok biztonsági frissítéseit A fürtnek például a Microsoft Container Registry (MCR) alapszintű rendszertároló lemezképeit kell lekérnie.

Az AK kimenő függőségei szinte teljesen meg vannak határozva teljes TARTOMÁNYNEVEk használatával, amelyek nem rendelkeznek statikus címmel. A statikus címek hiánya azt jelenti, hogy a hálózati biztonsági csoportok nem használhatók egy AK-fürtről érkező kimenő forgalom zárolására. 

Alapértelmezés szerint az AK-fürtök korlátlan kimenő (kimenő) internet-hozzáféréssel rendelkeznek. A hálózati hozzáférés ezen szintje lehetővé teszi, hogy a futtatott csomópontok és szolgáltatások igény szerint hozzáférhessenek a külső erőforrásokhoz. Ha korlátozni szeretné a kimenő forgalom forgalmát, a fürt kifogástalan karbantartási feladatainak megtartása érdekében korlátozott számú portot és címet kell elérhetővé tenni. A kimenő címek biztonságossá tételének legegyszerűbb megoldása egy olyan tűzfal-eszköz használata, amely a tartománynevek alapján képes a kimenő forgalom vezérlésére. Azure Firewall például a célként megadott teljes tartománynév alapján korlátozhatja a kimenő HTTP-és HTTPS-forgalmat. Az előnyben részesített tűzfal-és biztonsági szabályokat is konfigurálhatja, hogy engedélyezze ezeket a szükséges portokat és címeket.

> [!IMPORTANT]
> Ez a dokumentum csak azt ismerteti, hogyan lehet zárolni az AK-alhálózatot elhagyó forgalmat. Az AK-ban alapértelmezés szerint nincsenek beáramlási követelmények.  A **belső alhálózati forgalom** blokkolása hálózati biztonsági csoportokkal (NSG) és tűzfalakkal nem támogatott. A fürtön belüli forgalom szabályozásához és letiltásához használja a [**_hálózati házirendeket_**][network-policy].

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Szükséges kimenő hálózati szabályok és teljes tartománynevek az AK-fürtökhöz

A következő hálózati és FQDN/alkalmazási szabályok szükségesek egy AK-fürthöz, akkor használhatja őket, ha a Azure Firewallon kívüli megoldást szeretne konfigurálni.

* Az IP-címek függőségei nem HTTP/S forgalomra vonatkoznak (TCP-és UDP-forgalom)
* Az FQDN HTTP-/HTTPS-végpontok a tűzfal eszközén helyezhetők el.
* A helyettesítő HTTP/HTTPS-végpontok olyan függőségek, amelyek számos minősítőtől függően eltérőek lehetnek az AK-fürttől.
* Az AK egy belépésvezérlés használatával adja hozzá a teljes tartománynevet környezeti változóként a Kube-rendszer és a forgalomirányító-rendszer területen lévő összes központi telepítéshez, amely biztosítja, hogy a csomópontok és az API-kiszolgáló közötti összes rendszerkommunikáció az API-kiszolgáló teljes tartománynevét használja, nem az API-kiszolgáló IP-címét. 
* Ha van olyan alkalmazás vagy megoldás, amelynek az API-kiszolgálóval kell kommunikálnia, **további** hálózati szabályt kell hozzáadnia, hogy engedélyezze a *TCP-kommunikációt az API-kiszolgáló IP-címének 443-es portján*.
* Ritka esetekben, ha van karbantartási művelet, az API-kiszolgáló IP-címe változhat. Az API-kiszolgáló IP-címét megváltoztató tervezett karbantartási műveletek mindig előre lesznek továbbítva.


### <a name="azure-global-required-network-rules"></a>Az Azure globálisan szükséges hálózati szabályai

A szükséges hálózati szabályok és IP-címek függőségei a következők:

| Cél végpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Vagy* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. Ez a [magánhálózati fürtök](private-clusters.md) esetében nem szükséges|
| **`*:9000`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Vagy* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. Ez a [magánhálózati fürtök](private-clusters.md) esetében nem szükséges |
| **`*:123`** vagy **`ntp.ubuntu.com:123`** (Azure Firewall hálózati szabályok használata esetén)  | UDP      | 123     | A Linux-csomópontokon a Network Time Protocol (NTP) idejének szinkronizálásához szükséges.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Ha egyéni DNS-kiszolgálókat használ, gondoskodnia kell arról, hogy a fürtcsomópontok elérhetők legyenek. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Az API-kiszolgálót elérő hüvelyek/központi telepítések futtatása kötelező, ezek a hüvelyek/központi telepítések az API-t használják. Ez a [magánhálózati fürtök](private-clusters.md) esetében nem szükséges  |

### <a name="azure-global-required-fqdn--application-rules"></a>Azure globálisan szükséges teljes tartományneve/alkalmazási szabályok 

A következő teljes tartománynév/alkalmazás szabályok szükségesek:

| Cél teljes tartományneve                 | Port            | Használat      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | A Node <-> API-kiszolgáló kommunikációja szükséges. Cserélje le *\<location\>* a helyére azt a régiót, ahol az AK-fürtöt üzembe helyezi. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Lemezképek eléréséhez szükséges a Microsoft Container Registryban (MCR). Ez a beállításjegyzék tartalmazza az első féltől származó lemezképeket és diagramokat (például coreDNS stb.). Ezek a lemezképek a fürt megfelelő létrehozásához és működéséhez szükségesek, beleértve a méretezési és frissítési műveleteket is.  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Az Azure Content Delivery Network (CDN) által támogatott MCR tároláshoz szükséges. |
| **`management.azure.com`**       | **`HTTPS:443`** | Az Azure API-ra vonatkozó Kubernetes-műveletekhez szükséges. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Azure Active Directory hitelesítéshez szükséges. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Ez a címe a Microsoft Packages adattárat használja a gyorsítótárazott *apt-get* műveletekhez.  A csomagok közé tartoznak például a Moby, a PowerShell és az Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Ez a címe a szükséges bináris fájlok (például a kubenet és az Azure CNI) letöltéséhez és telepítéséhez szükséges adattár. |

### <a name="azure-china-21vianet-required-network-rules"></a>Az Azure China 21Vianet szükséges hálózati szabályok

A szükséges hálózati szabályok és IP-címek függőségei a következők:

| Cél végpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.Region:1194`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Vagy* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:9000`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Vagy* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:22`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:22`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:22`** <br/> *Vagy* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:123`** vagy **`ntp.ubuntu.com:123`** (Azure Firewall hálózati szabályok használata esetén)  | UDP      | 123     | A Linux-csomópontokon a Network Time Protocol (NTP) idejének szinkronizálásához szükséges.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Ha egyéni DNS-kiszolgálókat használ, gondoskodnia kell arról, hogy a fürtcsomópontok elérhetők legyenek. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Az API-kiszolgálót elérő hüvelyek/központi telepítések futtatása kötelező, ezek a pod/üzemelő példányok az API-t használják.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Az Azure China 21Vianet szükséges teljes tartományneve/alkalmazási szabályok

A következő teljes tartománynév/alkalmazás szabályok szükségesek:

| Cél teljes tartományneve                               | Port            | Használat      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | A Node <-> API-kiszolgáló kommunikációja szükséges. Cserélje le *\<location\>* a helyére azt a régiót, ahol az AK-fürtöt üzembe helyezi. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | A Node <-> API-kiszolgáló kommunikációja szükséges. Cserélje le *\<location\>* a helyére azt a régiót, ahol az AK-fürtöt üzembe helyezi. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Lemezképek eléréséhez szükséges a Microsoft Container Registryban (MCR). Ez a beállításjegyzék tartalmazza az első féltől származó lemezképeket és diagramokat (például coreDNS stb.). Ezek a lemezképek a fürt megfelelő létrehozásához és működéséhez szükségesek, beleértve a méretezési és frissítési műveleteket is. |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Az Azure Content Delivery Network (CDN) által támogatott MCR tároláshoz szükséges. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Az Azure API-ra vonatkozó Kubernetes-műveletekhez szükséges. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Azure Active Directory hitelesítéshez szükséges. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Ez a címe a Microsoft Packages adattárat használja a gyorsítótárazott *apt-get* műveletekhez.  A csomagok közé tartoznak például a Moby, a PowerShell és az Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Ez a címe a szükséges bináris fájlok (például a kubenet és az Azure CNI) letöltéséhez és telepítéséhez szükséges adattár. |

### <a name="azure-us-government-required-network-rules"></a>Az USA kormányzati szerveinek szükséges hálózati szabályai

A szükséges hálózati szabályok és IP-címek függőségei a következők:

| Cél végpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Vagy* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:9000`** <br/> *Vagy* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Vagy* <br/> [Regionális CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Vagy* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | A csomópontok és a vezérlő síkja közötti bújtatásos biztonságos kommunikációhoz. |
| **`*:123`** vagy **`ntp.ubuntu.com:123`** (Azure Firewall hálózati szabályok használata esetén)  | UDP      | 123     | A Linux-csomópontokon a Network Time Protocol (NTP) idejének szinkronizálásához szükséges.                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Ha egyéni DNS-kiszolgálókat használ, gondoskodnia kell arról, hogy a fürtcsomópontok elérhetők legyenek. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Az API-kiszolgálót elérő hüvelyek/központi telepítések futtatása kötelező, ezek a hüvelyek/központi telepítések az API-t használják.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure USA kormányának kötelező teljes tartományneve/alkalmazási szabályok 

A következő teljes tartománynév/alkalmazás szabályok szükségesek:

| Cél teljes tartományneve                                        | Port            | Használat      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | A Node <-> API-kiszolgáló kommunikációja szükséges. Cserélje le *\<location\>* a helyére azt a régiót, ahol az AK-fürtöt üzembe helyezi.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Lemezképek eléréséhez szükséges a Microsoft Container Registryban (MCR). Ez a beállításjegyzék tartalmazza az első féltől származó lemezképeket és diagramokat (például coreDNS stb.). Ezek a lemezképek a fürt megfelelő létrehozásához és működéséhez szükségesek, beleértve a méretezési és frissítési műveleteket is. |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Az Azure Content Delivery Network (CDN) által támogatott MCR tároláshoz szükséges. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Az Azure API-ra vonatkozó Kubernetes-műveletekhez szükséges. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Azure Active Directory hitelesítéshez szükséges. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Ez a címe a Microsoft Packages adattárat használja a gyorsítótárazott *apt-get* műveletekhez.  A csomagok közé tartoznak például a Moby, a PowerShell és az Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Ez a címe a szükséges bináris fájlok, például a kubenet és az Azure CNI telepítéséhez szükséges tárház. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Opcionálisan javasolt FQDN/alkalmazási szabályok az AK-fürtökhöz

A következő teljes tartománynév/alkalmazás-szabályok nem kötelezőek, de az AK-fürtök esetében ajánlott:

| Cél teljes tartományneve                                                               | Port          | Használat      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Ez a címe lehetővé teszi, hogy a Linux-fürtcsomópontok letöltsék a szükséges biztonsági javításokat és frissítéseket. |

Ha úgy dönt, hogy letiltja/nem engedélyezi ezeket a teljes tartományneveket, a csomópontok csak akkor kapják meg az operációs rendszer frissítését, amikor a [csomópont-rendszerkép frissítése](node-image-upgrade.md) vagy a [fürt frissítése](upgrade-cluster.md)történik.

## <a name="gpu-enabled-aks-clusters"></a>GPU-t támogató AK-fürtök

### <a name="required-fqdn--application-rules"></a>Szükséges teljes tartománynév/alkalmazási szabályok

A GPU-t használó AK-fürtök esetében a következő teljes tartománynév/alkalmazás szabályok szükségesek:

| Cél teljes tartományneve                        | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Ez a címe a megfelelő illesztőprogram-telepítéshez és-művelethez használható a GPU-alapú csomópontokon. |

## <a name="windows-server-based-node-pools"></a>Windows Server-alapú csomópont-készletek 

### <a name="required-fqdn--application-rules"></a>Szükséges teljes tartománynév/alkalmazási szabályok

A Windows Server-alapú csomópont-készletek használatához a következő FQDN-/alkalmazás-szabályok szükségesek:

| Cél teljes tartományneve                                                           | Port      | Használat      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | A Windows rendszerhez kapcsolódó bináris fájlok telepítése |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | A Windows rendszerhez kapcsolódó bináris fájlok telepítése |

## <a name="aks-addons-and-integrations"></a>AK-bővítmények és-integrációk

### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

Két lehetőség áll rendelkezésre a tárolók Azure Monitorhoz való hozzáférés biztosításához, engedélyezheti a Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) , **vagy** hozzáférést biztosíthat a szükséges teljes tartománynév/alkalmazás-szabályokhoz.

#### <a name="required-network-rules"></a>Szükséges hálózati szabályok

A következő teljes tartománynév/alkalmazás szabályok szükségesek:

| Cél végpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Ez a végpont a metrikai adatok és naplók küldésére szolgál Azure Monitor és Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Szükséges teljes tartománynév/alkalmazási szabályok

A következő teljes tartománynevek és alkalmazási szabályok szükségesek azon AK-fürtök esetében, amelyeken engedélyezve van az Azure Monitor a tárolók számára:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Ez a végpont a metrikák és a figyelési telemetria használatos a Azure Monitor használatával. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Ezt a végpontot a Azure Monitor használja a log Analytics-adatgyűjtéshez. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Ezt a végpontot a omsagent használja, amely a log Analytics szolgáltatás hitelesítésére szolgál. |
| *. monitoring.azure.com | **`HTTPS:443`** | Ez a végpont a metrikai adatok Azure Monitor történő küldésére szolgál. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

A tűzfal vagy a biztonsági konfiguráció frissítésével engedélyezheti a hálózati forgalmat az alábbi teljes TARTOMÁNYNEVEk és az [Azure dev Spaces infrastruktúra-szolgáltatások][dev-spaces-service-tags]között.

#### <a name="required-network-rules"></a>Szükséges hálózati szabályok

| Cél végpont                                                             | Protokoll | Port    | Használat  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureDevSpaces`**  | TCP           | 443      | Ez a végpont a metrikai adatok és naplók küldésére szolgál Azure Monitor és Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Szükséges teljes tartománynév/alkalmazási szabályok 

A következő teljes tartománynév/alkalmazás szabályok szükségesek azon AK-fürtökhöz, amelyeken engedélyezve van az Azure dev Spaces:

| FQDN                                    | Port      | Használat      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Ez a címe a linuxos alpesi és egyéb Azure dev Spaces-rendszerképek lekérésére szolgál |
| `gcr.io` | **`HTTPS:443`** | Ez a címe a Helm/Tiller-képek lekérésére szolgál |
| `storage.googleapis.com` | **`HTTPS:443`** | Ez a címe a Helm/Tiller-képek lekérésére szolgál |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Szükséges teljes tartománynév/alkalmazási szabályok 

A következő teljes tartománynevek/alkalmazási szabályok szükségesek azon AK-fürtök esetében, amelyeken engedélyezve van a Azure Policy.

| FQDN                                          | Port      | Használat      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Ez a címe a Kubernetes házirendek lekérésére és a fürt megfelelőségi állapotának a házirend-szolgáltatásba való bejelentésére szolgál. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Ez a címe a beépített szabályzatok forgalomirányító összetevőinek lekérésére szolgál. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Ez a címe Azure Policy helyes működéséhez használatos.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Ez a címe a beépített szabályzatok GitHubról történő lekérésére szolgál a Azure Policy megfelelő működésének biztosítása érdekében. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Azure Policy bővítmény, amely telemetria adatokat küld az Applications-elemzések végpontjának. |

#### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Az Azure China 21Vianet szükséges teljes tartományneve/alkalmazási szabályok 

A következő teljes tartománynevek/alkalmazási szabályok szükségesek azon AK-fürtök esetében, amelyeken engedélyezve van a Azure Policy.

| FQDN                                          | Port      | Használat      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.cn`** | **`HTTPS:443`** | Ez a címe a Kubernetes házirendek lekérésére és a fürt megfelelőségi állapotának a házirend-szolgáltatásba való bejelentésére szolgál. |
| **`store.policy.azure.cn`** | **`HTTPS:443`** | Ez a címe a beépített szabályzatok forgalomirányító összetevőinek lekérésére szolgál. |

#### <a name="azure-us-government-required-fqdn--application-rules"></a>Azure USA kormányának kötelező teljes tartományneve/alkalmazási szabályok

A következő teljes tartománynevek/alkalmazási szabályok szükségesek azon AK-fürtök esetében, amelyeken engedélyezve van a Azure Policy.

| FQDN                                          | Port      | Használat      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.azure.us`** | **`HTTPS:443`** | Ez a címe a Kubernetes házirendek lekérésére és a fürt megfelelőségi állapotának a házirend-szolgáltatásba való bejelentésére szolgál. |
| **`store.policy.azure.us`** | **`HTTPS:443`** | Ez a címe a beépített szabályzatok forgalomirányító összetevőinek lekérésére szolgál. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Kimenő forgalom korlátozása az Azure Firewall használatával

A Azure Firewall egy Azure Kubernetes szolgáltatás ( `AzureKubernetesService` ) FQDN címkét biztosít a konfiguráció egyszerűsítéséhez. 

> [!NOTE]
> A teljes tartománynév címke tartalmazza az összes fent felsorolt teljes tartománynevet, és a rendszer automatikusan naprakészen tartja.
>
> Azt javasoljuk, hogy üzemi helyzetekben legalább 20 előtérbeli IP-címet használjon a Azure Firewall, hogy elkerülje az SNAT-portok kimerülésével kapcsolatos problémákat.

Az alábbi példa az üzemelő példány architektúráját mutatja be:

![Zárolt topológia](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* A nyilvános bejövő forgalom a tűzfalon keresztüli szűrésre kényszerül
  * Az AK-ügynökök csomópontjai elkülönített alhálózatokban vannak elkülönítve.
  * [Azure Firewall](../firewall/overview.md) a saját alhálózatán van üzembe helyezve.
  * A DNAT szabály az FW nyilvános IP-címet az LB frontend IP-címére fordítja le.
* A kimenő kérelmek ügynök-csomópontokból a Azure Firewall belső IP-címekre indulnak egy [felhasználó által megadott útvonal](egress-outboundtype.md) használatával
  * Az AK-ügynök csomópontjaitól érkező kérések követnek egy olyan UDR, amely az AK-fürt üzembe helyezésére szolgáló alhálózaton van elhelyezve.
  * Azure Firewall egresses a virtuális hálózatról a nyilvános IP-címről
  * A nyilvános internethez vagy más Azure-szolgáltatásokhoz való hozzáférés a tűzfal előtér-IP-címére irányuló és onnan áramlik.
  * Ha szükséges, a hozzáférés az AK-vezérlési síkon az [API-kiszolgáló által jogosult IP-címtartományok](./api-server-authorized-ip-ranges.md)által védett, amely magában foglalja a tűzfal nyilvános ELŐTÉRBELI IP-címét.
* Belső forgalom
  * Ehelyett vagy egy [nyilvános Load Balanceron](load-balancer-standard.md) kívül a belső forgalomhoz [belső Load Balancer](internal-lb.md) is használhat, amelyet a saját alhálózatán is elkülönítheti.


Az alábbi lépések a Azure Firewall `AzureKubernetesService` FQDN-címkét használják az AK-fürt kimenő forgalmának korlátozására, és példát mutatnak arra, hogyan konfigurálható a nyilvános bejövő forgalom a tűzfalon keresztül.


### <a name="set-configuration-via-environment-variables"></a>Konfiguráció beállítása környezeti változók használatával

Adja meg az erőforrás-létrehozásokban használandó környezeti változók készletét.

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

Hozzon létre egy virtuális hálózatot két különálló alhálózattal, egyet a fürthöz, egyet a tűzfalhoz. Opcionálisan létrehozhat egyet a belső szolgáltatás bejövő szolgáltatásaihoz is.

![Üres hálózati topológia](media/limit-egress-traffic/empty-network.png)

Hozzon létre egy erőforráscsoportot az összes erőforrás tárolásához.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Hozzon létre egy virtuális hálózatot két alhálózattal az AK-fürt és a Azure Firewall üzemeltetéséhez. Mindegyiknek saját alhálózata lesz. Kezdjük az AK-hálózattal.

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

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Azure Firewall létrehozása és beállítása UDR

Azure Firewall be kell állítani a bejövő és a kimenő szabályokat. A tűzfal fő célja, hogy lehetővé tegye a szervezetek számára a részletes bejövő és kimenő forgalmi szabályok konfigurálását az AK-fürtbe.

![Tűzfal-és UDR](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Ha a fürt vagy alkalmazás nagy számú kimenő kapcsolatot hoz létre a célhelyek ugyanazon vagy kis részhalmazára irányítva, előfordulhat, hogy további tűzfal-előtérbeli IP-címekre van szüksége, hogy elkerülje a maxing a portok számára.
> További információ az Azure Firewall több IP-címmel való létrehozásáról [  :](../firewall/quick-create-multiple-ip-template.md)

Hozzon létre egy szabványos SKU nyilvános IP-erőforrást, amelyet Azure Firewall előtér-címként kíván használni.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Regisztrálja az előnézeti CLI-bővítményt Azure Firewall létrehozásához.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
A korábban létrehozott IP-cím most már hozzá lehet rendelni a tűzfal előtérbeli felületéhez.

> [!NOTE]
> A nyilvános IP-cím a Azure Firewall való beállítása néhány percet igénybe vehet.
> A hálózati szabályok teljes tartománynevének kihasználásához engedélyezve kell lennie a DNS-proxynak, ha engedélyezve van a tűzfal a 53-es portot fogja figyelni, és továbbítja a DNS-kéréseket a fent megadott DNS-kiszolgálónak. Ez lehetővé teszi, hogy a tűzfal automatikusan lefordítsa a teljes tartománynevet.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Az előző parancs sikeres végrehajtása után mentse a tűzfal előtér-IP-címét később a konfigurációhoz.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Ha a [jogosult IP-címtartományok](./api-server-authorized-ip-ranges.md)biztonságos hozzáférést biztosít az AK API-kiszolgálóhoz, a tűzfal nyilvános IP-címét fel kell vennie az engedélyezett IP-tartományba.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>UDR létrehozása ugrással Azure Firewall

Az Azure automatikusan irányítja a forgalmat az Azure-alhálózatok, a virtuális hálózatok és a helyszíni hálózatok között. Ha módosítani szeretné az Azure alapértelmezett útválasztását, hozzon létre egy útválasztási táblázatot.

Hozzon létre egy üres útválasztási táblázatot, amely egy adott alhálózathoz lesz társítva. Az útválasztási táblázat a következő ugrást fogja meghatározni a fent létrehozott Azure Firewall. Mindegyik alhálózattal nulla vagy egy útvonaltábla társítható.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

A [virtuális hálózati útválasztási táblázat dokumentációjában](../virtual-network/virtual-networks-udr-overview.md#user-defined) tájékozódhat arról, hogyan bírálhatja felül az Azure alapértelmezett rendszerútvonalait, vagy további útvonalakat adhat hozzá az alhálózat útválasztási táblájához.

### <a name="adding-firewall-rules"></a>Tűzfalszabályok hozzáadása

Az alábbi három hálózati szabály használható a tűzfalon való konfiguráláshoz, előfordulhat, hogy a központi telepítés alapján kell módosítania ezeket a szabályokat. Az első szabály a 9000-es port elérését teszi lehetővé a TCP protokollon keresztül. A második szabály lehetővé teszi a 1194-es és 123-as port elérését az UDP protokollon keresztül (ha üzembe helyezi az Azure China 21Vianet, [többre](#azure-china-21vianet-required-network-rules)is szükség lehet). Mindkét szabály csak az általunk használt Azure-régió CIDR irányuló forgalmat engedélyezi, ebben az esetben az USA keleti régiójában. Végül hozzáadunk egy harmadik hálózati szabályt, amely az 123-as portot az `ntp.ubuntu.com` UDP protokollal nyitja meg (FQDN-ként adja hozzá a teljes tartománynevet hálózati szabályként a Azure Firewall egyik sajátossága, és a saját beállítások használatakor módosítania kell).

A hálózati szabályok beállítása után egy, a `AzureKubernetesService` 443-es és a 80-es porton keresztül elérhető teljes tartománynevet tartalmazó szabályt is hozzáadunk az alkalmazáshoz.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

A Azure Firewall szolgáltatással kapcsolatos további információkért tekintse meg [Azure Firewall dokumentációját](../firewall/overview.md) .

### <a name="associate-the-route-table-to-aks"></a>Az útválasztási táblázat hozzárendelése AK-hoz

Ha a fürtöt a tűzfalhoz szeretné rendelni, a fürt alhálózatához tartozó dedikált alhálózatnak a fent létrehozott útválasztási táblára kell hivatkoznia. A társítást úgy teheti meg, hogy a fürtöt és a tűzfalat tároló virtuális hálózatra vonatkozó parancs kiadásával frissíti a fürt alhálózatának útválasztási táblázatát.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Az AK üzembe helyezése a kimenő UDR-típussal a meglévő hálózatra

Most már van egy AK-fürt üzembe helyezése a meglévő virtuális hálózatban. A [kimenő típust `userDefinedRouting` ](egress-outboundtype.md)is használni fogjuk, ez a funkció biztosítja, hogy a kimenő forgalom a tűzfalon keresztül kényszerítve legyen, és más kimenő útvonalak nem lesznek elérhetők (alapértelmezés szerint a Load Balancer kimenő típust lehet használni).

![AK – üzembe helyezés](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Egyszerű szolgáltatásnév létrehozása a meglévő virtuális hálózatban való üzembe helyezéshez

A rendszer a fürt identitását (felügyelt identitást vagy szolgáltatásnevet) használja a fürterőforrás létrehozásához. A létrehozás ideje alatt átadott egyszerű szolgáltatás a mögöttes AK-erőforrások, például a tárolási erőforrások, az IP-címek és a terheléselosztó által használt terheléselosztó létrehozásához használatos (Ehelyett [felügyelt identitást](use-managed-identity.md) is használhat). Ha nem kapta meg az alábbi megfelelő engedélyeket, nem fogja tudni kiépíteni az AK-fürtöt.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Most cserélje le az `APPID` és az `PASSWORD` alábbit az egyszerű szolgáltatásnév AppID és a szolgáltatás egyszerű jelszavára, amelyet az előző parancs kimenete automatikusan generált. A VNET erőforrás-AZONOSÍTÓra hivatkozunk, hogy megadja az engedélyeket az egyszerű szolgáltatásnak, hogy az AK-ban üzembe helyezhet erőforrásokat.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

[Itt](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)megtekintheti a szükséges részletes engedélyeket.

> [!NOTE]
> Ha a kubenet hálózati beépülő modult használja, meg kell adnia az AK szolgáltatás egyszerű vagy felügyelt identitási engedélyeit az előre létrehozott útválasztási táblázathoz, mivel a kubenet a letöltsük-útválasztási szabályok hozzáadásához útválasztási táblázatot igényel. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>AK üzembe helyezése

Végül az AK-fürt üzembe helyezhető a fürthöz dedikált meglévő alhálózaton. A rendszerbe központilag telepítendő célként megadott alhálózat a környezeti változóval van definiálva `$SUBNETID` . Nem definiálta a `$SUBNETID` változót az előző lépésekben. Az alhálózati azonosító értékének megadásához a következő parancsot használhatja:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

A kimenő típust úgy kell megadnia, hogy az alhálózaton már létező UDR használja. Ez a konfiguráció lehetővé teszi az ak-nak, hogy kihagyja a beállítást, és az IP-kiépítés a terheléselosztó számára.

> [!IMPORTANT]
> A kimenő típusokra vonatkozó UDR, beleértve a korlátozásokat, lásd: [**kimenő forgalom típusa UDR**](egress-outboundtype.md#limitations).


> [!TIP]
> További funkciók is hozzáadhatók a fürt üzembe helyezéséhez, például a [**privát fürthöz**](private-clusters.md). 
>
> Az [**API-kiszolgáló által engedélyezett IP-tartományokhoz**](api-server-authorized-ip-ranges.md) tartozó AK funkció hozzáadható az API-kiszolgáló hozzáférésének korlátozásához csak a tűzfal nyilvános végpontja számára. A jóváhagyott IP-címtartományok funkció a diagramon nem kötelező. Ha engedélyezi a jogosult IP-címtartomány használatát az API-kiszolgáló elérésének korlátozására, a fejlesztői eszközöknek Jumpbox kell használniuk a tűzfal virtuális hálózatáról, vagy az összes fejlesztői végpontot fel kell vennie az engedélyezett IP-tartományba.

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

Ha az előző lépésben az engedélyezett IP-tartományokat használta a fürthöz, hozzá kell adnia a fejlesztői eszközökhöz tartozó IP-címeket a jóváhagyott IP-címtartományok számára az AK-fürtök listájához, hogy onnan elérje az API-kiszolgálót. Egy másik lehetőség, hogy a tűzfal virtuális hálózatán belül egy külön alhálózaton belüli Jumpbox konfigurálja a szükséges eszközökkel.

Adjon hozzá egy másik IP-címet a jóváhagyott tartományokhoz a következő paranccsal

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Az `kubectl` újonnan létrehozott Kubernetes-fürthöz való kapcsolódáshoz használja az [az AK Get-hitelesítő adatok] [az-AK-Get-hitelesítőadats] parancsot. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Nyilvános szolgáltatás üzembe helyezése
Most már elindíthatja a szolgáltatásokat, és alkalmazásokat telepíthet a fürtre. Ebben a példában egy nyilvános szolgáltatást teszünk közzé, de belső [terheléselosztó](internal-lb.md)használatával is kiválaszthat egy belső szolgáltatást.

![Nyilvános szolgáltatás DNAT](media/limit-egress-traffic/aks-create-svc.png)

Telepítse az Azure szavazó app alkalmazást úgy, hogy az alábbi YAML másolja egy nevű fájlba `example.yaml` .

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

A szolgáltatás üzembe helyezése a futtatásával:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>DNAT-szabály hozzáadása a Azure Firewall

> [!IMPORTANT]
> Ha Azure Firewall használatával korlátozza a kimenő forgalom forgalmát, és egy felhasználó által megadott útvonalat (UDR) hoz létre az összes kimenő forgalom kikényszerítéséhez, akkor győződjön meg arról, hogy megfelelő DNAT-szabályt hoz létre a tűzfalban, hogy megfelelően engedélyezze a bejövő forgalmat. A Azure Firewall használata UDR megszakítja a bejövő beállításokat az aszimmetrikus útválasztás miatt. (A probléma akkor fordul elő, ha az AK-alhálózat alapértelmezett útvonala a tűzfal magánhálózati IP-címére mutat, de a következő típusú nyilvános terheléselosztó-bejövő vagy Kubernetes-szolgáltatást használja: terheléselosztó). Ebben az esetben a bejövő terheléselosztó forgalma a nyilvános IP-címén keresztül érkezik, a visszatérési útvonal azonban a tűzfal magánhálózati IP-címén halad át. Mivel a tűzfal állapot-nyilvántartó, eldobja a visszaadott csomagot, mert a tűzfal nem ismeri a létesített munkamenetet. Ha szeretné megtudni, hogyan integrálhatja a Azure Firewallt a bemenő vagy a Service Load balancerrel, tekintse meg a [Azure Firewall integrálása az Azure standard Load Balancer](../firewall/integrate-lb.md)-nal című


A bejövő kapcsolat konfigurálásához egy DNAT szabályt kell írni a Azure Firewallba. A fürthöz való csatlakozás teszteléséhez egy szabály van meghatározva a tűzfal előtér nyilvános IP-címére, hogy a belső szolgáltatás által közzétett belső IP-címhez irányítson.

A cél címe testreszabható, mert a tűzfal portja elérhető. A lefordított címnek a belső Load Balancer IP-címének kell lennie. A lefordított portnak a Kubernetes szolgáltatás számára elérhető portnak kell lennie.

Meg kell adnia a Kubernetes szolgáltatás által létrehozott terheléselosztó számára hozzárendelt belső IP-címet. A címek lekérése a futtatásával:

```bash
kubectl get services
```

A szükséges IP-cím a külső IP-oszlopban jelenik meg, a következőhöz hasonlóan.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

A szolgáltatás IP-címének lekérése a futtatásával:
```bash
SERVICE_IP=$(kubectl get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Adja hozzá a NAT-szabályt a futtatásával:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

A kapcsolat ellenőrzéséhez navigáljon a böngészőben a Azure Firewall előtér IP-címére.

Ekkor meg kell jelennie az AK-szavazati alkalmazásnak. Ebben a példában a tűzfal nyilvános IP-címe volt `52.253.228.132` .


![A képernyőképen az a K-S szavazási alkalmazás jelenik meg, amely a macskák, a kutyák és az alaphelyzetek, valamint az összesítések gombjaival rendelkezik](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure-erőforrások tisztításához törölje az AK-erőforráscsoport törlését.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogy milyen portokat és címeket kell engedélyezni, ha szeretné korlátozni a kimenő forgalmat a fürt számára. Azt is láthatja, hogyan védheti meg a kimenő forgalmat Azure Firewall használatával. 

Ha szükséges, általánosíthatja a fenti lépéseket, hogy továbbítsa a forgalmat az előnyben részesített kimenő megoldásra, a [kimenő típusok `userDefinedRoute` dokumentációja](egress-outboundtype.md)alapján.

Ha szeretné korlátozni, hogy a hüvelyek hogyan kommunikálnak egymással, és East-West a forgalom korlátozásait a fürtön belül, lásd: [biztonságos forgalom a hüvelyek között hálózati házirendek használatával][network-policy]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
