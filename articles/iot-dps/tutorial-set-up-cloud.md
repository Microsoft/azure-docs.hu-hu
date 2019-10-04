---
title: Felhő beállítása az Azure IoT Hub Device Provisioning Service-hez a Portalon | Microsoft Docs
description: IoT Hub automatikus eszközkiépítés az Azure Portalon
author: wesmc7777
ms.author: wesmc
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 8f06d3f033a2bf5907dc2ee324359bef0eb247d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60953526"
---
# <a name="configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Felhőalapú erőforrások konfigurálása az IoT Hub Device Provisioning Service használatával történő eszközkiépítéshez

Ez az oktatóanyag bemutatja, hogyan állíthatja be a felhőt az automatikus eszközkiépítéshez az IoT Hub Device Provisioning Service használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * IoT Hub Device Provisioning Service létrehozása és az azonosító hatókörének lekérése az Azure Portallal
> * IoT Hub létrehozása
> * Az IoT Hub csatolása a Device Provisioning Service-hez
> * A Device Provisioning Service kiosztási szabályzatának beállítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Device Provisioning Service-példány létrehozása és az azonosító hatókörének lekérése

Kövesse az alábbi lépéseket egy új Device Provisioning Service-példány létrehozásához.

1. Kattintson az Azure Portal bal felső sarkában az **Erőforrás létrehozása** gombra.

2. A keresőmezőbe írja be a **device provisioning** kifejezést. 

3. Kattintson az **IoT Hub Device Provisioning Service** elemre.

4. Töltse ki az **IoT Hub Device Provisioning Service** űrlapját a következő információkkal:
    
   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Name (Név)** | Bármely egyedi név | -- | 
   | **Előfizetés** | Az Ön előfizetése  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |   

   ![Írja be a Device Provisioning Service-szolgáltatás alapvető adatait a portálon](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Kattintson a **Create** (Létrehozás) gombra. A rendszer néhány pillanaton belül létrehozza a Device Provisioning Service-példányt, majd megjeleníti az **Áttekintés** lapot.

6. Az új szolgáltatáspéldány **Áttekintés** lapján másolja az **Azonosító hatóköre** értéket későbbi felhasználás céljából. Az érték a regisztrációs azonosítók azonosítására szolgál, továbbá garantálja a regisztrációs azonosító egyediségét.

7. Jegyezze fel a **Szolgáltatásvégpont** értéket is későbbi felhasználás céljából. 

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Létrehozta az IoT Hubot, és rendelkezik az oktatóanyag további részeinek teljesítéséhez szükséges állomásnévvel és IoT Hub kapcsolati sztringgel.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>A Device Provisioning Service csatolása az IoT Hubhoz

A következő lépés a Device Provisioning Service és az IoT-központ összekapcsolása, hogy az IoT Hub Device Provisioning Service regisztrálni tudja az eszközöket a központba. A szolgáltatás csak olyan IoT-központokba tud eszközöket kiépíteni, amelyek össze vannak kapcsolva a Device Provisioning Service-szel. Kövesse az alábbi lépéseket.

1. Az **Összes erőforrás** lapon kattintson a korábban létrehozott Device Provisioning Service-példányra.

2. A Device Provisioning Service lapján kattintson a **Csatolt IoT-központok** lehetőségre.

3. Kattintson a **Hozzáadás** parancsra.

4. A **Hivatkozás hozzáadása IoT hubhoz** lapon adja meg az alábbi adatokat, majd kattintson a **Mentés**gombra:

    * **Előfizetés:** Ellenőrizze, hogy az előfizetés, amely tartalmazza az IoT hub van kiválasztva. Másik előfizetésben található IoT hubot is csatlakoztathat.

    * **Az IoT hub:** Válassza ki az IoT hub csatolása a Device Provisioning Service-példányt a kívánt nevét.

    * **Hozzáférési szabályzat:** Válassza ki **iothubowner** , a hivatkozásra kattintva az IoT hub létrehozásához szükséges használandó hitelesítő adatok.

   ![A központ nevének csatolása a Device Provisioning Service-szolgáltatáshoz a portálon](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>A Device Provisioning Service kiosztási szabályzatának beállítása

A kiosztási szabályzat egy IoT Hub Device Provisioning Service-beállítás határozza meg, hogyan eszközök vannak rendelve egy IoT hubra. Három támogatott kiosztási szabályzat létezik: 

1. **Legkisebb késés**: Eszközök kiosztása egy IoT hubra a legkisebb mértékű késleltetéssel rendelkezik az eszközt a hubon alapján.

2. **Egyenletesen súlyozott elosztás** (alapértelmezett): Összekapcsolt IoT-központok várhatóan egyaránt rendelkezik kiosztott eszközökkel. Ez az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket. 

3. **A regisztrációs listán keresztül statikus konfiguráció**: A regisztrációs listában meghatározott IoT-központ specifikace prioritást élvez a Device Provisioning Service-szintű kiosztási szabályzattal keresztül.

A kiosztási szabályzat beállításához kattintson a **Kiosztási szabályzat kezelése** lehetőségre a Device Provisioning Service lapján. Győződjön meg arról, hogy a kiosztási házirend **Egyenletesen súlyozott elosztás** értékre van állítva (ez az alapértelmezett). A módosítások elvégzése után kattintson a **Mentés** gombra.

![Kiosztási szabályzat kezelése](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi oktatóanyag erre az oktatóanyagra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben az oktatóanyagban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon az oktatóanyaghoz létrehozott összes erőforrást.

1. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hub Device Provisioning Service-példányt. Az **Összes erőforrás** lap tetején kattintson a **Törlés** elemre.  

2. Az Azure Portal bal oldali menüjében kattintson az **Összes erőforrás** lehetőségre, majd válassza ki az IoT Hubot. Az **Összes erőforrás** lap tetején kattintson a **Törlés** elemre.
 
## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * IoT Hub Device Provisioning Service létrehozása és az azonosító hatókörének lekérése az Azure Portallal
> * IoT Hub létrehozása
> * Az IoT Hub csatolása a Device Provisioning Service-hez
> * A Device Provisioning Service kiosztási szabályzatának beállítása

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan állíthatja be az eszközt a kiépítéshez.

> [!div class="nextstepaction"]
> [Eszköz beállítása a kiépítéshez](tutorial-set-up-device.md)
