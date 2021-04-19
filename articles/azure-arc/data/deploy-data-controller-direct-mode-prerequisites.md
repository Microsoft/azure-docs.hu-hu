---
title: Előfeltételek | Közvetlen kapcsolódási mód
description: Az adatkezelő közvetlen kapcsolódási módban való üzembe helyezésének előfeltételei.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716312"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>Adatkezelő üzembe helyezése – közvetlen kapcsolódási mód (előfeltételek)

Ez a cikk azt ismerteti, hogyan készíthet elő egy adatkezelőt közvetlen Azure Arc-kompatibilis adatszolgáltatások módban való üzembe helyezésére.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Összefoglalva, az előfeltételek a következők:

1. Eszközök telepítése
1. Bővítmények hozzáadása
1. A szolgáltatásnév létrehozása és a metrikák szerepkörének konfigurálása
1. Kubernetes-fürt csatlakoztatása az Azure-hoz Azure Arc Kubernetes használatával

Az előfeltételek befejezése után üzembe helyezheti az Azure Arc [adatkezelőt, | Közvetlen kapcsolódási mód.](deploy-data-controller-direct-mode.md)

A cikk többi szakasza az előfeltételeket ismerteti.

## <a name="install-tools"></a>Eszközök telepítése

- Helm 3.3+ verzió ([telepítés](https://helm.sh/docs/intro/install/))
- Azure CLI ([telepítés](/sql/azdata/install/deploy-install-azdata))

## <a name="add-extensions-for-azure-cli"></a>Bővítmények hozzáadása az Azure CLI-hez

Emellett a következő az bővítményekre is szükség van:
- Azure `k8s-extension` CLI-bővítmény (0.2.0)
- Azure CLI `customlocation` (0.1.0)

A `az` minta és a CLI-bővítményei a következőek:

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>Szolgáltatásnév létrehozása és a metrikák szerepkörének konfigurálása

Kövesse a Metrikák feltöltése cikkben ismertetett lépéseket, [és](upload-metrics-and-logs-to-azure-monitor.md) hozzon létre egy szolgáltatásnévt, és adja meg a szerepköröket a cikkben leírtak szerint. 

Az SPN ClientID, TenantID és Client Secret adatokra lesz szükség az adatkezelő [Azure Arc telepítésekor.](deploy-data-controller-direct-mode.md) 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>Kubernetes-fürt csatlakoztatása az Azure-hoz Azure Arc Kubernetes használatával

A feladat végrehajtásához kövesse a Meglévő Kubernetes-fürt csatlakoztatása az [Azure Archoz című fejezet lépéseit.](../kubernetes/quickstart-connect-cluster.md)

## <a name="next-steps"></a>Következő lépések

Az előfeltételek befejezése után üzembe helyezheti az Azure Arc [adatkezelőt, | Közvetlen kapcsolódási mód.](deploy-data-controller-direct-mode.md)
