---
title: Azure arc-kompatibilis Kubernetes-ellenőrzési program
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: A Kubernetes-disztribúciók ív-ellenőrzési programjának ismertetése
keywords: Kubernetes, arc, Azure, K8s, érvényesítés
ms.openlocfilehash: 819df906add6275997e01fab310fe8dd57a87b51
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121371"
---
# <a name="azure-arc-validation-program"></a>Azure arc-ellenőrzési program

Az Azure arc-kompatibilis Kubernetes bármilyen Felhőbeli natív számítástechnikai alaprendszer (CNCF) tanúsítvánnyal rendelkező Kubernetes-fürttel működik. Az Azure arc csapata a legfontosabb iparági Kubernetes is dolgozott a szolgáltatók számára az Azure arc-kompatibilis Kubernetes ellenőrzéséhez a Kubernetes-disztribúciókkal. Az ezen szolgáltatók által kiadott Kubernetes-disztribúciók jövőbeli fő és alverziói az Azure arc-kompatibilis Kubernetes való kompatibilitás érdekében lesznek érvényesítve.

## <a name="validated-distributions"></a>Ellenőrzött eloszlások

A Microsoft által biztosított Kubernetes-disztribúciók és infrastruktúra-szolgáltatók sikeresen átadták az Azure arc-kompatibilis Kubernetes vonatkozó megfelelőségi teszteket:

| Terjesztés és infrastruktúra-szolgáltató | Verzió |
| ---------------------------------------- | ------- |
| Fürt API-szolgáltatója az Azure-ban            | Kiadás verziója: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12); Kubernetes verziója: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| AK Azure Stack HCI-ben                   | Kiadás verziója: [December 2020 frissítés](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012); Kubernetes verziója: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

A következő szolgáltatók és a hozzájuk tartozó Kubernetes-disztribúciók sikeresen átadták az Azure arc-kompatibilis Kubernetes vonatkozó megfelelőségi teszteket:

| Szolgáltató neve | Terjesztési név | Verzió |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift tárolóplatform](https://www.openshift.com/products/container-platform) | [4,5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4,6](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4,7](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes rács](https://tanzu.vmware.com/kubernetes-grid) | Kubernetes verziója: v 1.17.5 |
| Canonical    | [Bűbájos Kubernetes](https://ubuntu.com/kubernetes) | [1,19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE farmer      | [Farmer Kubernetes motor](https://rancher.com/products/rke/) | RKE CLI-verzió: [v 1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4); Kubernetes-verziók: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | 2.2.1-es verzió |

Az Azure arc csapata a következő nyilvános felhőalapú szolgáltatókon is futtatta a megfelelőségi teszteket és az ellenőrzött Azure arc-alapú Kubernetes forgatókönyveket:

| Nyilvános felhő szolgáltatójának neve | Terjesztési név | Verzió |
| -------------------------- | ----------------- | ------- |
| Amazon webszolgáltatások        | Rugalmas Kubernetes szolgáltatás (EKS) | v 1.18.9  |
| Google Cloud Platform      | Google Kubernetes Engine (GKE) | v 1.17.15 |

## <a name="scenarios-validated"></a>Érvényesített forgatókönyvek

Az Azure arc-kompatibilis Kubernetes ellenőrzésének részeként futtatott megfelelőségi tesztek a következő forgatókönyveket fedik le:

1. Kubernetes-fürtök összekötése az Azure arc használatával: 
    * Azure arc-alapú Kubernetes-ügynök Helm-diagramjának üzembe helyezése a fürtön.
    * A felügyelt rendszer-identitás (MSI) tanúsítványának beállítása a fürtön.
    * Az ügynökök a fürt metaadatait küldik az Azure-nak.

2. Konfigurálás: 
    * Hozzon létre konfigurációt az Azure arc-kompatibilis Kubernetes-erőforrás felett.
    * A GitOps-munkafolyamatok beállításához szükséges [fluxus](https://docs.fluxcd.io/)a fürtön van telepítve.
    * A Flux a bemutató git-tárházból beolvassa a jegyzékfájlokat és a Helm-diagramokat, és üzembe helyezi a fürtöt.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan csatlakoztatható a fürt az Azure arc szolgáltatáshoz.
> [!div class="nextstepaction"]
> [Fürt összekötése az Azure-ív használatával](./quickstart-connect-cluster.md)
