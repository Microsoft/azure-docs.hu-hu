---
title: GPU használata az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan használhatók a GPU-k nagy teljesítményű számítási vagy grafikus igényű munkaterhelésekhez az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: d7e312f049acc0b74aa0a253864bfce6100044bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929140"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>A GPU használata nagy számítási igényű munkaterhelésekhez az Azure Kubernetes szolgáltatásban (ak)

A grafikus feldolgozási egységek (GPU-k) gyakran nagy számítási igényű munkaterhelésekhez, például grafikus és vizualizációs munkaterhelésekhez használatosak. Az AK támogatja a GPU-kompatibilis csomópont-készletek létrehozását a nagy számítási igényű munkaterhelések Kubernetes való futtatásához. További információ a GPU-t használó elérhető virtuális gépekről: GPU-ra optimalizált virtuálisgép- [méretek az Azure-ban][gpu-skus]. AK-csomópontok esetén a *Standard_NC6* minimális mérete javasolt.

> [!NOTE]
> A GPU-kompatibilis virtuális gépek olyan speciális hardvert tartalmaznak, amely magasabb díjszabási és régióbeli rendelkezésre állást biztosít. További információt a [díjszabási][azure-pricing] eszköz és a [régió rendelkezésre állása][azure-availability]című témakörben talál.

