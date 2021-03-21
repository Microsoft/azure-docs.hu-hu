---
title: Az Azure Marketplace-en IoT Edge modul-ajánlatok előzetes tanúsítási ellenőrzőlistája
description: Ismerje meg az Azure Marketplace-en IoT Edge modul-ajánlatok közzétételének konkrét minősítési követelményeit.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: c1d4c9be1f76e62be3c17d4dec22479db003b77a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608257"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>A IoT Edge-modulok előzetes tanúsítási ellenőrzőlistája

> [!NOTE]
> Javasoljuk, hogy a minősítések elküldése előtt tekintse át a feladatlistát, és ellenőrizze a modul funkcióit. Ezzel felgyorsítja a minősítési folyamatot, ha csökkenti a módosítások és az ismételt küldés szükségességét.

## <a name="validation-of-image"></a>Rendszerkép ellenőrzése

Ha az Edge-modul képe készen áll a küldésre, hajtsa végre ezeket a lépéseket annak biztosításához, hogy a rendszerkép a Microsoft által várt módon működjön.

### <a name="steps-to-perform-in-the-azure-portal"></a>A Azure Portal végrehajtandó lépései

1. Nyissa meg az [Azure Portalt](https://partner.microsoft.com/).
1. Hozzon létre egy erőforráscsoportot.
1. IoT Hub létrehozása
1. Hozzon létre egy IoT Edge eszközt.
1. Másolja a kapcsolatok karakterláncát, és mentse a Jegyzettömbben.
1. Válassza ki az **Edge-eszközön létrehozott set-modulokat**.
1. Adja meg az ACR részleteit, ahol a lemezkép legújabb verziója található.
1. Válassza a **IoT Edge modul hozzáadása** lehetőséget, és adja meg a következőket:
    - A rendszerkép URI-ja a modul beállításaiban
    - A környezeti változó (megegyezik a partner Centerben hozzáadott értékekkel)
    - A tároló létrehozási beállításai (ugyanaz, mint amit a partner Centerben hozzáadott)
    - A modul Twin beállítása (ugyanaz, mint amit a partner Centerben hozzáadott)
1. Adja hozzá az útvonalakat (ez ugyanaz, mint amit a partner Centerben hozzáadott).
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

Az Edge-modulok üzembe helyezése az Azure-on létrehozott peremhálózati eszközön történik.

### <a name="steps-to-perform-on-the-device"></a>Az eszközön végrehajtandó lépések

#### <a name="device-details"></a>Eszközadatok

A minősítési csapat a következő hardvert használja a rendszerképek különböző architektúrán való ellenőrzéséhez:

- X64-lemezképek esetén egy olyan Azure-beli virtuális gép, amely az Ubuntu Server 18.04/Ubuntu Server 16,04-es verziójának megfelelő konfiguráció-mérettel rendelkezik.
- Azure Resource Manager 32-es rendszerképekhez a "B" málna 3. modell.
- Azure Resource Manager 64 rendszerképekhez egy NVIDIA Jetson Nano 4Gb.

#### <a name="steps"></a>Lépések

1. Győződjön meg arról, hogy az eszközök/virtuális gép létre van hozva a PuTTY használatával.
1. Töltse le [IoT Edge futtatókörnyezetet](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge) az eszközre.
1. Frissítse az 5. lépésben a config. YAML fájlba másolt kapcsolódási karakterláncot.
1. Indítsa újra az Edge-modult a rel `sudo systemctl restart iotedge` .
1. Ellenőrizze, hogy a modul telepítve van-e az eszközön a szolgáltatással, ezért `sudo iotedge list` futó állapotban kell lennie.
1. Győződjön meg arról, hogy a telepített modul naplói `sudo iotedge logs “Module Name“ -f` nem rendelkeznek hibával. Ha ismert hibák vannak, írja le ezt a partner Center- **megjegyzésekben a felülvizsgáló számára** az ajánlat elküldése előtt.

## <a name="metadata-validation"></a>Metaadatok ellenőrzése

Ellenőrizze a következőket:

- A legújabb címke a partner Centerben és a Azure Container Registryban is szerepel.
- A minimális hardverkövetelmények hozzáadódnak az ajánlat leírásához.
- Az Azure Container Registry felhasználóneve és jelszava frissül, és hozzá lesz adva a partner Centerben.
- A kívánt **Twin tulajdonság** pontossága, *Ha van ilyen*.
- A kívánt **környezeti változók** pontossága, *Ha van ilyen*.
- A kívánt **létrehozási beállítások** pontossága, *Ha van ilyen*.
- Az érdeklődői felügyeleti kapcsolatok karakterlánca megtalálható.
- Adatvédelmi szabályzat jelen van
- Használati feltételek jelen
- Támogatott IoT Edge eszköz hivatkozásának hozzáadása az [Azure IoT-eszköz katalógusból](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>Következő lépések

- [Modulok üzembe helyezése a kereskedelmi piactéren](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal#deploy-from-azure-marketplace)
- [Az Edge-modul közzététele a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)
- [IoT Edge modul üzembe helyezése](https://docs.microsoft.com/azure/iot-edge/quickstart-linux)  
