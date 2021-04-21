---
title: GPU-k használata Azure Kubernetes Service (AKS)
description: Megtudhatja, hogyan használhatja a GPU-kat nagy teljesítményű számítási feladatokhoz vagy nagy grafikai igényű számítási feladatokhoz Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/21/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 5e36465c307443c8e6f135c5937bddbbb079b60e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783160"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>GPU-k használata nagy számítási igényű számítási feladatokhoz Azure Kubernetes Service (AKS)

A grafikus feldolgozási egységeket (GPU-kat) gyakran használják nagy számítási igényű számítási feladatokhoz, például grafikus és vizualizációs számítási feladatokhoz. Az AKS támogatja a GPU-kompatibilis csomópontkészletek létrehozását a nagy számítási igényű számítási feladatok Kubernetesben való futtatásához. További információ az elérhető GPU-kompatibilis virtuális gépekről: [GPU-optimalizált virtuálisgép-méretek az Azure-ban.][gpu-skus] AKS-csomópontok számára javasoljuk, hogy legalább a következőt *Standard_NC6.*

> [!NOTE]
> A GPU-kompatibilis virtuális gépek speciális hardvert tartalmaznak, amely magasabb díjszabás és a régiónkénti rendelkezésre állás függvényében áll rendelkezésre. További információkért lásd [a][azure-pricing] díjszabási eszközt és [a régió rendelkezésre állását.][azure-availability]