Jelenleg a GPU-t használó csomópont-készletek használata csak Linux-csomópontos készletek esetén érhető el.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel a GPU-t támogató csomópontokkal. Az AK-fürtnek a 1,10-es vagy újabb Kubernetes kell futnia. Ha szüksége van egy AK-fürtre, amely megfelel ezeknek a követelményeknek, tekintse meg a cikk első szakaszát [egy AK-fürt létrehozásához](#create-an-aks-cluster).

Szüksége lesz az Azure CLI 2.0.64 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ha olyan AK-fürtre van szüksége, amely megfelel a minimális követelményeknek (GPU-kompatibilis csomópont-és Kubernetes-verzió 1,10 vagy újabb), hajtsa végre az alábbi lépéseket. Ha már rendelkezik olyan AK-fürttel, amely megfelel ezeknek a követelményeknek, [ugorjon a következő szakaszra](#confirm-that-gpus-are-schedulable).

Először hozzon létre egy erőforráscsoportot a fürthöz az az [Group Create][az-group-create] paranccsal. A következő példa létrehoz egy erőforráscsoport-nevet *myResourceGroup* a *eastus* régióban:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy AK-fürtöt az az [AK Create][az-aks-create] paranccsal. A következő példa egy olyan fürtöt hoz létre, amely egyetlen méretű csomóponttal rendelkezik `Standard_NC6` :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Kérje le az AK-fürt hitelesítő adatait az az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>Az NVIDIA-eszköz beépülő moduljának telepítése

Mielőtt a csomópontokon a GPU-k használhatók legyenek, telepítenie kell egy Daemonset elemet az NVIDIA-eszköz beépülő modulhoz. Ez a Daemonset elemet minden csomóponton futtat egy Pod-t, hogy megadja a GPU-k számára szükséges illesztőprogramokat.

Először hozzon létre egy névteret a [kubectl Create Namespace][kubectl-create] paranccsal, például a *GPU-erőforrások* használatával:

```console
kubectl create namespace gpu-resources
```

Hozzon létre egy *NVIDIA-Device-plugin-DS. YAML* nevű fájlt, és illessze be a következő YAML-jegyzékbe. Ez a jegyzékfájl a [Kubernetes projekthez készült NVIDIA-eszköz beépülő modul][nvidia-github]részeként van megadva.

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: nvidia-device-plugin-daemonset
  namespace: gpu-resources
spec:
  selector:
    matchLabels:
      name: nvidia-device-plugin-ds
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      - key: nvidia.com/gpu
        operator: Exists
        effect: NoSchedule
      containers:
      - image: mcr.microsoft.com/oss/nvidia/k8s-device-plugin:1.11
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
```

Most a [kubectl Apply][kubectl-apply] paranccsal hozza létre a daemonset elemet, és győződjön meg róla, hogy az NVIDIA-eszköz beépülő modul sikeresen létrejött, ahogy az a következő példában látható:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Az AK speciális GPU-rendszerképének használata (előzetes verzió)

Ezen lépések alternatívájaként az AK egy teljesen konfigurált AK-rendszerképet biztosít, amely már tartalmazza a [Kubernetes készült NVIDIA-eszköz beépülő modult][nvidia-github].

> [!WARNING]
> Ne telepítse manuálisan az NVIDIA Device plugin Daemon-készletét a fürtökhöz az új AK speciális GPU-rendszerkép használatával.


Regisztrálja a `GPUDedicatedVHDPreview` szolgáltatást:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Több percet is igénybe vehet, amíg az állapot **regisztrálva** jelenik meg. A regisztrációs állapotot az az [Feature List](/cli/azure/feature#az-feature-list) parancs használatával tekintheti meg:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztrálva értékre van állítva, frissítse az `Microsoft.ContainerService` erőforrás-szolgáltató regisztrációját az az [Provider Register](/cli/azure/provider#az-provider-register) paranccsal:

```azurecli
az provider register --namespace Microsoft.ContainerService
```

A következő Azure CLI-parancsokkal telepítheti az AK-előnézeti CLI-bővítményt:

```azurecli
az extension add --name aks-preview
```

A következő Azure CLI-parancsokkal frissítheti az AK-előnézeti CLI-bővítményt:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Az AK speciális GPU-rendszerképének használata új fürtökön (előzetes verzió)    

Konfigurálja úgy a fürtöt, hogy a fürt létrehozásakor az AK speciális GPU-rendszerképet használja. Használja az `--aks-custom-headers` új fürt GPU-ügynök csomópontjainak jelölőjét az AK speciális GPU-rendszerkép használatára.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Ha a normál AK-lemezképek használatával szeretne fürtöt létrehozni, ezt az egyéni címke kihagyásával teheti meg `--aks-custom-headers` . Azt is megteheti, hogy az alábbi módon további speciális GPU Node-készleteket ad hozzá.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Az AK speciális GPU-rendszerképének használata meglévő fürtökön (előzetes verzió)

Konfiguráljon egy új csomópont-készletet az AK speciális GPU-rendszerkép használatára. Használja az `--aks-custom-headers` új csomóponton található GPU-ügynök csomópontjainak jelző jelzőjét az AK speciális GPU-rendszerkép használatára.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Ha a normál AK-lemezképek használatával szeretne csomópont-készletet létrehozni, ezt az egyéni címke kihagyása mellett teheti meg `--aks-custom-headers` . 

> [!NOTE]
> Ha a GPU SKU-hoz 2. generációs virtuális gépek szükségesek, a következőket teheti:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Ellenőrizze, hogy a GPU-k ütemezhető-e

A létrehozott AK-fürttel ellenőrizze, hogy a GPU-k ütemezhető-e a Kubernetes-ben. Először sorolja fel a fürt csomópontjait a [kubectl Get Nodes][kubectl-get] parancs használatával:

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Most a [kubectl leíró csomópont][kubectl-describe] paranccsal ellenőrizze, hogy a GPU-k ütemezhető-e. A *kapacitás* szakaszban a GPU-nak a következőképpen kell szerepelnie `nvidia.com/gpu:  1` .

A következő tömörített példa azt mutatja, hogy a GPU elérhető a (z) *nodepool1-18821093-0* nevű csomóponton:

```console
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 attachable-volumes-azure-disk:  24
 cpu:                            6
 ephemeral-storage:              101584140Ki
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         57713784Ki
 nvidia.com/gpu:                 1
 pods:                           110
Allocatable:
 attachable-volumes-azure-disk:  24
 cpu:                            5916m
 ephemeral-storage:              93619943269
 hugepages-1Gi:                  0
 hugepages-2Mi:                  0
 memory:                         51702904Ki
 nvidia.com/gpu:                 1
 pods:                           110
System Info:
 Machine ID:                 b0cd6fb49ffe4900b56ac8df2eaa0376
 System UUID:                486A1C08-C459-6F43-AD6B-E9CD0F8AEC17
 Boot ID:                    f134525f-385d-4b4e-89b8-989f3abb490b
 Kernel Version:             4.15.0-1040-azure
 OS Image:                   Ubuntu 16.04.6 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.12.7
 Kube-Proxy Version:         v1.12.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  kube-system                nvidia-device-plugin-daemonset-bbjlq     0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m39s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>GPU-t használó munkaterhelés futtatása

A GPU működés közbeni megtekintéséhez ütemezzen egy GPU-t használó munkaterhelést a megfelelő erőforrás-kéréssel. Ebben a példában egy [Tensorflow](https://www.tensorflow.org/) -feladatot futtatunk a [MNIST adatkészleten](http://yann.lecun.com/exdb/mnist/).

Hozzon létre egy *Samples-TF-mnist-demo. YAML* nevű fájlt, és illessze be a következő YAML-jegyzékbe. A következő feladattípus egy erőforrás-korlátot tartalmaz `nvidia.com/gpu: 1` :

> [!NOTE]
> Ha az eszközillesztők meghívásakor hibaüzenetet kap, például a CUDA-illesztőprogram verziószáma nem elegendő a CUDA futtatókörnyezet verziójához, tekintse át az NVIDIA illesztőprogram-mátrix kompatibilitási diagramját – [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Futtassa a feladatot a [kubectl Apply][kubectl-apply] parancs használatával. Ez a parancs elemzi a jegyzékfájlt, és létrehozza a definiált Kubernetes objektumokat:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>A GPU-t használó munkaterhelés állapotának és kimenetének megtekintése

Figyelje meg a feladat előrehaladását a [kubectl Get Jobs][kubectl-get] paranccsal az `--watch` argumentummal. Eltarthat néhány percig, amíg először lekéri a rendszerképet, és feldolgozza az adatkészletet. Ha a *Befejezés* oszlopban a *1/1* látható, a feladatot sikeresen befejezte. Kilépés a `kubetctl --watch` parancsból a *CTRL-C* paranccsal:

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

A GPU-t használó munkaterhelés kimenetének megjelenítéséhez először szerezze be a pod nevét a [kubectl Get hüvely][kubectl-get] paranccsal:

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Most a [kubectl logs][kubectl-logs] paranccsal tekintheti meg a pod-naplókat. Az alábbi példában szereplő Pod-naplók megerősítik, hogy a megfelelő GPU-eszköz fel lett derítve `Tesla K80` . Adja meg a saját Pod nevét:

```console
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-05-16 16:08:31.258328: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-05-16 16:08:31.396846: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties: 
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 2fd7:00:00.0
totalMemory: 11.17GiB freeMemory: 11.10GiB
2019-05-16 16:08:31.396886: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 2fd7:00:00.0, compute capability: 3.7)
2019-05-16 16:08:36.076962: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.1081
Accuracy at step 10: 0.7457
Accuracy at step 20: 0.8233
Accuracy at step 30: 0.8644
Accuracy at step 40: 0.8848
Accuracy at step 50: 0.8889
Accuracy at step 60: 0.8898
Accuracy at step 70: 0.8979
Accuracy at step 80: 0.9087
Accuracy at step 90: 0.9099
Adding run metadata for 99
Accuracy at step 100: 0.9125
Accuracy at step 110: 0.9184
Accuracy at step 120: 0.922
Accuracy at step 130: 0.9161
Accuracy at step 140: 0.9219
Accuracy at step 150: 0.9151
Accuracy at step 160: 0.9199
Accuracy at step 170: 0.9305
Accuracy at step 180: 0.9251
Accuracy at step 190: 0.9258
Adding run metadata for 199
Accuracy at step 200: 0.9315
Accuracy at step 210: 0.9361
Accuracy at step 220: 0.9357
Accuracy at step 230: 0.9392
Accuracy at step 240: 0.9387
Accuracy at step 250: 0.9401
Accuracy at step 260: 0.9398
Accuracy at step 270: 0.9407
Accuracy at step 280: 0.9434
Accuracy at step 290: 0.9447
Adding run metadata for 299
Accuracy at step 300: 0.9463
Accuracy at step 310: 0.943
Accuracy at step 320: 0.9439
Accuracy at step 330: 0.943
Accuracy at step 340: 0.9457
Accuracy at step 350: 0.9497
Accuracy at step 360: 0.9481
Accuracy at step 370: 0.9466
Accuracy at step 380: 0.9514
Accuracy at step 390: 0.948
Adding run metadata for 399
Accuracy at step 400: 0.9469
Accuracy at step 410: 0.9489
Accuracy at step 420: 0.9529
Accuracy at step 430: 0.9507
Accuracy at step 440: 0.9504
Accuracy at step 450: 0.951
Accuracy at step 460: 0.9512
Accuracy at step 470: 0.9539
Accuracy at step 480: 0.9533
Accuracy at step 490: 0.9494
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A cikkben létrehozott társított Kubernetes-objektumok eltávolításához használja a [kubectl delete Job][kubectl delete] parancsot a következő módon:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Következő lépések

Apache Spark feladatok futtatásához tekintse [meg Apache Spark feladatok futtatása az AK][aks-spark]-ban című témakört.

A Machine learning (ML) számítási feladatainak Kubernetes való futtatásával kapcsolatos további információkért lásd: [Kubeflow Labs][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/
[nvidia-github]: https://github.com/NVIDIA/k8s-device-plugin

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
