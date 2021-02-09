---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: b06b91e972fd07543cf02105360cb0400ef6b0f1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831543"
---
A számítási feladatokkal kapcsolatos hibák elhárításához használja a IoT Edge ügynök futásidejű válaszait. Itt látható a lehetséges válaszok listája:

* 200 – OK
* 400 – a telepítési konfiguráció helytelen formátumú vagy érvénytelen.
* 417 – az eszköz nem rendelkezik telepítési konfigurációs készlettel.
* 412 – a séma verziója érvénytelen a telepítési konfigurációban.
* 406 – a IoT Edge eszköz offline állapotban van, vagy nem küld állapotjelentéseket.
* 500 – hiba történt a IoT Edge futtatókörnyezetben.

További információ: [IoT Edge Agent](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent).

A következő hiba a Azure Stack Edge Pro IoT Edge szolgáltatásával kapcsolatos.<!--/ Data Box Gateway--> irányítópultokkal.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>A számítási modulok ismeretlen állapottal rendelkeznek, és nem használhatók

#### <a name="error-description"></a>Hibaleírás

Az eszközön lévő összes modul ismeretlen állapotot mutat, és nem használható. Az ismeretlen állapot egy újraindítás után szűnik meg.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Javasolt megoldás

Törölje a IoT Edge szolgáltatást, majd telepítse újra a modul (oka) t. További információ: [Remove IoT Edge Service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service).
