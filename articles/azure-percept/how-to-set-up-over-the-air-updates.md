---
title: Azure-IoT Hub beállítása a légi frissítések üzembe helyezéséhez
description: Ismerje meg, hogyan konfigurálhatja az Azure IoT Hubt frissítések központi telepítéséhez az Azure Percept DK-be
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662323"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Azure-IoT Hub beállítása az Azure Percept DK-re való üzembe helyezéshez
Az Azure Percept DK-t biztonságban és naprakészen tarthatja a over-the-Air frissítések használatával. Néhány egyszerű lépésben beállíthatja az Azure-környezetet a IoT Hub eszköz frissítésével, és üzembe helyezheti az Azure Percept DK legújabb frissítéseit.

## <a name="create-a-device-update-account"></a>Eszköz-frissítési fiók létrehozása

1. Lépjen a [Azure Portalra](https://portal.azure.com) , és jelentkezzen be az Azure Percept használt Azure-fiókkal 

1. A lap tetején található Keresés ablakban kezdje el beírni az "eszköz frissítése IoT Hub" kifejezést.

1. Válassza ki az **IoT hub-eszközök frissítését** , ahogy az a keresési ablakban jelenik meg.

1. Kattintson a **+ Hozzáadás** gombra a lap bal felső részén.

1. Válassza ki az Azure Percept-eszközhöz társított Azure-előfizetést és erőforráscsoportot (ez az a hely, ahol a bevezetési IoT Hub található).

1. Adja meg az eszköz frissítési fiókjának nevét és helyét

1. Tekintse át a részleteket, majd válassza a **felülvizsgálat + létrehozás** elemet.
 
1. Az üzembe helyezés befejezése után kattintson **az erőforrás keresése** elemre.
 
## <a name="create-a-device-update-instance"></a>Eszköz frissítési példányának létrehozása
Most hozzon létre egy példányt az eszköz frissítésén belül IoT Hub fiókhoz.

1. IoT Hub erőforráshoz tartozó frissítésnél kattintson a példányok **kezelése** **elemre** .
 
1. Kattintson a **+ Létrehozás** gombra, adja meg a példány nevét, és válassza ki az Azure Percept-eszközhöz társított IoT hub (azaz a bevezetési élmény során). Ez eltarthat néhány percig.
 
1. Kattintson a **Létrehozás** gombra

## <a name="configure-iot-hub"></a>IoT Hub konfigurálása

1. A példányok felügyeleti **példányai** lapon várja meg, hogy az eszköz frissítési példánya **sikeres** állapotba lépjen. Az állapot frissítéséhez kattintson a **delete** ( **frissítés** ) ikonra.
 
1. Válassza ki az Ön számára létrehozott példányt, majd kattintson a **IoT hub konfigurálása** elemre. A bal oldali panelen válassza az **Elfogadom lehetőséget a módosítások végrehajtásához** , majd kattintson a **frissítés** gombra.
 
1. Várjon, amíg a folyamat sikeresen befejeződik.
 
## <a name="configure-access-control-roles"></a>Hozzáférés-vezérlési szerepkörök konfigurálása
Az utolsó lépés lehetővé teszi a felhasználók számára a frissítések közzétételére és telepítésére vonatkozó engedélyek megadását.

1. Az IoT Hub erőforrás frissítésében kattintson a **hozzáférés-vezérlés (iam)** elemre.
 
2. Kattintson a **+ Hozzáadás** , majd a **szerepkör-hozzárendelés hozzáadása** elemre.
 
3. A **szerepkör** lapon válassza az **eszköz frissítésének rendszergazdája** elemet. A **hozzáférés hozzárendeléséhez** válassza a **felhasználó, csoport vagy szolgáltatás elv** lehetőséget. A **válassza** ki a fiókját vagy a frissítéseket telepítő személy fiókját. Ezután kattintson a **Mentés** gombra. 

    > [!TIP]
    > Ha több személyt szeretne biztosítani a szervezet számára, megismételheti ezt a lépést, és elvégezheti az egyes felhasználók számára az **eszköz frissítési rendszergazdáját**.

## <a name="next-steps"></a>Következő lépések

Most már be van állítva, és [frissítheti az Azure Percept dev Kit-t a](./how-to-update-over-the-air.md) IoT hub eszköz frissítése révén. Navigáljon ahhoz az Azure-IoT Hubhoz, amelyet az Azure Percept-eszközhöz használ.