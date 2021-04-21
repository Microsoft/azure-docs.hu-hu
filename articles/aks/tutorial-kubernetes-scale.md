---
title: Azure-on futó Kubernetes oktatóanyag – Alkalmazások skálázása
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan skálázhat csomópontokat és podokat a Kubernetesben, és hogyan valósíthatja meg a podok horizontális felskálázását.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: a268d39ec514fc7b88b555221ece7dc044ca49ba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767510"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazások skálázása az Azure Kubernetes Service-ben (AKS)

Ha követte az oktatóanyagokat, van egy működő Kubernetes-fürtje az AKS-ban, és üzembe helyezett egy Azure-szavazóalkalmazás-mintaalkalmazást. Ebben az oktatóanyagban, amely egy hétrészes sorozat ötödik része, horizontálisan felskálázzuk az alkalmazás podjait, továbbá kipróbáljuk a podok automatikus méretezését. Emellett megismerjük, hogyan skálázható az Azure-beli virtuális gépek csomópontjainak száma a fürt kapacitásának beállításához a számítási feladatok futtatása érdekében. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * A Kubernetes-csomópontok skálázása
> * Az alkalmazást futtató Kubernetes-podok manuális skálázása
> * Az alkalmazás kezelőfelületét futtató automatikus skálázású podok konfigurálása

A későbbi oktatóanyagokban az Azure Vote alkalmazást új verzióra frissítjük.

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltak egy tároló-rendszerképbe. Ezt a képet feltöltötte Azure Container Registry, és létrehozott egy AKS-fürtöt. Az alkalmazást ezután üzembe helyezni az AKS-fürtön. Ha még nem tette meg ezeket a lépéseket, és szeretné követni őket, kezdje az 1. oktatóanyag – Tároló rendszerképek [létrehozása lépéssel.][aks-tutorial-prepare-app]

Ehhez az oktatóanyaghoz az Azure CLI 2.0.53-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="manually-scale-pods"></a>Podok manuális méretezése

Az előző oktatóanyagokban az Azure Vote előtérrendszerének és a Redis-példánynak a telepítésekor egyetlen replika jött létre. A fürtben lévő podok számának és állapotának megtekintéséhez használja a [kubectl get][kubectl-get] parancsot a következőképpen:

```console
kubectl get pods
```

A következő példa egy előtérbeli podot és egy háttérbeli podot mutat be:

```output
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Ha manuálisan szeretné módosítani az *azure-vote-front* üzemelő példány podjainak számát, használja a [kubectl scale][kubectl-scale] parancsot. A következő példa *5*-re növeli az előtérbeli podok számát:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Futtassa [ismét a kubectl get pods][kubectl-get] parancsokat annak ellenőrzéséhez, hogy az AKS sikeresen létrehozza-e a további podokat. Néhány perc múlva a podok elérhetők a fürtben:

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Podok automatikus méretezése

A Kubernetes támogatja a [podok horizontális felskálázását][kubernetes-hpa] a környezetekben a podok számának a processzorhasználat vagy egyéb megadott metrikák alapján való módosítása érdekében. A [Metrics Server][metrics-server] erőforrás-használatot biztosít a Kubernetes számára, üzembe helyezése pedig automatikusan megtörténik az 1.10-es vagy újabb verziójú AKS-fürtökön. Az AKS-fürt verziószámának megtekintéséhez használja az [az aks show][az-aks-show] parancsot az alábbi példában látható módon:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> Ha az AKS-fürt *1.10-esnél* kisebb, a rendszer nem telepíti automatikusan a Metrics Servert. A Metrics Server telepítési jegyzékei eszközként érhetők el a Metrics Server-kiadásokban, ami azt jelenti, hogy url-címen `components.yaml` keresztül telepítheti őket. További információ ezekről a YAML-definíciókról: Az olvasás [Üzembe][metrics-server-github] helyezés szakasza.
> 
> Példa a telepítésre:
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

Az automatikus skálázó csak akkor használható, ha a podok és a podok összes tárolója cpu-kéréseket és korlátokat határoz meg. Az üzembe helyezés során az előoldali tároló már `azure-vote-front` 0,25 CPU-t kér, a korlát pedig 0,5. Ezek az erőforráskérések és korlátok az alábbi példarészletben látható módon vannak definiálva:

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

Az alábbi példa a [kubectl autoscale][kubectl-autoscale] paranccsal automatikusan skálázza a podok számát az *azure-vote-front* üzemelő példányban. Ha az összes pod átlagos CPU-kihasználtsága meghaladja a kért használat 50%-át, az automatikus skálázási rendszer legfeljebb *10* példányig növeli a podok számát. Ezután legalább *3* példány lesz meghatározva az üzembe helyezéshez:

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Másik lehetőségként létrehozhat egy jegyzékfájlt az automatikus skálázási mód és az erőforráskorlátok meghatározásához. Az alábbiakban egy nevű jegyzékfájlra mutatunk `azure-vote-hpa.yaml` példát.

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

A jegyzékfájlban definiált automatikus skálázó `kubectl apply` alkalmazására `azure-vote-hpa.yaml` használja a következőt: .

```console
kubectl apply -f azure-vote-hpa.yaml
```

Az automatikus skálázó állapotának megtekintéséhez használja a `kubectl get hpa` parancsot a következőképpen:

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Néhány perc elteltével az Azure Vote alkalmazás minimális terhelése mellett a podreplikák száma automatikusan háromra csökken. Ismét használhatja a `kubectl get pods` parancsot a szükségtelen podok eltávolításának megtekintéséhez.

## <a name="manually-scale-aks-nodes"></a>AKS-csomópontok manuális méretezése

Ha az előző oktatóanyagban található parancsokkal hozta létre a Kubernetes-fürtöt, két csomóponttal rendelkezik. A csomópontok számát manuálisan módosíthatja, ha több vagy kevesebb, tárolókhoz kapcsolódó számítási feladatot tervez futtatni a fürtön.

A következő példában háromra növeljük a csomópontok számát a *myAKSCluster* nevű Kubernetes-fürtben. A parancs végrehajtása eltarthat néhány percig.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

Ha a fürt skálázva lett, a kimenet az alábbi példához hasonló:

```output
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban különböző méretezési funkciókat használtunk a Kubernetes-fürtben. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Az alkalmazást futtató Kubernetes-podok manuális skálázása
> * Az alkalmazás kezelőfelületét futtató automatikus skálázású podok konfigurálása
> * A Kubernetes-csomópontok manuális skálázása

A következő oktatóanyag azt mutatja be, hogyan frissítheti az alkalmazásokat a Kubernetesben.

> [!div class="nextstepaction"]
> [Alkalmazások frissítése a Kubernetesben][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
