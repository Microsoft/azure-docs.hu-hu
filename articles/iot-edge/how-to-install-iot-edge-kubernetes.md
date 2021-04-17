---
title: Alkalmazások telepítése IoT Edge Kubernetes-| Microsoft Docs
description: Megtudhatja, hogyan telepíthet IoT Edge kubernetes-beli virtuális hálózatokat helyi fejlesztési fürtkörnyezet használatával
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: a5407c46be6726974a920b5dddd87639dc426d0d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587255"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Alkalmazás telepítése IoT Edge Kubernetesen (előzetes verzió)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

IoT Edge kubernetes-sel integrálható, és rugalmas, magas rendelkezésre állékonyságú infrastruktúrarétegként használható. Ez a támogatás itt illeszkedik egy magas szintű IoT Edge megoldásba:

![k8s-bevezetés](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Ehhez az integrációhoz jó szellemi modell a Kubernetes mint egy másik üzemeltetési környezet, IoT Edge linuxos és windowsos alkalmazások is futtathatók.

## <a name="architecture"></a>Architektúra 
A Kubernetesben a IoT Edge *erőforrás-definíciót* (CRD) biztosít a peremhálózati számítási feladatok üzembe helyezéséhez. IoT Edge Agent egy  *CRD-vezérlő* szerepét feltételezi, amely egyezteti a felhő által felügyelt célállapotot a helyi fürt állapotával.

A modul élettartamát a Kubernetes ütemező kezeli, amely fenntartja a modul rendelkezésre állását, és kiválasztja az elhelyezésüket. IoT Edge felügyeli a felül futó peremhálózati alkalmazásplatformot, és folyamatosan egyezteti a IoT Hub által megadott célállapotot a peremhálózati fürt állapotával. Az alkalmazásmodell továbbra is az ismert modell a IoT Edge és útvonalak alapján. A IoT Edge Agent vezérlő automatikus  fordítást IoT Edge a Kubernetes natív szerkezetéhez, például podok, üzemelő példányok, szolgáltatások stb. alkalmazásmodellhez.

Az alábbi magas szintű architektúradiagram:

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

A peremhálózati üzemelő példány minden összetevője egy, az eszközre jellemző Kubernetes-névtérre terjed ki, így ugyanazon fürterőforrások több peremeszköz és azok üzemelő példányai között is megosztható.

>[!NOTE]
>IoT Edge Kubernetesen elérhető előzetes [verzió.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Oktatóanyagok és referenciák 

További információkért IoT Edge részletes oktatóanyagokat és referenciákat a [Kubernetes](https://aka.ms/edgek8sdoc) előzetes verziójú docs minioldalán található dokumentumban található.
