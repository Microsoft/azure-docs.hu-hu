---
title: 'Gyors útmutató: Rendszerkövetelmények'
description: Ebben a rövid útmutatóban az Azure Defender IoT való futtatásához szükséges rendszerkövetelményeket kapja meg.
ms.date: 11/30/2020
ms.topic: quickstart
ms.openlocfilehash: 2aae849b6ee772c2aa29c680f3b107af3ed600b0
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382946"
---
# <a name="quickstart-system-prerequisites"></a>Gyors útmutató: Rendszerkövetelmények

Ez a cikk az Azure Defender IoT való futtatásának rendszerkövetelményeit sorolja fel.

## <a name="prerequisites"></a>Előfeltételek

- Nincsenek

## <a name="minimum-requirements"></a>Minimális követelmények

- A forgalom figyelését támogató hálózati kapcsolók a SPAN porton keresztül.
- Hardvereszközök a NTA-érzékelőkhöz.
- Az Azure-előfizetés közreműködői szerepköre. Csak a véglegesített eszközök definiálásához és az Azure Sentinelhez való csatlakozáshoz szükséges.
- Azure IoT Hub (ingyenes vagy standard szintű) **közreműködő** szerepkör, felhőalapú felügyelethez. Győződjön meg arról, hogy az **Azure Defender for IoT** funkció engedélyezve van.
- Az eszköz szintű Defender-IoT-Micro-Agent támogatáshoz a Defender for IoT-ügynökök támogatják az eszközök és platformok egyre bővülő listáját. Tekintse meg a [támogatott platformok listáját](how-to-deploy-agent.md).

## <a name="supported-service-regions"></a>Támogatott szolgáltatási régiók

A IoT Defender az összes európai régióból érkező forgalmat a Nyugat-európai regionális adatközpontba irányítja. Az összes többi régiótól az USA középső régiójában lévő adatközpontba irányítja át a forgalmat.

További információ: [IoT hub támogatott régiók](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A szükséges berendezések azonosítása](how-to-identify-required-appliances.md)
