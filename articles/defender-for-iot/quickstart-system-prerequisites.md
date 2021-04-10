---
title: Rendszer-előfeltételek
description: Szerezze be a IoT Azure Defender futtatásához szükséges rendszerkövetelményeket.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 78d5948554ae531c4b2f77d67bb916d5290db943
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780939"
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
