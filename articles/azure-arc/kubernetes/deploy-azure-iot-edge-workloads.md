---
title: Azure IoT Edge számítási feladatok üzembe helyezése (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 02/10/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure IoT Edge számítási feladatok üzembe helyezése
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.openlocfilehash: f228b79f14ab24281415cd4bd5964fc86a095d3c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390436"
---
# <a name="deploy-azure-iot-edge-workloads-preview"></a>Azure IoT Edge számítási feladatok üzembe helyezése (előzetes verzió)

## <a name="overview"></a>Áttekintés

Az Azure arc és a Azure IoT Edge könnyedén kiegészítik egymás képességeit. 

Az Azure arc mechanizmusokat biztosít a fürtszolgáltatások számára a fürtök alapvető összetevőinek konfigurálásához, valamint a fürtre vonatkozó házirendek alkalmazásához és érvényesítéséhez. 

Azure IoT Edge lehetővé teszi az alkalmazások kezelői számára, hogy a számítási feladatok távoli üzembe helyezését és felügyeletét a kényelmes Felhőbeli betöltéssel és a kétirányú kommunikációs primitívekkel végezzék. 

Az alábbi ábra az Azure arc és a Azure IoT Edge kapcsolatát szemlélteti:

![IoT ív konfigurálása](./media/edge-arc.png)

## <a name="pre-requisites"></a>Előfeltételek

* [Regisztráljon IoT Edge eszközt](../../iot-edge/quickstart-linux.md#register-an-iot-edge-device) , és [telepítse a szimulált hőmérséklet-érzékelő modult](../../iot-edge/quickstart-linux.md#deploy-a-module). Jegyezze fel az eszköz YAML a lent említett *Values.* .

* Használja [IoT Edge támogatását a Kubernetes](https://aka.ms/edgek8sdoc) számára az Azure arc Flux-kezelőjén keresztül történő üzembe helyezéshez.

* Töltse le a IoT Edge Helm diagram [*Values. YAML*](https://github.com/Azure/iotedge/blob/preview/iiot/kubernetes/charts/edge-kubernetes/values.yaml) fájlját, és cserélje le a `deviceConnectionString` fájl végén található helyőrzőt a korábban feljegyzett kapcsolatok karakterláncra. Szükség szerint állítsa be a többi támogatott diagram telepítési beállításait. Hozzon létre egy névteret a IoT Edge munkaterhelés számára, és hozzon létre egy titkos kulcsot:

  ```
  $ kubectl create ns iotedge

  $ kubectl create secret generic dcs --from-file=fully-qualified-path-to-values.yaml --namespace iotedge
  ```

  Azt is megteheti, hogy távolról is beállíthatja a [fürt konfigurációs példáját](./use-gitops-connected-cluster.md).

## <a name="connect-a-cluster"></a>Fürt összekötése

A `az` `connectedk8s` Kubernetes-fürtök Azure-beli összekapcsolásához használja az Azure CLI-bővítményt:

  ```
  az connectedk8s connect --name AzureArcIotEdge --resource-group AzureArcTest
  ```

## <a name="create-a-configuration-for-iot-edge"></a>Konfiguráció létrehozása IoT Edgehoz

A [példában szereplő git](https://github.com/veyalla/edgearc) -tárház az IoT Edge Helm diagramra mutat, és az előfeltételek szakaszban létrehozott titkos kulcsra hivatkozik.

Az `az` Azure CLI `k8sconfiguration` bővítmény használatával hozzon létre egy olyan konfigurációt, amely a csatlakoztatott fürtöt a git-tárházhoz csatolja:

  ```
  az k8sconfiguration create --name iotedge --cluster-name AzureArcIotEdge --resource-group AzureArcTest --operator-instance-name iotedge --operator-namespace azure-arc-iot-edge --enable-helm-operator --helm-operator-chart-version 0.6.0 --helm-operator-chart-values "--set helm.versions=v3" --repository-url "git://github.com/veyalla/edgearc.git" --cluster-scoped
  ```

Néhány percen belül meg kell jelennie a fürt névterében üzembe helyezett IoT Edge munkaterhelés-moduloknak `iotedge` . 

Tekintse meg a `SimulatedTemperatureSensor` névtérben található Pod-naplókat, és tekintse meg a generált minták értékét. A [Visual Studio Code-hoz készült Azure IoT hub Toolkit-bővítmény](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)használatával megtekintheti az IoT hub-ra érkező üzeneteket is.

## <a name="cleanup"></a>Felesleges tartalmak törlése

A konfiguráció eltávolítása a használatával:

```
az k8sconfiguration delete -g AzureArcTest --cluster-name AzureArcIotEdge --name iotedge
```

## <a name="next-steps"></a>Következő lépések

[A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
