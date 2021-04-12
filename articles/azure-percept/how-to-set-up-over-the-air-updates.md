---
title: Azure-IoT Hub beállítása a légi frissítések üzembe helyezéséhez
description: Ismerje meg, hogyan konfigurálhatja az Azure IoT Hubt a frissítések Azure Percept DK-ra való központi telepítéséhez
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064120"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Azure-IoT Hub beállítása az Azure Percept DK-re való üzembe helyezéshez

Az Azure Percept DK-t biztonságban és naprakészen tarthatja a over-the-Air frissítések használatával. Néhány egyszerű lépésben beállíthatja az Azure-környezetet a IoT Hub eszköz frissítésével, és üzembe helyezheti az Azure Percept DK legújabb frissítéseit.

## <a name="prerequisites"></a>Előfeltételek

- Azure Percept DK (fejlesztői készlet)
- [Azure-előfizetés](https://azure.microsoft.com/free/)
- Az [Azure PERCEPT DK telepítési felülete](./quickstart-percept-dk-set-up.md): összekapcsolta a fejlesztői csomagot egy Wi-Fi hálózattal, létrehozott egy IoT hub, és csatlakoztatta a fejlesztői készletet a IoT hub

## <a name="create-a-device-update-account"></a>Eszköz-frissítési fiók létrehozása

1. Lépjen a [Azure Portal](https://portal.azure.com) , és jelentkezzen be az Azure Percept használt Azure-fiókkal.

1. A lap tetején található keresősáv alatt adja meg a **IoT hub eszköz frissítését**.

1. Válassza ki az **eszköz frissítését IoT hub** amikor megjelenik a keresősávban.

1. Kattintson a lap bal felső részén található **+ Hozzáadás** gombra.

1. Válassza ki az Azure Percept-eszközhöz társított **Azure-előfizetést** és **erőforráscsoportot** , valamint annak IoT hub.

1. Adja meg az eszköz frissítési fiókjának **nevét** és **helyét** .

1. Tekintse át a részleteket, és válassza a **felülvizsgálat + létrehozás** elemet.

1. Az üzembe helyezés befejezése után kattintson **az erőforrás keresése** elemre.

## <a name="create-a-device-update-instance"></a>Eszköz frissítési példányának létrehozása

1. IoT Hub erőforráshoz tartozó frissítésnél kattintson a példányok **kezelése** **elemre** .

1. Kattintson a **+ Létrehozás** gombra, adja meg a példány nevét, és válassza ki az Azure Percept-eszközhöz társított IoT hub. Ez eltarthat néhány percig.

1. Kattintson a **Létrehozás** lehetőségre.

## <a name="configure-iot-hub"></a>IoT Hub konfigurálása

1. A példányok felügyeleti **példányai** lapon várja meg, hogy az eszköz frissítési példánya **sikeres** állapotba lépjen. Az állapot frissítéséhez kattintson a **frissítés** ikonra.

1. Válassza ki az Ön számára létrehozott példányt, és kattintson a **IoT hub konfigurálása** elemre. A bal oldali panelen válassza az **Elfogadom lehetőséget a módosítások végrehajtásához** , majd kattintson a **frissítés** gombra.

1. Várjon, amíg a folyamat sikeresen befejeződik.

## <a name="configure-access-control-roles"></a>Hozzáférés-vezérlési szerepkörök konfigurálása

Az utolsó lépés lehetővé teszi a felhasználók számára a frissítések közzétételére és telepítésére vonatkozó engedélyek megadását.

1. Az IoT Hub erőforrás frissítésében kattintson a **hozzáférés-vezérlés (iam)** elemre.

1. Kattintson a **+ Hozzáadás** , majd a **szerepkör-hozzárendelés hozzáadása** elemre.

1. A **szerepkör** lapon válassza az **eszköz frissítésének rendszergazdája** elemet. A **hozzáférés hozzárendeléséhez** válassza a **felhasználó, csoport vagy szolgáltatás elv** lehetőséget. A **Select (kiválasztás**) lehetőségnél válassza ki a fiókját vagy annak a személynek a fiókját, aki telepíteni kívánja a frissítéseket. Kattintson a **Mentés** gombra.

> [!TIP]
> Ha több személyt szeretne biztosítani a szervezet számára, megismételheti ezt a lépést, és elvégezheti az egyes felhasználók számára az **eszköz frissítési rendszergazdáját**.

## <a name="next-steps"></a>Következő lépések

Most már készen áll arra, hogy [frissítse az Azure Percept dev Kit-t a](./how-to-update-over-the-air.md) IoT hub eszköz frissítésével.