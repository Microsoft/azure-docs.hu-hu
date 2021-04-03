---
title: Oktatóanyag – eszközök kiépítése elosztott terhelésű hubok között az Azure IoT Hub Device Provisioning Service
description: Ez az oktatóanyag azt mutatja be, hogy a Device kiépítési szolgáltatás (DPS) hogyan teszi lehetővé az eszközök automatikus kiépítés a terheléselosztásos IoT-hubok között az Azure Portalon
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6cca34774038156cfb01e872e751338c1c96efaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91316000"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>Oktatóanyag: eszközök kiépítése elosztott terhelésű IoT hubok között

Ebből az oktatóanyagból megtudhatja, hogyan regisztrálhat eszközöket több elosztott terhelésű IoT hubhoz a Device Provisioning Service használatával. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Második eszköz regisztrálása egy második IoT-központhoz az Azure Portal használatával 
> * Regisztrációs listabejegyzés hozzáadása a második eszközhöz
> * A Device Provisioning Service kiosztási szabályzatának beállítása **egyenletes eloszláshoz**
> * Az új IoT hub csatolása a Device Provisioning Service-hez

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag az előző, [Eszköz regisztrálása központhoz](tutorial-provision-device-to-hub.md) című oktatóanyagra épít.

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Második eszköz regisztrálása egy második IoT-központhoz az Azure Portal használatával

Egy második eszköz második IoT-központhoz való regisztrációjához kövesse az [Eszköz regisztrálása központhoz](tutorial-provision-device-to-hub.md) című oktatóanyag lépéseit.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Regisztrációs listabejegyzés hozzáadása a második eszközhöz

A regisztrációs lista megmondja a Device Provisioning Service-nek, hogy melyik igazolási metódust (az eszközidentitás megerősítésének metódusa) használja az eszközhöz. A következő lépés a regisztrációs listabejegyzés hozzáadása a második eszközhöz. 

1. A Device Provisioning Service lapján kattintson a **Regisztrációk kezelése** elemre. Megjelenik a **Regisztrációs listabejegyzés hozzáadása** lap. 
2. Kattintson a **Hozzáadás** gombra a lap tetején.
2. Töltse ki a mezőket, majd kattintson a **Mentés** gombra.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>A Device Provisioning Service kiosztási szabályzatának beállítása

A kiosztási szabályzat egy Device Provisioning Service-beállítás, amely meghatározza az eszközök IoT hubokhoz való hozzárendelésének módját. Három támogatott kiosztási szabályzat létezik: 

1. **Legkisebb mértékű késleltetés**: Az eszközök abban az IoT-központban lesznek kiépítve, amely a legkisebb mértékű késleltetéssel rendelkezik az adott eszköz irányába.
2. **Egyenletesen súlyozott elosztás** (alapértelmezett): Az eszközök egyforma valószínűséggel lesznek kiépítve a csatolt IoT-központokban. Ez az alapértelmezett beállítás. Akkor érdemes megtartani, ha csak egy IoT-központban épít ki eszközöket. 
3. **Statikus konfigurálás a regisztrációs listán keresztül**: A regisztrációs listában meghatározott IoT-központ elsőbbséget élvez a Device Provisioning Service-szintű kiosztási szabályzattal szemben.

Kövesse az alábbi lépéseket a kiosztási szabályzat beállításához:

1. A kiosztási szabályzat beállításához kattintson a **Kiosztási szabályzat kezelése** lehetőségre a Device Provisioning Service lapján.
2. Állítsa a kiosztási szabályzatot **Egyenletesen súlyozott elosztás** értékre.
3. Kattintson a **Mentés** gombra.

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>Az új IoT hub csatolása a Device Provisioning Service-hez

Kapcsolja össze a Device Provisioning Service-t és az IoT hubot, hogy a Device Provisioning Service regisztrálni tudja az eszközöket a hubhoz.

1. Az **Összes erőforrás** lapon kattintson a korábban létrehozott Device Provisioning Service szolgáltatásra.
2. A Device Provisioning Service lapján kattintson a **Csatolt IoT-központok** lehetőségre.
3. Kattintson a **Hozzáadás** parancsra.
4. A **Hivatkozás hozzáadása IoT-központhoz** lapon a választógombok segítségével adja meg, hogy a csatolt IoT-központ az aktuális előfizetésben vagy egy másik előfizetésben található. Ezután válassza ki az IoT-központ nevét az **IoT-központ** mezőben.
5. Kattintson a **Mentés** gombra.

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Második eszköz regisztrálása egy második IoT-központhoz az Azure Portal használatával 
> * Regisztrációs listabejegyzés hozzáadása a második eszközhöz
> * A Device Provisioning Service kiosztási szabályzatának beállítása **egyenletes eloszláshoz**
> * Az új IoT hub csatolása a Device Provisioning Service-hez

## <a name="next-steps"></a>Következő lépések

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
