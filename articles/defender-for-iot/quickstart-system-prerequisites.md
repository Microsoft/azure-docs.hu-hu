---
title: Rendszer-előfeltételek
description: Szerezze be a IoT Azure Defender futtatásához szükséges rendszerkövetelményeket.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/30/2020
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 4b5db049e6d1cfe76bdd0d5cd6d7360e0b98bad0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489811"
---
# <a name="system-prerequisites"></a>Rendszer-előfeltételek
Ez a cikk az Azure Defender IoT való futtatásának rendszerkövetelményeit sorolja fel.

## <a name="minimum-requirements"></a>Minimális követelmények

- A forgalom figyelését támogató hálózati kapcsolók a SPAN porton keresztül.
- Hardvereszközök a NTA-érzékelőkhöz.
- Az Azure-előfizetés közreműködői szerepköre. Csak a véglegesített eszközök definiálásához és az Azure Sentinelhez való csatlakozáshoz szükséges.
- Azure IoT Hub (ingyenes vagy standard szintű) **közreműködő** szerepkör, felhőalapú felügyelethez. Győződjön meg arról, hogy az **Azure Defender for IoT** funkció engedélyezve van.
- Az eszköz szintű Defender-IoT-Micro-Agent támogatáshoz a Defender for IoT-ügynökök támogatják az eszközök és platformok egyre bővülő listáját. Tekintse meg a [támogatott platformok listáját](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók

A IoT Defender az összes európai régióból érkező forgalmat a Nyugat-európai regionális adatközpontba irányítja. Az összes többi régiótól az USA középső régiójában lévő adatközpontba irányítja át a forgalmat.

További információ: [IoT hub támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="see-also"></a>Lásd még

- [A szükséges berendezések azonosítása](how-to-identify-required-appliances.md)
- [Tudnivalók az Azure Defender for IoT Network telepítőről](how-to-set-up-your-network.md)
