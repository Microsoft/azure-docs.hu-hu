---
title: Egyéni helyszínek az Azure arc-kompatibilis Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions
description: Egyéni helyszínek használata az Azure-beli Pásti-szolgáltatások üzembe helyezéséhez az Azure arc-kompatibilis Kubernetes-fürtökön
ms.openlocfilehash: ddda6420acd7126cb46b043f5c1bce67758342bc
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451041"
---
# <a name="custom-locations-on-azure-arc-enabled-kubernetes"></a>Egyéni helyszínek az Azure arc-kompatibilis Kubernetes

Az Azure-beli hely kiterjesztéseként az *Egyéni helyek* lehetővé teszik a bérlői rendszergazdák számára, hogy az Azure-szolgáltatások példányainak üzembe helyezéséhez célként megadott helyként használják az Azure arc-kompatibilis Kubernetes-fürtöket. Az Azure-erőforrások példái közé tartozik az Azure arc használatára képes SQL felügyelt példány és az Azure arc engedélyezve PostgreSQL nagy kapacitású.

Az Azure-beli helyekhez hasonlóan a bérlőn belül az egyéni helyekhez való hozzáféréssel rendelkező végfelhasználók is üzembe helyezhetik az erőforrásokat a vállalat saját számítási szolgáltatásával.

A szolgáltatás fogalmi áttekintése az [Egyéni helyszíneken érhető el – Azure arc-kompatibilis Kubernetes-](conceptual-custom-locations.md) cikk.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Telepítse vagy frissítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t a következő verzióra: >= 2.16.0.

- `connectedk8s` (verzió: >= 1.1.0), `k8s-extension` (version >= 0.2.0) és `customlocation` (Version >= 0.1.0) Azure CLI-bővítmények. Telepítse ezeket az Azure CLI-bővítményeket a következő parancsok futtatásával:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    Ha a `connectedk8s` `k8s-extension` és a `customlocation` bővítmények már telepítve vannak, a következő parancs használatával frissítheti azokat a legújabb verzióra:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- A szolgáltató regisztrációja befejeződött `Microsoft.ExtendedLocation` .
    1. Írja be a következő parancsokat:
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. Figyelje a regisztrációs folyamatot. A regisztráció akár 10 percet is igénybe vehet.
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

>[!NOTE]
>**Támogatott régiók az egyéni helyszínekhez:**
>* USA keleti régiója
>* Nyugat-Európa

## <a name="enable-custom-locations-on-cluster"></a>Egyéni helyszínek engedélyezése a fürtön

Ha engedélyezni szeretné a szolgáltatást a fürtön, hajtsa végre a következő parancsot:

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

> [!NOTE]
> 1. Az egyéni helyszínek funkció a fürt csatlakozási funkciójával függ. Ezért mindkét szolgáltatást engedélyezni kell az egyéni helyeknek való működéshez.
> 2. `az connectedk8s enable-features` olyan gépen kell futnia, ahol a `kubeconfig` fájl arra a fürtre mutat, amelyen engedélyezni szeretné a szolgáltatásokat.

## <a name="create-custom-location"></a>Egyéni hely létrehozása

1. Hozzon létre egy Azure arc-kompatibilis Kubernetes-fürtöt.
    - Ha még nem csatlakoztatta a fürtöt, használja a gyors üzembe helyezési [útmutatót.](quickstart-connect-cluster.md)
    - [Frissítse az ügynököket](agent-upgrade.md#manually-upgrade-agents) a >= 1.1.0 verzióra.

1. Telepítse az Azure-szolgáltatás fürtszolgáltatását, amelynek a példányát végül az egyéni hely tetején szeretné használni:

    ```azurecli
    az k8s-extension create --name <extensionInstanceName> --extension-type microsoft.arcdataservices --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName> --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper
    ```

    > [!NOTE]
    > A kimenő proxy hitelesítés nélkül, a kimenő proxy és az alapszintű hitelesítés használata esetén az ív engedélyezve Data Services a fürt kiterjesztését támogatja. A megbízható tanúsítványokat elvárt kimenő proxy jelenleg nem támogatott.

1. Szerezze be az Azure arc-kompatibilis Kubernetes-fürt Azure Resource Manager-azonosítóját, amelyet később a következő lépések követnek `connectedClusterId` :

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Szerezze be az Azure arc-kompatibilis Kubernetes-fürtön üzembe helyezett fürtszolgáltatás Azure Resource Manager-azonosítóját, amely a későbbi lépésekben a következő módon hivatkozik `extensionId` :

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. Hozzon létre egyéni helyet az Azure arc-kompatibilis Kubernetes-fürtre és a bővítményre való hivatkozással:

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> Biztonságos kapcsolódás a fürthöz a [fürt csatlakoztatása](cluster-connect.md) használatával