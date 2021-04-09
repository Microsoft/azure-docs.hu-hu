---
title: Azure Kubernetes hálózati szabályzatok | Microsoft Docs
description: Ismerje meg a Kubernetes-fürt védelmét szolgáló Kubernetes hálózati házirendeket.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a68e1a3f60930e290e97084ff2ec9350b18e2873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594969"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Az Azure Kubernetes hálózati házirendjeinek áttekintése

A hálózati házirendek lehetővé teszik a hüvelyek számára a mikro-szegmentálást, mint a hálózati biztonsági csoportok (NSG-EK) a virtuális gépek mikro-szegmentálását. Az Azure Network Policy Manager (más néven Azure NPM) implementációja a szabványos Kubernetes hálózati házirend-specifikációt támogatja. A címkék használatával kiválaszthatja a hüvelyek csoportjait, és meghatározhatja a bejövő és a kimenő szabályok listáját, hogy az ilyen hüvelyek felé irányuló és onnan érkező forgalmat is szűrni lehessen. További információ a Kubernetes hálózati házirendjeiről a [Kubernetes dokumentációjában](https://kubernetes.io/docs/concepts/services-networking/network-policies/).

![A Kubernetes hálózati házirendjeinek áttekintése](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Az Azure NPM implementációja az Azure CNI együtt működik, amely VNet-integrációt biztosít a tárolók számára. A NPM csak Linux rendszeren támogatott. A megvalósítás kikényszeríti a forgalmi szűrést úgy, hogy a definiált szabályzatok alapján beállítja az IP-szabályok engedélyezését és megtagadását a Linux iptables-ben Ezek a szabályok a Linux IPSets együtt vannak csoportosítva.

## <a name="planning-security-for-your-kubernetes-cluster"></a>A Kubernetes-fürt biztonságának megtervezése
A fürt biztonságának megvalósításakor a hálózati biztonsági csoportok (NSG-EK) használatával szűrheti a fürt alhálózatához beérkező és onnan távozó forgalmat (észak-déli forgalom). Használja az Azure NPM-t a fürtben található hüvelyek közötti adatforgalomhoz (kelet-nyugati forgalom).

## <a name="using-azure-npm"></a>Az Azure NPM használata
Az Azure NPM a következő módokon használható a hüvelyek mikro-szegmentálásának biztosítására.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
A NPM natív módon elérhető az AK-ban, és a fürt létrehozásakor engedélyezhető. További információ a [hüvelyek közötti biztonságos adatforgalomról az Azure Kubernetes szolgáltatásban (ak) lévő hálózati szabályzatok használatával](../aks/use-network-policies.md).

### <a name="aks-engine"></a>AK – motor
AKS-Engine egy olyan eszköz, amely egy Azure Resource Manager sablont hoz létre egy Kubernetes-fürt Azure-beli telepítéséhez. A fürtkonfiguráció egy JSON-fájlban van meghatározva, amelyet a sablon létrehozásakor a rendszer továbbít az eszköznek. A támogatott fürtbeállítások teljes listájával és a beállítások leírásával kapcsolatos további részletekért tekintse meg a Microsoft Azure Container Service Engine – Fürtdefiníció című részt.

Ha az ACS-Engine használatával telepített fürtökön szeretné engedélyezni a házirendeket, akkor a fürt definíciós fájljában válassza a networkPolicy beállítás értékét az "Azure" értékre.

#### <a name="example-configuration"></a>Konfigurációs példa

Az alábbi JSON-példa egy új virtuális hálózatot és alhálózatot hoz létre, és üzembe helyez egy Kubernetes-fürtöt az Azure CNI. Javasoljuk, hogy a JSON-fájl szerkesztéséhez használja a "Jegyzettömb" kifejezést. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Saját maga (DIY) Kubernetes-fürtök az Azure-ban
 A DIY fürtök esetében először telepítse a CNI beépülő modult, és engedélyezze azt a fürt összes virtuális gépén. Részletes információ: [A beépülő modul üzembe helyezése saját kezűleg üzembe helyezett Kubernetes-fürthöz](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Miután telepítette a fürtöt, futtassa a következő `kubectl` parancsot a fürtre beállított Azure NPM *Daemon* letöltéséhez és alkalmazásához.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
A megoldás szintén nyílt forráskódú, és a kód elérhető az [Azure Container Networking adattárában](https://github.com/Azure/azure-container-networking/tree/master/npm).

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Hálózati konfigurációk figyelése és megjelenítése az Azure NPM
Az Azure NPM olyan tájékoztató Prometheus-mérőszámokat tartalmaz, amelyek lehetővé teszik a konfigurációk figyelését és jobb megismerését. Beépített vizualizációkat biztosít a Azure Portal vagy a Grafana Labs szolgáltatásban. Ezek a metrikák a Azure Monitor vagy a Prometheus-kiszolgáló használatával is elindíthatók.

### <a name="benefits-of-azure-npm-metrics"></a>Az Azure NPM metrikáinak előnyei
A felhasználók korábban csak a parancssorban futtatott paranccsal tudtak megismerni a hálózati konfigurációt `iptables -L` , ami részletes és nehezen értelmezhető eredményt ad. A NPM-metrikák a következő előnyöket nyújtják a hálózati házirendekkel, az iptables-szabályokkal és a IPSets kapcsolatban.
- A három és egy idődimenzió közötti kapcsolat betekintést nyújt a konfiguráció hibakereséséhez.
- A bejegyzések száma az összes IPSets és az egyes IPSet.
- A szabályzat iptables/IPSet szintű részletességgel történő alkalmazásához szükséges idő.
 
### <a name="supported-metrics"></a>Támogatott metrikák
A támogatott metrikák listája a következő:

|Metrika neve |Leírás  |Prometheus-metrika típusa  |Címkék  |
|---------|---------|---------|---------|
|`npm_num_policies`     |hálózati házirendek száma          |Kijelző         |-         |
|`npm_num_iptables_rules`     | Iptables-szabályok száma     | Kijelző        |-         |         
|`npm_num_ipsets`     |IPSets száma         |Kijelző            |-         |
|`npm_num_ipset_entries`     |az összes IPSets található IP-címek bejegyzéseinek száma         |Kijelző         |-         |
|`npm_add_policy_exec_time`     |futtatókörnyezet hálózati házirend hozzáadásához         |Összefoglalás         |quantile (0,5, 0,9 vagy 0,99)         |
|`npm_add_iptables_rule_exec_time`     |az iptables-szabály hozzáadására szolgáló futtatókörnyezet         |Összefoglalás         |quantile (0,5, 0,9 vagy 0,99)         |
|`npm_add_ipset_exec_time`     |IPSet hozzáadására szolgáló futtatókörnyezet         |Összefoglalás         |quantile (0,5, 0,9 vagy 0,99)         |
|`npm_ipset_counts` speciális     |az egyes IPSet belüli bejegyzések száma         |GaugeVec         |név & kivonatának beállítása         |

A "exec_time" metrikák különböző quantile szintjei segítenek az általános és a legrosszabb esetek közötti különbségtételben.

Minden "exec_time" összegző metrika esetében létezik egy "exec_time_count" és "exec_time_sum" metrika is.

A metrikák Azure Monitor a tárolók vagy a Prometheus-n keresztül is megtekinthetők.

### <a name="setup-for-azure-monitor"></a>A Azure Monitor beállítása
Az első lépés az Azure Monitor engedélyezése a Kubernetes-fürthöz tartozó tárolók számára. A [Azure monitor a tárolók áttekintése című](../azure-monitor/containers/container-insights-overview.md)témakörben talál útmutatást. Ha engedélyezve van Azure Monitor a tárolók számára, konfigurálja a [tárolók ConfigMap Azure monitor](https://aka.ms/container-azm-ms-agentconfig) a NPM-integráció és a Prometheus NPM-metrikák gyűjtésének engedélyezéséhez. Az Azure monitor for containers ConfigMap tartalmaz egy ```integrations``` szakaszt, amely tartalmazza a NPM-metrikák gyűjtéséhez szükséges beállításokat. Ezek a beállítások alapértelmezés szerint le vannak tiltva a ConfigMap. Az alapszintű beállítás engedélyezésével az ```collect_basic_metrics = true``` alapszintű NPM mérőszámok gyűjtése történik. A speciális beállítás engedélyezése ```collect_advanced_metrics = true``` az alapmetrikák mellett speciális mérőszámokat is gyűjt. 

A ConfigMap szerkesztése után mentse helyileg, és alkalmazza a ConfigMap a fürtre a következőképpen.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Alább található egy kódrészlet az [Azure monitor for containers ConfigMap](https://aka.ms/container-azm-ms-agentconfig), amely az NPM integrációt mutatja be a speciális metrikák gyűjteményével.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
A speciális mérőszámok nem kötelezőek, és a bekapcsolás automatikusan bekapcsolja az alapszintű mérőszámok gyűjteményét. Jelenleg csak a speciális mérőszámok tartoznak ide `npm_ipset_counts`

További információ az [Azure monitor for containers Collection beállításairól a konfigurációs térképen](../azure-monitor/containers/container-insights-agent-config.md)

### <a name="visualization-options-for-azure-monitor"></a>Vizualizációs beállítások a Azure Monitor
Ha a NPM metrikáinak gyűjteménye engedélyezve van, a Azure Portal a tároló-és Grafana használatával megtekintheti a metrikákat.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Megtekintés a Azure Portal a fürt bepillantása alatt
Nyissa meg az Azure Portalt. Miután a fürtben megtalálta az adatait, navigáljon a "munkafüzetek" elemre, és nyissa meg a "hálózati házirend-kezelő (NPM) konfiguráció" című részt.

A munkafüzet megtekintése mellett (az alábbi képeken) közvetlenül is lekérdezheti a Prometheus-metrikákat az elemzések szakasz "naplók" szakaszában. Ez a lekérdezés például az összes összegyűjtött metrikát visszaküldi.
| ahol a TimeGenerated > ezelőtt (5h) | ahol a név tartalmazza a "npm_"

Log Analytics közvetlenül a metrikák esetében is lekérdezheti. További információ a [első lépésekról log Analytics lekérdezésekkel](../azure-monitor/containers/container-insights-log-search.md) 

#### <a name="viewing-in-grafana-dashboard"></a>Megtekintés a Grafana-irányítópulton
Állítsa be a Grafana-kiszolgálót, és konfiguráljon egy Log Analytics adatforrást az [itt](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource)leírtak szerint. Ezután importálja a [Grafana-irányítópultot egy log Analytics háttérrel](https://grafana.com/grafana/dashboards/10956) a Grafana Labs-be.

Az irányítópult az Azure-munkafüzethez hasonló vizualizációkat tartalmaz. Hozzáadhat paneleket a diagram & NPM-metrikák megjelenítéséhez a InsightsMetrics táblából.

### <a name="setup-for-prometheus-server"></a>A Prometheus-kiszolgáló beállítása
Egyes felhasználók dönthetnek úgy, hogy egy Prometheus-kiszolgálóval összegyűjtik a metrikákat a tárolók Azure Monitor helyett. A NPM-metrikák gyűjtéséhez csupán két feladatot kell felvennie a selejt-konfigurációba.

Egy egyszerű Prometheus-kiszolgáló telepítéséhez adja hozzá a Helm-tárházat a fürthöz
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
Ezután adjon hozzá egy kiszolgálót
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
ahol `prometheus-server-scrape-config.yaml` a a következőkből áll
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


A feladatot az alábbi tartalommal is lecserélheti, `azure-npm-node-metrics` vagy beépítheti egy már létező feladatokba a Kubernetes hüvelyek esetében:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>A Prometheus vizualizációs lehetőségei
A Prometheus-kiszolgálók csak a Grafana-irányítópult használata esetén támogatottak. 

Ha még nem tette meg, állítsa be a Grafana-kiszolgálót, és konfigurálja a Prometheus-adatforrást. Ezután importálja a [Grafana-irányítópultot egy Prometheus-háttérrel](https://grafana.com/grafana/dashboards/13000) a Grafana Labs-be.

Az irányítópult vizualizációi azonosak az irányítópulttal, amely egy tároló-és Log Analytics-háttérrel rendelkezik.

### <a name="sample-dashboards"></a>Minta irányítópultok
Az alábbiakban néhány példa a NPM metrikák a Container bepillantást a CI-ben és a Grafana

#### <a name="ci-summary-counts"></a>CI összegzések száma
![Azure-munkafüzetek összegzési száma](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>CI-számok az idő függvényében
[![Az Azure-munkafüzetek száma az idő függvényében](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet-bejegyzések
[![Azure-munkafüzetek IPSet bejegyzései](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI futásidejű Quantiles
![Azure-munkafüzet futtatókörnyezetének quantiles](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Grafana-irányítópult összesített száma
![Grafana-irányítópult összesített száma](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Grafana-irányítópultok száma az idő függvényében
[![Grafana-irányítópultok száma az idő függvényében](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana-irányítópult IPSet bejegyzései
[![Grafana-irányítópult IPSet bejegyzései](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana-irányítópult futásidejű Quantiles
[![Grafana-irányítópult futásidejű quantiles](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Következő lépések
- További tudnivalók az [Azure Kubernetes szolgáltatásról](../aks/intro-kubernetes.md).
-  A [tároló hálózatkezelésének](container-networking-overview.md)megismerése.
- [Telepítse a beépülő](deploy-container-networking.md) modult a Kubernetes-fürtök vagy a Docker-tárolók számára.

