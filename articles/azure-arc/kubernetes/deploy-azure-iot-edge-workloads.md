---
title: Azure IoT Edge számítási feladatok üzembe helyezése (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge számítási feladatok üzembe helyezése
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.openlocfilehash: 88c480f93bfe28a424441a1c5857c623efb4e1d3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99091647"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Azure IoT Edge számítási feladatok üzembe helyezése (előzetes verzió)

## <a name="overview"></a>Áttekintés

Az Azure arc és a Azure IoT Edge nagyon jól kiegészíti egymás képességeit. Az Azure arc mechanizmusokat biztosít a fürtszolgáltatások számára a fürt alapvető összetevőinek konfigurálásához, valamint a fürtre vonatkozó házirendek alkalmazásához és érvényesítéséhez. A IoT Edge lehetővé teszi az alkalmazások kezelői számára a számítási feladatok távoli üzembe helyezését és kezelését a kényelmes Felhőbeli betöltéssel és a kétirányú kommunikációs primitívekkel. Az alábbi ábra a következőket szemlélteti:

![IoT ív konfigurálása](./media/edge-arc.png)

## <a name="pre-requisites"></a>Előfeltételek

* [Regisztráljon IoT Edge eszközt](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) , és [telepítse a szimulált hőmérséklet-érzékelő modult](../../iot-edge/quickstart-linux.md#deploy-a-module). Ügyeljen arra, hogy az eszköz kapcsolódási karakterláncát jegyezze fel.

* Használja [IoT Edge támogatását a Kubernetes](https://aka.ms/edgek8sdoc) számára az Azure arc Flux-kezelőjén keresztül történő üzembe helyezéshez.

* Töltse le a IoT Edge Helm diagram [**Values. YAML**](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) fájlját, és cserélje le a fájl végén található **deviceConnectionString** helyőrzőt az 1. lépésben feljegyzett értékre. Az egyéb támogatott diagramok telepítési beállításait igény szerint állíthatja be. Hozzon létre egy névteret a IoT Edge munkaterhelés számára, és hozzon létre egy titkos kulcsot:

    ```
    $ kubectl create ns iotedge

    $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
    ```

    Ezt a konfigurációt távolról is beállíthatja a [fürt konfigurációs példájának](./use-gitops-connected-cluster.md)használatával.

## <a name="connect-a-cluster"></a>Fürt összekötése

`az` `connectedk8s` Kubernetes-fürt és az Azure arc összekapcsolásához használja a CLI-bővítményt:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Konfiguráció létrehozása IoT Edgehoz

Példa a tárházra: https://github.com/veyalla/edgearc

Ez a tárház a IoT Edge Helm diagramra mutat, és az előfeltételek szakaszban létrehozott titkos kulcsra hivatkozik.

1. A `az` CLI `k8sconfiguration` bővítmény használatával hozzon létre egy konfigurációt, amely a csatlakoztatott fürtöt a git-tárházhoz kapcsolja:

    ```
    az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
    ```

    Egy-két percen belül meg kell jelennie a fürt névterében üzembe helyezett IoT Edge munkaterhelés-moduloknak `iotedge` . Az adott névtérben található Pod naplóit megtekintve megtekintheti `SimulatedTemperatureSensor` a generált minták értékeit. A [Visual Studio Code-hoz készült Azure IoT hub Toolkit-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)használatával megtekintheti az IoT hub-ra érkező üzeneteket is.

## <a name="cleanup"></a>Felesleges tartalmak törlése

A konfigurációt a használatával távolíthatja el:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Következő lépések

[A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
