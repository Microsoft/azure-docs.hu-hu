---
title: A containers Agent Azure Monitor kezelése | Microsoft Docs
description: Ez a cikk ismerteti a leggyakoribb karbantartási feladatok kezelését a Azure Monitor által a tárolók számára használt Log Analytics ügynökkel.
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: b656b0cc89e40dd732def4ebf56dceae69a033b0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620038"
---
# <a name="how-to-manage-the-azure-monitor-for-containers-agent"></a>A containers Agent Azure Monitor kezelése

A tárolók Azure Monitor a Linux Log Analytics ügynökének egy tárolós verzióját használja. A kezdeti üzembe helyezést követően a rendszer rutin vagy opcionális feladatokat hajt végre, amelyekre szükség lehet az életciklusa során. Ez a cikk azt ismerteti, hogyan lehet manuálisan frissíteni az ügynököt, és letiltani a környezeti változók gyűjteményét egy adott tárolóból. 

## <a name="how-to-upgrade-the-azure-monitor-for-containers-agent"></a>A Azure Monitor a tárolók ügynökének frissítése

A tárolók Azure Monitor a Linux Log Analytics ügynökének egy tárolós verzióját használja. Az ügynök új verziójának felszabadításakor az ügynök automatikusan frissül az Azure Kubernetes szolgáltatásban (ak) üzemeltetett felügyelt Kubernetes-fürtökön, valamint az Azure Red Hat OpenShift 3. x verziójában. A [hibrid Kubernetes-fürtök](container-insights-hybrid-setup.md) és az Azure Red Hat OpenShift 4. x verziója esetében az ügynök nem felügyelt, és manuálisan kell frissítenie az ügynököt.

Ha az ügynök frissítése meghiúsul az AK-ban vagy az Azure Red Hat OpenShift 3. x verziójában üzemeltetett fürt esetében, ez a cikk az ügynök manuális frissítésének folyamatát is ismerteti. A kiadott verziók követéséhez tekintse meg az [ügynök kiadási hirdetményei](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)című témakört.

### <a name="upgrade-agent-on-aks-cluster"></a>Ügynök frissítése az AK-fürtön

