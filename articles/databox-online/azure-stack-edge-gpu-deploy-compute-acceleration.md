---
title: A számítási gyorsulás GPU-t vagy VPU-t használhat Azure Stack Edge-eszközökön a Kubernetes üzembe helyezéséhez | Microsoft Docs
description: Ismerteti, hogyan használható a számítási gyorsulási GPU vagy a VPU a Azure Stack Edge Pro GPU-ban, Azure Stack Edge Pro R vagy Azure Stack Edge mini ri Kubernetes üzemelő példányokhoz.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/05/2020
ms.author: alkohli
ms.openlocfilehash: 0aaad18ba5bf98ca2ad53bd86605dfc6cce3e52c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466536"
---
# <a name="use-compute-acceleration-on-azure-stack-edge-pro-gpu-for-kubernetes-deployment"></a>A Kubernetes üzembe helyezéséhez használja a számítási gyorsítást Azure Stack Edge Pro GPU-ban

Ez a cikk azt ismerteti, hogyan használható a számítási gyorsítás Azure Stack peremhálózati eszközökön Kubernetes-telepítések használata esetén. A cikk Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkozik.


## <a name="about-compute-acceleration"></a>A számítási gyorsítás ismertetése 

A központi feldolgozó egység (CPU) az alapértelmezett általános célú számítás a számítógépen futó legtöbb folyamat esetében. A speciális számítógépes hardverek gyakran több funkció hatékonyabb elvégzésére szolgálnak, mint a CPU-ban lévő szoftverekben. A képfeldolgozó egységek (GPU) például felhasználhatók a képpont típusú adatfeldolgozás felgyorsítására.  

A számítási gyorsítás olyan kifejezés, amelyet kifejezetten Azure Stack Edge-eszközökhöz használunk, ahol grafikus feldolgozási egység (GPU), egy látási feldolgozó egység (VPU) vagy egy mező, programozható Gate array (FPGA) használható a hardveres gyorsításhoz. Az Azure Stack Edge-eszközön üzembe helyezett legtöbb számítási feladat kritikus időzítéssel, több kamerás streamtel és/vagy magas képaránysal rendelkezik, amelyek mindegyike adott hardveres gyorsítást igényel.

A cikk a számítási gyorsulást csak a GPU vagy a VPU használatával tárgyalja a következő eszközökhöz:

- **Azure stack Edge Pro GPU** – ezeknek az eszközöknek 1 vagy 2 NVIDIA T4-s GPU-val kell rendelkezniük. További információ: [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/).
- **Azure stack Edge Pro R** – ezek az eszközök 1 NVIDIA T4 tenser Core GPU-val rendelkeznek. További információ: [NVIDIA T4](https://www.nvidia.com/data-center/tesla-t4/).
- **Azure stack Edge mini R** – ezek az eszközök 1 Intel Movidius SZÁMTALAN X VPU rendelkeznek. További információ: az [Intel Movidius számtalan X VPU](https://www.movidius.com/MyriadX).


## <a name="use-gpu-for-kubernetes-deployment"></a>GPU használata a Kubernetes üzembe helyezéséhez

A következő példa használható `yaml` Azure stack Edge Pro GPU-hoz vagy egy Azure stack Edge Pro R-eszközhöz GPU-val.

<!--In a production scenario, Pods are not used directly and these are wrapped around higher level constructs like Deployment, ReplicaSet which maintain the desired state in case of pod restarts, failures.-->

```yml
apiVersion: v1
kind: Pod
metadata:
  name: gpu-pod
spec:
  containers:
    - name: cuda-container
      image: nvidia/cuda:9.0-devel
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
    - name: digits-container
      image: nvidia/digits:6.0
      resources:
        limits:
          nvidia.com/gpu: 1 # requesting 1 GPU
```


## <a name="use-vpu-for-kubernetes-deployment"></a>A VPU használata a Kubernetes üzembe helyezéséhez

A következő példa egy `yaml` VPU rendelkező Azure stack Edge mini R-eszközhöz használható.

```yml
apiVersion: batch/v1
kind: Job
metadata:
 name: intelvpu-demo-job
 labels:
   jobgroup: intelvpu-demo
spec:
 template:
   metadata:
     labels:
       jobgroup: intelvpu-demo
   spec:
     restartPolicy: Never
     containers:
       -
         name: intelvpu-demo-job-1
         image: ubuntu-demo-openvino:devel
         imagePullPolicy: IfNotPresent
         command: [ "/do_classification.sh" ]
         resources:
           limits:
             vpu.intel.com/hddl: 1
```


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [használhatja a kubectl egy Kubernetes állapot-nyilvántartó alkalmazás futtatására a Azure stack Edge Pro GPU-eszköz PersistentVolume](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md).