Jelenleg a GPU-kompatibilis csomópontkészletek használata csak Linux-csomópontkészletek esetén érhető el.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van olyan AKS-fürt, amely GPU-kat támogató csomópontokkal van. Az AKS-fürtnek a Kubernetes 1.10-es vagy újabb futtatását kell futtatnia. Ha olyan AKS-fürtre van szüksége, amely megfelel ezeknek a követelményeknek, tekintse meg a cikk első szakaszát egy [AKS-fürt létrehozásához.](#create-an-aks-cluster)

Emellett az Azure CLI 2.0.64-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ha olyan AKS-fürtre van szüksége, amely megfelel a minimális követelményeknek (GPU-kompatibilis csomópont és a Kubernetes 1.10-es vagy újabb verziója), kövesse az alábbi lépéseket. Ha már rendelkezik olyan AKS-fürtkel, amely megfelel ezeknek a követelményeknek, ugorjon a [következő szakaszra.](#confirm-that-gpus-are-schedulable)

Először hozzon létre egy erőforráscsoportot a fürthöz az [az group create paranccsal.][az-group-create] Az alábbi példa létrehoz egy *myResourceGroup nevű erőforráscsoportot* az *eastus régióban:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy AKS-fürtöt [az az aks create paranccsal.][az-aks-create] Az alábbi példa egy fürtöt hoz létre egyetlen méretű `Standard_NC6` csomóponttal:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1
```

Az az [aks get-credentials][az-aks-get-credentials] paranccsal szerezze be az AKS-fürt hitelesítő adatait:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="install-nvidia-device-plugin"></a>NVIDIA-eszköz beépülő modul telepítése

A csomópontokon lévő GPU-k használata előtt telepítenie kell egy DaemonSetet az NVIDIA-eszköz beépülő modulhoz. Ez a DaemonSet podot futtat minden csomóponton a GPU-khoz szükséges illesztőprogramok érdekében.

Először hozzon létre egy névteret a [kubectl create namespace paranccsal,][kubectl-create] például *a gpu-resources paranccsal:*

```console
kubectl create namespace gpu-resources
```

Hozzon létre egy *nvidia-device-plugin-ds.yaml nevű fájlt,* és illessze be a következő YAML-jegyzékfájlt. Ezt a jegyzékfájlt a [Kubernetes-projekthez][nvidia-github]elérhető NVIDIA-eszköz beépülő modul részeként biztosítjuk.

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

Most használja a [kubectl apply][kubectl-apply] parancsot a DaemonSet létrehozásához, és ellenőrizze, hogy az NVIDIA-eszköz beépülő modulja sikeresen létrejött-e, ahogy az alábbi kimenetben látható:

```console
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

## <a name="use-the-aks-specialized-gpu-image-preview"></a>Az AKS speciális GPU-rendszerképének használata (előzetes verzió)

A fenti lépések alternatívájaként az AKS egy teljes körűen konfigurált AKS-rendszerképet biztosít, amely már tartalmazza a [Kubernetes nvidia][nvidia-github]eszköz beépülő modulját.

> [!WARNING]
> Ne telepítse manuálisan az NVIDIA-eszköz beépülő modul démonkészletét a fürtökhöz az új speciális AKS GPU-rendszerkép használatával.


A funkció `GPUDedicatedVHDPreview` regisztrálása:

```azurecli
az feature register --name GPUDedicatedVHDPreview --namespace Microsoft.ContainerService
```

Eltarthat néhány percig, amíg az állapot Regisztrált **állapotúként fog mutatni.** A regisztrációs állapotot az az feature list paranccsal [ellenőrizheti:](/cli/azure/feature#az_feature_list)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/GPUDedicatedVHDPreview')].{Name:name,State:properties.state}"
```

Ha az állapot regisztráltként jelenik meg, frissítse az erőforrás-szolgáltató regisztrációját `Microsoft.ContainerService` az az provider register [paranccsal:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.ContainerService
```

Az aks-preview CLI-bővítmény telepítéséhez használja a következő Azure CLI-parancsokat:

```azurecli
az extension add --name aks-preview
```

Az aks-preview CLI-bővítmény frissítéséhez használja a következő Azure CLI-parancsokat:

```azurecli
az extension update --name aks-preview
```

### <a name="use-the-aks-specialized-gpu-image-on-new-clusters-preview"></a>Az AKS specializált GPU-rendszerképének használata új fürtökön (előzetes verzió)    

Konfigurálja a fürtöt az AKS speciális GPU-rendszerképének használatára a fürt létrehozásakor. Használja az új fürt GPU-ügynökcsomópontjainak jelzőt az `--aks-custom-headers` AKS speciális GPU-rendszerképének használatára.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Ha a normál AKS-rendszerképekkel szeretne fürtöt létrehozni, ezt az egyéni címke kihagyása után `--aks-custom-headers` használhatja. Az alábbiakban látható módon speciálisABB GPU-csomópontkészleteket is hozzáadhat.


### <a name="use-the-aks-specialized-gpu-image-on-existing-clusters-preview"></a>Az AKS specializált GPU-rendszerképének használata meglévő fürtökön (előzetes verzió)

Konfigurál egy új csomópontkészletet az AKS speciális GPU-rendszerképének használatára. Használja az új csomópontkészlet GPU-ügynökcsomópontjainak jelzőjét az `--aks-custom-headers` AKS speciális GPU-rendszerképének használatára.

```azurecli
az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true
```

Ha a normál AKS-rendszerképekkel szeretne létrehozni egy csomópontkészletet, ezt az egyéni címke kihagyása után `--aks-custom-headers` használhatja. 

> [!NOTE]
> Ha a GPU-termékváltozathoz 2. generációs virtuális gépekre van szükség, a következőt hozhatja létre:
> ```azurecli
> az aks nodepool add --name gpu --cluster-name myAKSCluster --resource-group myResourceGroup --node-vm-size Standard_NC6s_v2 --node-count 1 --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true
> ```

## <a name="confirm-that-gpus-are-schedulable"></a>Annak ellenőrzése, hogy a GPU-k nem hajthatóak-e el

Az AKS-fürt létrehozása után ellenőrizze, hogy a GPU-k nem használhatók-e a Kubernetesben. Először sorolja fel a fürtben lévő csomópontokat a [kubectl get nodes paranccsal:][kubectl-get]

```console
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   13m   v1.12.7
```

Most használja a [kubectl describe node][kubectl-describe] parancsot annak megerősítéséhez, hogy a GPU-k nem írhatók le. A *Kapacitás szakaszban* a GPU-nak a következőként kell listának lennie: `nvidia.com/gpu:  1` .

Az alábbi rövid példa azt mutatja, hogy a GPU elérhető az *aks-nodepool1-18821093-0 nevű csomóponton:*

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

## <a name="run-a-gpu-enabled-workload"></a>GPU-kompatibilis számítási feladat futtatása

A GPU használatának érvénybe lépéséhez ütemezhet egy GPU-kompatibilis számítási feladatot a megfelelő erőforrás-kéréssel. Ebben a példában futtasunk egy [Tensorflow-feladatot](https://www.tensorflow.org/) a [MNIST-adathalmaz.](http://yann.lecun.com/exdb/mnist/)

Hozzon létre egy *samples-tf-mnist-demo.yaml nevű* fájlt, és illessze be a következő YAML-jegyzékfájlt. Az alábbi feladatjegyzék a erőforráskorlátot `nvidia.com/gpu: 1` tartalmazza:

> [!NOTE]
> Ha verzió-eltérést kap az illesztőprogramok hívása során, például a CUDA-illesztő verziója nem elegendő a CUDA-futásidejű verzióhoz, tekintse át az NVIDIA-illesztőprogrammátrix kompatibilitási diagramját – [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

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

Futtassa a feladatot a [kubectl apply][kubectl-apply] paranccsal. Ez a parancs a jegyzékfájlt elemezi, és létrehozza a definiált Kubernetes-objektumokat:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>A GPU-kompatibilis számítási feladat állapotának és kimenetének megtekintése

Figyelje a feladat előrehaladását a [kubectl get jobs paranccsal][kubectl-get] a `--watch` argumentummal. Eltarthat néhány percig, hogy lekérte a rendszerképet, és feldolgozta az adatkészletet. Ha *a COMPLETIONS* *oszlopban az 1/1* érték látható, a feladat sikeresen befejeződött. Lépjen ki `kubetctl --watch` a parancsból *a Ctrl-C billentyűkombinációval:*

```console
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

A GPU-kompatibilis számítási feladat kimenetének nevére először szerezze be a pod nevét a [kubectl get pods paranccsal:][kubectl-get]

```console
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-mtd44   0/1     Completed   0          4m39s
```

Most használja a [kubectl logs parancsot][kubectl-logs] a podnaplók megtekintéséhez. Az alábbi podnapló-példa megerősíti, hogy a megfelelő GPU-eszköz van `Tesla K80` felderítve. Adja meg a saját podjának nevét:

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

A cikkben létrehozott társított Kubernetes-objektumok eltávolításához használja a [kubectl delete job parancsot][kubectl delete] a következőképpen:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="next-steps"></a>Következő lépések

A feladatok Apache Spark lásd: Run Apache Spark jobs on AKS (Feladatfuttassa a feladatokat [az AKS-on).][aks-spark]

A gépi tanulási (ML) számítási feladatok Kubernetesben való futtatásával kapcsolatos további információkért lásd: [Kubeflow Labs.][kubeflow-labs]

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
[az-group-create]: /cli/azure/group#az_group_create
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
