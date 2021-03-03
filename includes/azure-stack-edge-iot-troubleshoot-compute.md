---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750664"
---
A számítási feladatokkal kapcsolatos hibák elhárításához használja a IoT Edge ügynök futásidejű válaszait. Itt látható a lehetséges válaszok listája:

* 200 – OK
* 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
* 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
* 412 – a séma verziója érvénytelen a telepítési konfigurációban.
* 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
* 500 – hiba történt a IoT Edge futtatókörnyezetben.

További információ: [IoT Edge Agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

A következő hiba a Azure Stack Edge Pro IoT Edge szolgáltatásával kapcsolatos.<!--/ Data Box Gateway--> irányítópultokkal.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>A számítási modulok ismeretlen állapottal rendelkeznek, és nem használhatók

#### <a name="error-description"></a>Hibaleírás

Az eszközön lévő összes modul ismeretlen állapotot mutat, és nem használható. Az ismeretlen állapot egy újraindítás után szűnik meg.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Javasolt megoldás

Törölje a IoT Edge szolgáltatást, majd telepítse újra a modul (oka) t. További információ: [Remove IoT Edge Service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).