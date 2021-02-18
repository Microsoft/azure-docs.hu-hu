---
title: Azure Monitor engedélyezése a tárolók számára | Microsoft Docs
description: Ez a cikk bemutatja, hogyan engedélyezheti és konfigurálhatja a tárolók Azure Monitorét, hogy megtudja, hogyan hajtja végre a tárolót, és hogy milyen teljesítménnyel kapcsolatos problémákat észlelt a rendszer.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 56f60b58cff351aa37e98cdba933c929aaaedab6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618788"
---
# <a name="enable-azure-monitor-for-containers"></a>Tárolók Azure Monitor engedélyezése

Ez a cikk áttekintést nyújt a tárolók Azure Monitorának beállításához rendelkezésre álló lehetőségekről a Kubernetes környezetekben üzembe helyezett munkaterhelések teljesítményének figyeléséhez, valamint a következő helyeken:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) -verziók 3. x és 4. x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4. x verzió  
- Egy [arc-kompatibilis Kubernetes-fürt](../../azure-arc/kubernetes/overview.md)

A szolgáltatásban üzemeltetett, önállóan felügyelt Kubernetes-fürtökön üzembe helyezett munkaterhelések teljesítményét is figyelemmel kísérheti:
- Azure, az AK- [motor](https://github.com/Azure/aks-engine) használatával
- [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) vagy helyszíni, az AK-motor használatával.

A következő támogatott módszerek bármelyikével engedélyezheti a tárolók Azure Monitorét egy új központi telepítéshez vagy egy vagy több Kubernetes meglévő központi telepítéséhez:

- Azure Portal
- Azure PowerShell
- Azure CLI
- [Terraform és AK](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy teljesítette a következő követelményeket:

> [!IMPORTANT]
> Log Analytics a tárolók Linux-ügynöke (REPLICASET Pod) API-hívásokat tesz lehetővé a fürtön belüli összes Windows-csomópont számára a Kubelet biztonságos portján (10250) a csomópontok és a tárolók teljesítményével kapcsolatos metrikák összegyűjtéséhez. A Kubelet biztonságos portot (: 10250) a fürt virtuális hálózatán kell megnyitni a Windows-csomópont bejövő és kimenő verziójában, valamint a tárolók teljesítményével kapcsolatos metrikák gyűjteményének működéséhez.
>
> Ha Windows-csomópontokkal rendelkező Kubernetes-fürttel rendelkezik, tekintse át és konfigurálja a hálózati biztonsági csoportot és a hálózati házirendeket, és győződjön meg arról, hogy a fürt virtuális hálózatában a Kubelet Secure port (: 10250) meg van nyitva mind a bejövő, mind a kimenő forgalomhoz.


- Log Analytics munkaterülettel rendelkezik.

   A tárolók Azure Monitor Log Analytics munkaterületet támogatnak a [régiókban elérhető termékekben](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiókban.

   Létrehozhat egy munkaterületet, amikor engedélyezi az új AK-fürt figyelését, vagy lehetővé teszi, hogy a bevezetési élmény hozzon létre egy alapértelmezett munkaterületet az AK-fürt előfizetés alapértelmezett erőforráscsoporthoz. 
   
   Ha saját maga hozza létre a munkaterületet, a következő módon hozhatja létre: 
   - [Azure Resource Manager](../samples/resource-manager-workspace.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Az Azure Portal](../learn/quick-create-workspace.md) 
   
   Az alapértelmezett munkaterülethez használt támogatott leképezési párok listáját a következő témakörben tekintheti meg: [régió leképezése Azure monitor for containers](container-insights-region-mapping.md).

- Tagja a *log Analytics közreműködői* csoportnak a tárolók figyelésének engedélyezéséhez. A Log Analytics munkaterület elérésének szabályozásáról a [munkaterületek kezelése](../platform/manage-access.md)című témakörben olvashat bővebben.

- Ön a [ *tulajdonos* csoport](../../role-based-access-control/built-in-roles.md#owner) tagja az AK-fürt erőforrásán.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- A figyelési adat megtekintéséhez [*log Analytics olvasó*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörrel kell rendelkeznie a log Analytics munkaterületen, amely a tárolók Azure monitorhoz van konfigurálva.

- A Prometheus-metrikák alapértelmezés szerint nincsenek összegyűjtve. Mielőtt [konfigurálja az ügynököt](container-insights-prometheus-integration.md) a metrikák gyűjtésére, fontos, hogy áttekintse a [Prometheus dokumentációját](https://prometheus.io/) , hogy megtudja, milyen adatok tölthetők le, és milyen módszerekkel támogatottak.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A tárolók Azure Monitor hivatalosan a következő konfigurációkat támogatják:

- Környezetek: az Azure Red Hat OpenShift, a helyszíni Kubernetes, valamint az Azure-ban és Azure Stack található AK-motor. További információkért tekintse [meg a Azure stack AK-motorját](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
- A Kubernetes és a támogatási szabályzat verziói ugyanazok, mint az [Azure Kubernetes szolgáltatásban (ak)](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények

A következő táblázat felsorolja azokat a proxy-és tűzfal-konfigurációs adatokat, amelyek szükségesek ahhoz, hogy a tároló ügynök Azure Monitor kommunikáljon a tárolókkal. Az ügynöktől érkező összes hálózati forgalom a Azure Monitor felé irányul.

|Ügynök erőforrása|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

A következő táblázat az Azure China 21Vianet proxy-és tűzfal-konfigurációs információit sorolja fel:

|Ügynök erőforrása|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Adatfeldolgozás |
| `*.oms.opinsights.azure.cn` | 443 | OMS bevezetése |
| `dc.services.visualstudio.com` | 443 | Az Azure Public Cloud Application Insightst használó ügynökök telemetria |

Az alábbi táblázat az Azure US government proxy-és tűzfal-konfigurációs információit sorolja fel:

|Ügynök erőforrása|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Adatfeldolgozás |
| `*.oms.opinsights.azure.us` | 443 | OMS bevezetése |
| `dc.services.visualstudio.com` | 443 | Az Azure Public Cloud Application Insightst használó ügynökök telemetria |

## <a name="components"></a>Összetevők

A teljesítmény monitorozásának lehetősége egy olyan, a Linux rendszerhez készült Log Analytics ügynök, amelyet kifejezetten a tárolók számára fejlesztettek ki Azure Monitor. Ez a speciális ügynök a fürt összes csomópontjának teljesítményét és eseményeit gyűjti, és az üzembe helyezés során a rendszer automatikusan telepíti és regisztrálja a megadott Log Analytics munkaterületet. 

Az ügynök verziója Microsoft/OMS: ciprod04202018 vagy újabb, és a formátum a következő formátumban jelenik meg: *mmddyyyy*.

>[!NOTE]
>Az AK-hoz készült Windows Server-támogatás általános elérhetősége esetén a Windows Server-csomópontokkal rendelkező AK-fürtök előnézeti ügynöke daemonset elemet Pod-ként van telepítve minden egyes Windows Server-csomóponton, hogy összegyűjtse a naplókat, és továbbítsa azt Log Analytics. A teljesítmény-mérőszámok esetében a szabványos központi telepítés részeként automatikusan üzembe helyezett Linux-csomópont gyűjti és továbbítja az adatokat Azure Monitor a fürt összes Windows-csomópontjának nevében.

Az ügynök új verziójának felszabadításakor a rendszer automatikusan frissíti az Azure Kubernetes szolgáltatásban (ak) üzemeltetett felügyelt Kubernetes-fürtökön. A kiadott verziók nyomon követéséhez tekintse meg az [ügynök kiadásával kapcsolatos hirdetményeket](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

> [!NOTE]
> Ha már telepített egy AK-fürtöt, akkor az Azure CLI vagy egy megadott Azure Resource Manager sablon használatával engedélyezte a figyelést, ahogyan azt a cikk későbbi részében is ismertetjük. Nem használható `kubectl` az ügynök frissítésére, törlésére, újbóli üzembe helyezésére vagy üzembe helyezésére.
>
> A sablont a fürttel azonos erőforráscsoporthoz kell telepíteni.

A tárolók Azure Monitorának engedélyezéséhez használja az alábbi táblázatban leírt módszerek egyikét:

| Központi telepítés állapota | Metódus | Leírás |
|------------------|--------|-------------|
| Új Kubernetes-fürt | [AK-fürt létrehozása az Azure CLI használatával](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Engedélyezheti az Azure CLI használatával létrehozott új AK-fürtök figyelését. |
| | [AK-fürt létrehozása a Terraform használatával](container-insights-enable-new-cluster.md#enable-using-terraform)| A nyílt forráskódú eszköz Terraform használatával engedélyezheti a figyelést a létrehozott új AK-fürtökhöz. |
| | [OpenShift-fürt létrehozása Azure Resource Manager sablon használatával](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Engedélyezheti az előre konfigurált Azure Resource Manager sablonnal létrehozott új OpenShift-fürtök figyelését. |
| | [OpenShift-fürt létrehozása az Azure CLI használatával](/cli/azure/openshift#az-openshift-create) | Ha új OpenShift-fürtöt telepít az Azure CLI használatával, akkor engedélyezheti a figyelést. |
| Meglévő Kubernetes-fürt | [AK-fürt figyelésének engedélyezése az Azure CLI használatával](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Az Azure CLI használatával már üzembe helyezett AK-fürtök figyelését is engedélyezheti. |
| |[Az AK-fürt engedélyezése a Terraform használatával](container-insights-enable-existing-clusters.md#enable-using-terraform) | A nyílt forráskódú eszköz Terraform használatával engedélyezheti a figyelést a már üzembe helyezett AK-fürtökön. |
| | [Az AK-fürtök engedélyezése Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Engedélyezheti a figyelést egy vagy több AK-fürtön, amelyek már telepítve vannak a Azure Monitor több fürtből álló oldaláról. |
| | [Engedélyezés az AK-fürtből](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| A figyelést közvetlenül a Azure Portal AK-fürtjéből is engedélyezheti. |
| | [Az AK-fürtök engedélyezése Azure Resource Manager sablon használatával](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Az AK-fürtök figyelését előre konfigurált Azure Resource Manager sablon használatával engedélyezheti. |
| | [Hibrid Kubernetes-fürt engedélyezése](container-insights-hybrid-setup.md) | Engedélyezheti a Azure Stack üzemeltetett, vagy a helyszínen üzemeltetett Kubernetes-fürtök figyelését. |
| | [Engedélyezze az ív használatára képes Kubernetes-fürtöt](container-insights-enable-arc-enabled-clusters.md). | Engedélyezheti az Azure-on kívül üzemeltetett Kubernetes-fürtök figyelését, és az Azure arc használatával engedélyezheti azokat. |
| | [OpenShift-fürt engedélyezése Azure Resource Manager sablon használatával](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Az előre konfigurált Azure Resource Manager sablonnal engedélyezheti a figyelést egy meglévő OpenShift-fürthöz. |
| | [OpenShift-fürt engedélyezése Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Engedélyezheti a figyelést egy vagy több olyan OpenShift-fürtön, amelyek már telepítve vannak a Azure Monitor több fürtből álló oldaláról. |

## <a name="next-steps"></a>Következő lépések

Most, hogy engedélyezte a figyelést, megkezdheti az Azure Kubernetes szolgáltatásban (ak), Azure Stack vagy más környezetben üzemeltetett Kubernetes-fürtök teljesítményének elemzését. A Azure Monitor for containers használatának megismeréséhez tekintse meg a [Kubernetes-fürt teljesítményének megtekintése](container-insights-analyze.md)című témakört.
