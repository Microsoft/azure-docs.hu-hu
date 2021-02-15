---
title: Rendszer-előfeltételek
description: Szerezze be a IoT Azure Defender futtatásához szükséges rendszerkövetelményeket.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 7b609fec2a47dc0685b30dac12f43263127f70ef
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523209"
---
# <a name="system-prerequisites"></a>Rendszer-előfeltételek
Ez a cikk az Azure Defender IoT való futtatásának rendszerkövetelményeit sorolja fel.

## <a name="minimum-requirements"></a>Minimális követelmények

- A forgalom figyelését támogató hálózati kapcsolók a SPAN porton keresztül.
- Hardvereszközök a NTA-érzékelőkhöz.
- Az Azure-előfizetés közreműködői szerepköre. Csak a véglegesített eszközök definiálásához és az Azure Sentinelhez való csatlakozáshoz szükséges.
- Azure IoT Hub (ingyenes vagy standard szintű) **közreműködő** szerepkör, felhőalapú felügyelethez. Győződjön meg arról, hogy az **Azure Defender for IoT** funkció engedélyezve van.
- Az eszköz szintű biztonsági modul támogatásához a Defender for IoT-ügynökök támogatják az eszközök és platformok egyre bővülő listáját. Tekintse meg a [támogatott platformok listáját](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók

A IoT Defender az összes európai régióból érkező forgalmat a Nyugat-európai regionális adatközpontba irányítja. Az összes többi régiótól az USA középső régiójában lévő adatközpontba irányítja át a forgalmat.

További információ: [IoT hub támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Következő lépések

[Szükséges berendezések azonosítása](how-to-identify-required-appliances.md) 
 [Tudnivalók az Azure Defender for IoT Network telepítőről](how-to-set-up-your-network.md)
