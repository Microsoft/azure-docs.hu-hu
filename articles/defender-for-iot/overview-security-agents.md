---
title: Biztonsági ügynökök
description: Ismerkedjen meg az Azure Defender IoT biztonsági szolgáltatási ügynökökkel való megismerésével, konfigurálásával, üzembe helyezésével és használatával a IoT-eszközökön.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/24/2021
ms.author: shhazam
ms.openlocfilehash: a40b64dd3b8f898c961863c1d78a2a56642c44b9
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521611"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Ismerkedés az Azure Defender IoT-eszköz Micro-ügynökökkel való használatába

A IoT biztonsági ügynökei fokozott biztonsági képességeket biztosítanak, mint például az operációs rendszer konfigurációjának figyelése ajánlott eljárások. A fenyegetések elleni védelem és a biztonsági helyzet szabályozása egyetlen szolgáltatással.

A IoT biztonsági ügynökök védelmezői kezelik a nyers események gyűjtését az eszköz operációs rendszeréről, az események összesítését a költségeket és a konfigurációt egy külön eszközosztály használatával. A biztonsági üzeneteket a IoT Hub a Defender for IoT Analytics Services szolgáltatásban küldik el.

A következő munkafolyamat használatával telepítheti és tesztelheti a Defender for IoT biztonsági ügynökeit:

1. [Engedélyezze a Defender for IoT szolgáltatást a IoT hub](quickstart-onboard-iot-hub.md).

1. Ha a IoT Hub nem rendelkezik regisztrált eszközökkel, [regisztráljon egy új eszközt](../iot-accelerators/iot-accelerators-device-simulation-overview.md).

1. [Hozzon létre egy DefenderIotMicroAgent-modult](quickstart-create-micro-agent-module-twin.md) az eszközökhöz.

1. Ha az ügynököt egy Azure-beli szimulált eszközre szeretné telepíteni ahelyett, hogy tényleges eszközre telepítené, egy [új Azure-beli virtuális gépet (VM)](../virtual-machines/linux/quick-create-portal.md) helyezzen üzembe egy elérhető zónában.

1. [Helyezzen üzembe egy Defender for IoT biztonsági ügynököt](how-to-deploy-linux-cs.md) a IoT-eszközön vagy az új virtuális gépen.

1. Az operációs rendszer alapkonfigurációja eseményének futtatásához kövesse az [trigger_events](https://aka.ms/iot-security-github-trigger-events) utasításait.

1. Az előző lépésben a szimulált operációsrendszer-alapkonfiguráció-ellenőrzési hibára adott válaszként ellenőrizze a Defender IoT kapcsolatos javaslatait. A szkript futtatása után 30 perccel az ellenőrzés megkezdése.

## <a name="next-steps"></a>Következő lépések

A [megoldás](quickstart-configure-your-solution.md)beállítása 
 [biztonsági modulok](quickstart-create-security-twin.md) konfigurálása [egyéni riasztások](quickstart-create-custom-alerts.md)beállítása 
 [biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)
