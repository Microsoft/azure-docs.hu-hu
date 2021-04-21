---
title: Container Insights-| Microsoft Docs
description: Ez a cikk bemutatja, hogyan engedélyezheti és konfigurálhatja a Container Insightsot, hogy jobban át tudja látni a tároló teljesítményét és a teljesítménnyel kapcsolatos azonosított problémákat.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: e0544232f40e93cce0705fff6814d29697a96218
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782116"
---
# <a name="enable-container-insights"></a>Container Insights engedélyezése

Ez a cikk áttekintést nyújt a Container Insights beállítási lehetőségeiről, amelyek a Kubernetes-környezetekben üzembe helyezett és a következőn üzemeltetett számítási feladatok teljesítményének monitorzása érdekében használhatók:

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 3.x és 4.x verziók  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4.x verzió  
- [Arc-kompatibilis Kubernetes-fürt](../../azure-arc/kubernetes/overview.md)

A saját maga által felügyelt Kubernetes-fürtökön üzembe helyezett számítási feladatok teljesítményét is figyelheti:
- Azure, az [AKS-motor használatával](https://github.com/Azure/aks-engine)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) vagy helyszíni környezetben az AKS-motor használatával.

A Következő támogatott módszerek bármelyikével engedélyezheti a Container Insightsot egy új üzemelő példányhoz vagy a Kubernetes egy vagy több meglévő üzemelő példánya számára:

- Azure Portal
- Azure PowerShell
- Azure CLI
- [Terraform és AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy megfelel az alábbi követelményeknek:

> [!IMPORTANT]
> A Log Analytics tárolóba ezett Linux-ügynöke (replikakészlet-pod) API-hívásokat intéz a fürt összes Windows-csomópontjához a fürtön belül, hogy összegyűjtse a csomópontokkal és tárolók teljesítményével kapcsolatos metrikákat. A Kubelet biztonságos portját (:10250) meg kell nyitni a fürt virtuális hálózatában, hogy a Windows-csomópont és a tároló teljesítményével kapcsolatos metrikák gyűjtése működjön a bejövő és kimenő forgalom számára is.
>
> Ha Windows-csomópontokkal rendelkezik egy Kubernetes-fürttel, tekintse át és konfigurálja a hálózati biztonsági csoportot és a hálózati házirendeket, és győződjön meg arról, hogy a Kubelet biztonságos portja (:10250) meg van nyitva a fürt virtuális hálózatának bejövő és kimenő portja számára is.


- Van egy Log Analytics-munkaterülete.

   A tárolóelemzések támogatják a Log Analytics-munkaterületeket a régiónként elérhető [termékek listában szereplő régiókban.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

   Létrehozhat egy munkaterületet, amikor engedélyezi az új AKS-fürt monitorozását, vagy hagyhatja, hogy a beállítási élmény egy alapértelmezett munkaterületet hozzon létre az AKS-fürt előfizetésének alapértelmezett erőforráscsoportján belül. 
   
   Ha úgy dönt, hogy saját maga hozza létre a munkaterületet, a következőn keresztül hozhatja létre: 
   - [Azure Resource Manager](../logs/resource-manager-workspace.md)
   - [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Az Azure Portal](../logs/quick-create-workspace.md) 
   
   Az alapértelmezett munkaterülethez használható támogatott leképezési párok listáját lásd: Régióleképezés a [Container Insightshoz.](container-insights-region-mapping.md)

- Ön tagja a *Log Analytics közreműködői* csoportjának a tárolófigyelés engedélyezéséhez. További információ a Log Analytics-munkaterületek hozzáférésének szabályozásával kapcsolatban: [Munkaterületek kezelése.](../logs/manage-access.md)

- Ön az AKS-fürterőforrás [ *Tulajdonos*](../../role-based-access-control/built-in-roles.md#owner) csoportjának tagja.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- A monitorozási adatok megtekintéséhez [*Log Analytics-olvasó*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörre van szükség a Log Analytics-munkaterületen, a Container Insights használatával konfigurálva.

- A Prometheus-metrikákat a rendszer alapértelmezés szerint nem gyűjti. Mielőtt [konfigurálja](container-insights-prometheus-integration.md) az ügynököt a metrikák gyűjtésére, fontos áttekinteni a [Prometheus](https://prometheus.io/) dokumentációját, hogy tudja, milyen adatok gyűjthetőek le, és milyen módszerek támogatottak.
- Az AKS-fürtök egy másik Azure-előfizetés Log Analytics-munkaterületéhez csatolhatóak ugyanabban az Azure AD-bérlőben. Ezt jelenleg nem lehet az Azure Portallal, de az Azure CLI-val vagy egy Resource Manager meg.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A Container Insights hivatalosan a következő konfigurációkat támogatja:

- Környezetek: Azure Red Hat OpenShift, a helyszíni Kubernetes, valamint az AKS-motor az Azure-ban és Azure Stack. További információkért lásd az [AKS-motort a Azure Stack.](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
- A Kubernetes és a támogatási szabályzat verziói ugyanazok, mint amelyek a Azure Kubernetes Service [(AKS) által támogatottak.](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Hálózati tűzfalra vonatkozó követelmények

Az alábbi táblázat azokat a proxy- és tűzfal-konfigurációs információkat sorolja fel, amelyek ahhoz szükségesek, hogy a tárolóba sorolt ügynök kommunikáljon a Container Insights használatával. Az ügynöktől kimenő összes hálózati forgalom a Azure Monitor.

|Ügynök erőforrása|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

Az alábbi táblázat a proxy- és tűzfalkonfigurációs adatokat sorolja fel a Azure China 21Vianet:

|Ügynök erőforrása|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Adatfeldolgozás |
| `*.oms.opinsights.azure.cn` | 443 | OMS-onboarding (OMS-onboarding) |
| `dc.services.visualstudio.com` | 443 | Az Azure Nyilvános felhőszolgáltatást használó ügynök-telemetri Application Insights |

Az alábbi táblázat az Azure US Government proxy- és tűzfal-konfigurációs információit sorolja fel:

|Ügynök erőforrása|Port |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Adatfeldolgozás |
| `*.oms.opinsights.azure.us` | 443 | OMS-onboarding (OMS-onboarding) |
| `dc.services.visualstudio.com` | 443 | Az Azure Nyilvános felhőszolgáltatást használó ügynök-telemetri Application Insights |

## <a name="components"></a>Összetevők

A teljesítmény figyelése egy tárolóba ezett Log Analytics-ügynökre támaszkodik Linuxon, amelyet kifejezetten a Container Insightshoz fejlesztettek ki. Ez a speciális ügynök a fürt összes csomópontja teljesítmény- és eseményadatokat gyűjt, és az ügynököt a rendszer az üzembe helyezés során automatikusan telepíti és regisztrálja a megadott Log Analytics-munkaterületen. 

Az ügynök verziója microsoft/oms:ciprod04202018 vagy újabb, és ezt egy dátum képviseli a következő formátumban: *mmddyyyy*.

>[!NOTE]
>A Windows Server AKS-hez való általános támogatása révén a Windows Server-csomópontokkal felszerelt AKS-fürtök minden egyes Windows Server-csomópontján telepítve van egy előzetes verziójú ügynök démonkészlet-podként a naplók gyűjtéséhez és a Log Analyticsbe való továbbításához. A teljesítménymetrikák esetén a fürtben a normál üzembe helyezés részeként automatikusan üzembe helyezett Linux-csomópont összegyűjti és továbbítja az adatokat a Azure Monitor fürt összes Windows-csomópontja nevében.

Amikor az ügynök új verziója megjelenik, az automatikusan frissül a felügyelt Kubernetes-fürtökön, amelyek a Azure Kubernetes Service (AKS) vannak üzemeltetve. A kiadott verziók nyomon követéséhez tekintse meg az [ügynök kiadási közleményét.](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)

> [!NOTE]
> Ha már üzembe helyezett egy AKS-fürtöt, engedélyezte a monitorozást az Azure CLI vagy egy megadott Azure Resource Manager-sablon használatával, ahogyan azt a cikk későbbi cikke is bemutatja. Az nem használható az ügynök frissítéséhez, törléséhez, ismételt üzembe helyezéséhez `kubectl` vagy üzembe helyezéséhez.
>
> A sablont ugyanabban az erőforráscsoportban kell üzembe helyezni, mint a fürtöt.

A Container Insights engedélyezéséhez használja az alábbi táblázatban leírt módszerek egyikét:

| Üzembe helyezés állapota | Metódus | Leírás |
|------------------|--------|-------------|
| Új Kubernetes-fürt | [AKS-fürt létrehozása az Azure CLI használatával](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Az Azure CLI használatával létrehozhat egy új AKS-fürt monitorozását. |
| | [AKS-fürt létrehozása a Terraform használatával](container-insights-enable-new-cluster.md#enable-using-terraform)| A nyílt forráskódú Terraform eszközzel engedélyezheti a létrehozott új AKS-fürt monitorozását. |
| | [OpenShift-fürt létrehozása sablon Azure Resource Manager használatával](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | A létrehozott új OpenShift-fürtök figyelését egy előre konfigurált Azure Resource Manager engedélyezheti. |
| | [OpenShift-fürt létrehozása az Azure CLI használatával](/cli/azure/openshift#az_openshift_create) | Új OpenShift-fürt üzembe helyezésekor engedélyezheti a monitorozást az Azure CLI használatával. |
| Meglévő Kubernetes-fürt | [AKS-fürt monitorozásának engedélyezése az Azure CLI használatával](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Az Azure CLI használatával engedélyezheti a már üzembe helyezett AKS-fürtök monitorozását. |
| |[Engedélyezés AKS-fürthöz Terraform használatával](container-insights-enable-existing-clusters.md#enable-using-terraform) | A nyílt forráskódú Terraform eszköz használatával engedélyezheti a már üzembe helyezett AKS-fürtök monitorozását. |
| | [Engedélyezés AKS-fürtön Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Engedélyezheti a figyelés engedélyezését egy vagy több olyan AKS-fürt számára, amely már telepítve van a fürt többfürtlapján a Azure Monitor. |
| | [Engedélyezés AKS-fürtből](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| A monitorozást közvetlenül az AKS-fürtről engedélyezheti a Azure Portal. |
| | [Engedélyezés AKS-fürthöz Azure Resource Manager sablonnal](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Az AKS-fürtök figyelését egy előre konfigurált Azure Resource Manager engedélyezheti. |
| | [Engedélyezés hibrid Kubernetes-fürthöz](container-insights-hybrid-setup.md) | Engedélyezheti a Azure Stack vagy a helyszínen üzemeltetett Kubernetes-fürtön üzemeltetett AKS-motor monitorozását. |
| | [Engedélyezze az Arc-kompatibilis Kubernetes-fürtöt.](container-insights-enable-arc-enabled-clusters.md) | Engedélyezheti a monitorozást az Azure-n kívül üzemeltetett és az azure-ral engedélyezett Kubernetes-fürtök Azure Arc. |
| | [Engedélyezés OpenShift-fürthöz Azure Resource Manager sablonnal](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | A meglévő OpenShift-fürtök figyelését egy előre konfigurált Azure Resource Manager engedélyezheti. |
| | [Engedélyezés OpenShift-fürtön a Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Engedélyezheti a monitorozást egy vagy több olyan OpenShift-fürt számára, amely már üzembe van stb. a fürt több fürtözött Azure Monitor. |

## <a name="next-steps"></a>Következő lépések

Most, hogy engedélyezte a monitorozást, megkezdheti az Azure Kubernetes Service- (AKS-), Azure Stack- vagy más környezetben üzemeltetett Kubernetes-fürtök teljesítményének elemzését. A Container Insights használatával kapcsolatos további információkért lásd: [Kubernetes-fürt teljesítményének megtekintése.](container-insights-analyze.md)
