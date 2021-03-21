---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 06/30/2020
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 6b9ec2017ffa5d4e4148b441aa23ed2eca6ee8b8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99628956"
---
Azure IoT Edge egyik fő funkciója, hogy a felhőből üzembe helyezi a IoT Edge-eszközökön a kódot. *IoT Edge modulok* tárolóként megvalósított végrehajtható csomagok. Ebben a szakaszban egy előre összeállított modult helyezünk üzembe az [Azure Marketplace IoT Edge-modulok szakaszával](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) közvetlenül az Azure IoT hub-ból.

Az ebben a szakaszban üzembe helyezett modul szimulál egy érzékelőt, és a generált adatokat küldi el. Ez a modul hasznos kódrészlet, ha első lépések a IoT Edgehoz, mivel a szimulált adat a fejlesztéshez és a teszteléshez használható. Ha pontosan látni szeretné a modult, akkor megtekintheti a [szimulált hőmérséklet-érzékelő forráskódját](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Kövesse az alábbi lépéseket az első modul üzembe helyezéséhez az Azure Marketplace-en.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és nyissa meg az IoT hubot.

1. A bal oldali menüben az **automatikus eszközkezelés** területen válassza a **IoT Edge** lehetőséget.

1. Válassza ki a céleszköz eszközének AZONOSÍTÓját az eszközök listájából.

1. A felső sávon válassza a **modulok beállítása** lehetőséget.

   ![Képernyőkép, amely a set modulok kiválasztása lehetőséget mutatja.](./media/iot-edge-deploy-module/select-set-modules.png)

1. A **IoT Edge modulok** területen nyissa meg a **Hozzáadás** legördülő menüt, majd válassza a **piactér modul** elemet.

   ![A Hozzáadás legördülő menüt megjelenítő képernyőkép.](./media/iot-edge-deploy-module/add-marketplace-module.png)

1. A **IoT Edge modul piactéren** keresse meg és válassza ki a `Simulated Temperature Sensor` modult.

   A modul hozzá lesz adva a IoT Edge modulok szakaszhoz a kívánt **futtatási** állapottal.

1. Válassza a **tovább lehetőséget: útvonalakat** a varázsló következő lépéséhez.

   ![Képernyőkép, amely a modul hozzáadása után a következő lépés folytatását mutatja.](./media/iot-edge-deploy-module/view-temperature-sensor-next-routes.png)

1. Az **útvonalak** lapon távolítsa el az alapértelmezett útvonalat, az **útvonalat**, majd válassza a **Tovább: felülvizsgálat + létrehozás** lehetőséget a varázsló következő lépésének folytatásához.

   >[!Note]
   >Az útvonalak név és érték párok használatával jönnek létre. Ezen a lapon két útvonalnak kell megjelennie. Az alapértelmezett útvonal, az **útvonal**, az összes üzenet küldése a IoT hubnak (amely neve `$upstream` ). A második útvonal, a **SimulatedTemperatureSensorToIoTHub**, automatikusan jött létre, amikor hozzáadta a modult az Azure Marketplace-ről. Ez az útvonal a szimulált hőmérséklet modul összes üzenetét IoT Hubba küldi. Az alapértelmezett útvonalat törölheti, mert ebben az esetben redundáns lehet.

   ![Képernyőfelvétel: az alapértelmezett útvonal eltávolítása, majd a következő lépésre való áttérés.](./media/iot-edge-deploy-module/delete-route-next-review-create.png)

1. Tekintse át a JSON-fájlt, majd kattintson a **Létrehozás** gombra. A JSON-fájl a IoT Edge eszközre telepített összes modult definiálja. Ekkor megjelenik a **SimulatedTemperatureSensor** modul és a két futásidejű modul, a **EdgeAgent** és a **edgeHub**.

   >[!Note]
   >Amikor egy új üzemelő példányt küld egy IoT Edge-eszközre, semmi sem kerül az eszközre. Ehelyett az eszköz rendszeresen lekérdezi az IoT Hubról az új utasításokat. Ha az eszköz egy frissítettüzembe helyezési jegyzéket talál, felhasználja az új üzemelő példányra vonatkozó információt a modul lemezképeinek felhőből történő adatkiszolgálásához, majd elindítja a modulok helyi futtatását. Ez a folyamat eltarthat néhány percig.

1. Miután létrehozta a modul központi telepítésének részleteit, a varázsló visszaadja az eszköz adatai lapot. Tekintse meg a telepítés állapotát a **modulok** lapon.

   Három modulnak kell megjelennie: **$edgeAgent**, **$edgeHub** és **SimulatedTemperatureSensor**. Ha egy vagy több modul **Igen** értékkel rendelkezik **az üzembe helyezés során** , de nem az **eszköz által jelentett**, akkor a IoT Edge eszköz továbbra is elindul. Várjon néhány percet, majd frissítse az oldalt.

   ![A telepített modulok listájában a szimulált hőmérséklet-érzékelőt bemutató képernyőkép.](./media/iot-edge-deploy-module/view-deployed-modules.png)
