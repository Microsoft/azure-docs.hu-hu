---
title: A IoT Edge telepítése Kubernetes-| Microsoft Docs
description: Megtudhatja, hogyan telepíthet IoT Edge Kubernetesre helyi fejlesztési fürtkörnyezet használatával
author: kgremban
manager: philmea
ms.author: veyalla
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: iotedge-2018-06
ms.openlocfilehash: 1c7c221a2fea60f3bbbc4f2cde960dcb8638efe2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576567"
---
# <a name="how-to-install-iot-edge-on-kubernetes-preview"></a>Az alkalmazás telepítése IoT Edge Kubernetesen (előzetes verzió)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge kubernetes-sel integrálható, és rugalmas, magas rendelkezésre áll rendelkezésre álló infrastruktúrarétegként használhatja. Ez a támogatás itt illeszkedik egy magas szintű IoT Edge megoldásba:

![k8s bevezetés](./media/how-to-install-iot-edge-kubernetes/kubernetes-model.png)

>[!TIP]
>Az integrációhoz jó szellemi modell a Kubernetes, mint egy másik üzemeltetési környezet, IoT Edge linuxos és windowsos alkalmazások is futtathatók.

## <a name="architecture"></a>Architektúra 
A Kubernetesben a IoT Edge *biztosít* egyéni erőforrás-definíciót (CRD) a peremhálózati számítási feladatok üzembe helyezéséhez. IoT Edge Ügynök egy  *CRD-vezérlő* szerepét feltételezi, amely egyezteti a felhő által felügyelt célállapotot a helyi fürt állapotával.

A modul élettartamát a Kubernetes-ütemező kezeli, amely fenntartja a modulok rendelkezésre állását, és kiválasztja azok elhelyezését. IoT Edge felügyeli a felül futó peremhálózati alkalmazásplatformot, és folyamatosan egyezteti a IoT Hub alatt megadott célállapotot a peremhálózati fürt állapotával. Az alkalmazásmodell továbbra is az ismert modell, amely a IoT Edge és útvonalakon alapul. A IoT Edge Agent vezérlő automatikusan  lefordítja IoT Edge Kubernetes natív szerkezetére, például podra, üzemelő példányra, szolgáltatásra stb.

Az alábbi magas szintű architektúradiagram:

![kubernetes arch](./media/how-to-install-iot-edge-kubernetes/publicpreview-refresh-kubernetes.png)

A peremhálózati üzemelő példány minden összetevője egy, az eszközre jellemző Kubernetes-névtérre terjed ki, így lehetővé teszi ugyanazon fürterőforrások megosztását több peremeszköz és azok üzemelő példányai között.

>[!NOTE]
>IoT Edge Kubernetesen elérhető előzetes [verzióban érhető el.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="tutorials-and-references"></a>Oktatóanyagok és referenciák 

További információkért IoT Edge részletes oktatóanyagokat és referenciákat a [Kubernetes](https://aka.ms/edgek8sdoc) előzetes verziójú dokumentum minioldalán található.