Az ügynök az AK-fürtökön való frissítésének folyamata két egyenes továbbítási lépésből áll. Első lépésként le kell tiltania a figyelést Azure Monitor az Azure CLI-vel rendelkező tárolók esetében. Kövesse a [figyelés letiltása](container-insights-optout.md?#azure-cli) című cikkben ismertetett lépéseket. Az Azure CLI használata lehetővé teszi, hogy az ügynököt a fürt csomópontjairól távolítsa el anélkül, hogy ez hatással lenne a megoldásra és a munkaterületen tárolt megfelelő adatokra. 

>[!NOTE]
>A karbantartási tevékenység végrehajtása közben a fürt csomópontjai nem továbbítják az összegyűjtött adatokat, és a teljesítmény nézetek nem jelenítik meg az ügynök eltávolítása és az új verzió telepítésének időpontja közötti adatokat. 
>

Az ügynök új verziójának telepítéséhez kövesse a [figyelés engedélyezése az Azure CLI](container-insights-enable-new-cluster.md#enable-using-azure-cli)-vel című cikkben ismertetett lépéseket a folyamat befejezéséhez.  

A figyelés ismételt engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt frissített állapotának mérőszámait. Az ügynök sikeres frissítésének ellenőrzéséhez az alábbiakat teheti:

* Futtassa a parancsot: `kubectl get pod <omsagent-pod-name> -n kube-system -o=jsonpath='{.spec.containers[0].image}'` . A visszaadott állapotban jegyezze fel a kimenet omsagent található **rendszerkép** *a következőben* :.
* A **csomópontok** lapon válassza ki a fürtcsomópont csomópontot, majd a jobb oldalon a **Tulajdonságok** ablaktáblán jegyezze fel az értéket az **ügynök képcímkéje** területen.

Az ügynök által megjelenített verziónak meg kell egyeznie a [kiadási előzmények](https://github.com/microsoft/docker-provider/tree/ci_feature_prod) lapon felsorolt legújabb verzióval.

### <a name="upgrade-agent-on-hybrid-kubernetes-cluster"></a>A hibrid Kubernetes-fürtön futó ügynök frissítése

A következő lépések végrehajtásával frissítheti az ügynököt a-t futtató Kubernetes-fürtökön:

* Az Azure-ban üzemeltetett, önfelügyelt Kubernetes-fürtök az AK motor használatával.
* A Azure Stackon vagy a helyszínen üzemeltetett, az AK-motorral rendelkező, önállóan felügyelt Kubernetes-fürtök.
* A Red Hat OpenShift 4-es verziója. x.

Ha a Log Analytics munkaterület a kereskedelmi Azure-ban található, futtassa a következő parancsot:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
```

Ha a Log Analytics munkaterület az Azure China 21Vianet található, futtassa a következő parancsot:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

Ha az Log Analytics munkaterület az Azure US Governmentben van, futtassa a következő parancsot:

```console
$ helm upgrade --name myrelease-1 \
--set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
```

### <a name="upgrade-agent-on-azure-red-hat-openshift-v4"></a>Az Azure Red Hat OpenShift v4-ügynök frissítése

Az alábbi lépések végrehajtásával frissítheti az ügynököt egy Azure Red Hat OpenShift 4. x verzión futó Kubernetes-fürtön. 

>[!NOTE]
>Az Azure Red Hat OpenShift 4. x verziója csak az Azure kereskedelmi felhőben való futtatást támogatja.
>

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $ azureAroV4ClusterResourceId
```

Tekintse meg az [Azure arc-kompatibilis Kubernetes-fürt figyelésének engedélyezése](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) az **egyszerű szolgáltatásnév használatával** című témakört, amely részletesen ismerteti az ezzel a paranccsal használható egyszerű szolgáltatásokat.

### <a name="upgrade-agent-on-azure-arc-enabled-kubernetes"></a>Ügynök frissítése az Azure arc-kompatibilis Kubernetes

A következő parancs végrehajtásával frissítheti az ügynököt egy Azure arc-kompatibilis Kubernetes-fürtön.

```console
curl -o upgrade-monitoring.sh -L https://aka.ms/upgrade-monitoring-bash-script
export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
bash upgrade-monitoring.sh --resource-id $azureArcClusterResourceId
```

Tekintse meg az [Azure arc-kompatibilis Kubernetes-fürt figyelésének engedélyezése](container-insights-enable-arc-enabled-clusters.md#enable-using-bash-script) az **egyszerű szolgáltatásnév használatával** című témakört, amely részletesen ismerteti az ezzel a paranccsal használható egyszerű szolgáltatásokat.


## <a name="how-to-disable-environment-variable-collection-on-a-container"></a>Környezeti változók gyűjteményének letiltása tárolón

A tárolók Azure Monitor környezeti változókat gyűjtenek a hüvelyben futó tárolókban, és **a tárolók nézetben a** kiválasztott tároló tulajdonságok ablaktábláján jelenítik meg azokat. Ezt a viselkedést úgy szabályozhatja, ha letilt egy adott tárolóhoz tartozó gyűjteményt a Kubernetes-fürt telepítése során vagy a *AZMON_COLLECT_ENV* környezeti változó beállításával. Ez a funkció az ügynök verziójában érhető el – ciprod11292018 és újabb.  

Ha le szeretné tiltani a környezeti változók új vagy meglévő tárolón való gyűjtését, állítsa be az **AZMON_COLLECT_ENV** változó értékét **false** értékre a Kubernetes telepítési YAML konfigurációs fájljában. 

```yaml
- name: AZMON_COLLECT_ENV  
  value: "False"  
```

Futtassa a következő parancsot, hogy alkalmazza a módosítást az Azure Red Hat OpenShift eltérő Kubernetes-fürtökre: `kubectl apply -f  <path to yaml file>` . A ConfigMap szerkesztéséhez és az Azure Red Hat OpenShift-fürtökre vonatkozó módosítás alkalmazásához futtassa a következő parancsot:

```bash
oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Ekkor megnyílik az alapértelmezett szövegszerkesztő. A változó beállítása után mentse a fájlt a szerkesztőbe.

A konfiguráció módosításának ellenőrzéséhez jelöljön ki egy tárolót **Azure monitor tárolók nézetben,** és a Tulajdonságok panelen bontsa ki a **környezeti változók** elemet.  A szakasznak csak a korábban létrehozott változót kell megjelenítenie – **AZMON_COLLECT_ENV = false**. Az összes többi tároló esetében a környezeti változók szakaszban fel kell sorolni az összes felderített környezeti változót.

A környezeti változók felderítésének újbóli engedélyezéséhez alkalmazza ugyanezt a folyamatot, és módosítsa a **hamis** értéket **true** értékre, majd futtassa újra a `kubectl` parancsot a tároló frissítéséhez.  

```yaml
- name: AZMON_COLLECT_ENV  
  value: "True"  
```  

## <a name="next-steps"></a>Következő lépések

Ha problémák merülnek fel az ügynök frissítésekor, tekintse át a támogatási [hibaelhárítási útmutatót](container-insights-troubleshoot.md) .
