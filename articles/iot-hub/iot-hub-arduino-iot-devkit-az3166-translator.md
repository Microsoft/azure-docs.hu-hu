---
title: Translator Azure Functions, Cognitive Services, IoT fejlesztői készlet
description: A IoT-fejlesztői készlet található mikrofon használatával hangüzenetet kaphat, majd az Azure Cognitive Services használatával feldolgozhatja azt a lefordított szövegbe angol nyelven
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: be26c6fe03dac9b9ff9dbff4a2bdce391ec0837e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96024163"
---
# <a name="use-iot-devkit-az3166-with-azure-functions-and-cognitive-services-to-make-a-language-translator"></a>IoT fejlesztői készlet-AZ3166 használata Azure Functions és Cognitive Services a nyelvi fordítók elvégzéséhez

Ebből a cikkből megtudhatja, hogyan teheti IoT fejlesztői készlet az [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)használatával. Rögzíti a hangját, és lefordítja a fejlesztői készlet képernyőn megjelenő angol nyelvű szövegre.

A [MXChip IoT fejlesztői készlet](https://aka.ms/iot-devkit) egy all-in-One Arduino-kompatibilis tábla, amely gazdag perifériákkal és érzékelőkkel rendelkezik. A Visual Studio Code-ban az [Azure IoT Device Workbench](https://aka.ms/iot-workbench) vagy az [Azure IoT Tools](https://aka.ms/azure-iot-tools) Extension Pack használatával fejlesztheti azt. A [projects Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) a IoT-megoldások prototípusát segítő példákat tartalmaz.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag lépéseinek elvégzéséhez először hajtsa végre a következő feladatokat:

* Készítse elő a fejlesztői készlet a [IoT fejlesztői készlet AZ3166 és a felhőben lévő Azure IoT hub összekapcsolásához](./iot-hub-arduino-iot-devkit-az3166-get-started.md)szükséges lépéseket követve.

## <a name="create-azure-cognitive-service"></a>Az Azure kognitív szolgáltatás létrehozása

1. A Azure Portal kattintson az **erőforrás létrehozása** és a **beszéd** keresése elemre. Töltse ki az űrlapot a Speech Service-szolgáltatás létrehozásához.
  ![Beszédfelismerési szolgáltatás](media/iot-hub-arduino-iot-devkit-az3166-translator/speech-service.png)

1. Nyissa meg az imént létrehozott beszédfelismerési szolgáltatást, kattintson a **kulcsok** szakaszra a másoláshoz, és jegyezze fel a **Key1** a fejlesztői készlet eléréséhez.
  ![Kulcsok másolása](media/iot-hub-arduino-iot-devkit-az3166-translator/copy-keys.png)

## <a name="open-sample-project"></a>Minta projekt megnyitása

1. Győződjön meg arról, hogy a IoT fejlesztői készlet nincs **csatlakoztatva** a számítógéphez. Először indítsa el a VS Code-ot, majd kapcsolja össze a fejlesztői készlet a számítógéppel.

1. Kattintson a `F1` parancs palettájának megnyitásához, írja be a parancsot, majd válassza az **Azure IoT Device Workbench: Megnyitás példák..**. lehetőséget. Ezután válassza a **IoT fejlesztői készlet** lehetőséget.

1. A IoT Workbench-példák oldalon keresse meg a **fejlesztői készlet translatort** , és kattintson a **minta megnyitása** lehetőségre. Ezután kiválasztja a mintakód letöltésének alapértelmezett elérési útját.
  ![Minta megnyitása](media/iot-hub-arduino-iot-devkit-az3166-translator/open-sample.png)

## <a name="use-speech-service-with-azure-functions"></a>A Speech Service használata Azure Functions

1. A VS Code-ban kattintson a elemre `F1` , írja be és válassza az **Azure IoT Device Workbench: Azure-szolgáltatások kiépítése...** ![ lehetőséget. Azure-szolgáltatások kiépítése](media/iot-hub-arduino-iot-devkit-az3166-translator/provision.png)

1. Az Azure IoT Hub és Azure Functions üzembe helyezésének befejezéséhez kövesse az alábbi lépéseket.
   ![Kiépítési lépések](media/iot-hub-arduino-iot-devkit-az3166-translator/provision-steps.png)

   Jegyezze fel a létrehozott Azuure IoT Hub-eszköz nevét.

1. Nyissa meg `Functions\DevKitTranslatorFunction.cs` és frissítse a kód következő sorát a megjegyzett eszköz neve és a beszédfelismerési szolgáltatás kulcsaként.
   ```csharp
   // Subscription Key of Speech Service
   const string speechSubscriptionKey = "";

   // Region of the speech service, see https://docs.microsoft.com/azure/cognitive-services/speech-service/regions for more details.
   const string speechServiceRegion = "";

   // Device ID
   const string deviceName = "";
   ```

1. Kattintson `F1` , írja be és válassza **Az Azure IoT Device Workbench: üzembe helyezés az Azure**-ban... lehetőséget. Ha a VS Code megerősítést kér az újratelepítéshez, kattintson az **Igen** gombra.
   ![Figyelmeztetés üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-warning.png)

1. Győződjön meg arról, hogy az üzembe helyezés sikeres volt.
   ![Sikeres üzembe helyezés](media/iot-hub-arduino-iot-devkit-az3166-translator/deploy-success.png)

1. Azure Portal, lépjen a **functions-alkalmazások** szakaszra, keresse meg az imént létrehozott Azure Function alkalmazást. Kattintson `devkit_translator` a elemre, majd kattintson **</> a függvény URL-címének lekérése** elemre az URL másolásához
   ![Függvény URL-címének másolása](media/iot-hub-arduino-iot-devkit-az3166-translator/get-function-url.png)

1. Illessze be az URL-címet a `azure_config.h` fájlba.
   ![Azure-konfiguráció](media/iot-hub-arduino-iot-devkit-az3166-translator/azure-config.png)

   > [!NOTE]
   > Ha a Function alkalmazás nem működik megfelelően, a megoldáshoz olvassa el ezt a [GYIK](https://microsoft.github.io/azure-iot-developer-kit/docs/faq#compilation-error-for-azure-function) szakaszt.

## <a name="build-and-upload-device-code"></a>Eszköz kódjának létrehozása és feltöltése

1. Állítsa be a fejlesztői készlet **konfigurációs módba** a következő módon:
   * Tartsa lenyomva **A** gombot.
   * Nyomja le és szabadítsa fel az **Alaphelyzetbe állítás** gombot.

   Ekkor megjelenik a fejlesztői készlet-azonosító és- **konfiguráció**.

   ![Fejlesztői készlet-konfigurációs mód](media/iot-hub-arduino-iot-devkit-az3166-translator/devkit-configuration-mode.png)

1. Kattintson `F1` , írja be és válassza ki az **Azure IoT Device Workbench: eszközbeállítások konfigurálása... > a konfigurációs eszköz csatlakoztatási karakterláncát**. Válassza a **IoT hub eszköz kapcsolódási karakterlánc kiválasztása** lehetőséget a fejlesztői készlet való konfigurálásához.
   ![A kapcsolatok karakterláncának konfigurálása](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string.png)

1. Az értesítést a sikeres végrehajtás után fogja látni.
   ![A kapcsolatok karakterláncának konfigurálása sikeres](media/iot-hub-arduino-iot-devkit-az3166-translator/configure-connection-string-success.png)

1. Kattintson `F1` újra, írja be és válassza az **Azure IoT Device Workbench: eszköz kódjának feltöltése** lehetőséget. Elindul a kód fordítása és feltöltése a fejlesztői készlet.
   ![Eszköz feltöltése](media/iot-hub-arduino-iot-devkit-az3166-translator/device-upload.png)

## <a name="test-the-project"></a>A projekt tesztelése

Az alkalmazás inicializálását követően kövesse a DevKit képernyőjén megjelenő utasításokat. Az alapértelmezett forrásnyelv a kínai.

Másik nyelv kiválasztása a fordításhoz:

1. Nyomja le az A gombot a telepítési mód megadásához.

2. Nyomja le a B gombot az összes támogatott nyelv megjelenítéséhez.

3. Az A gomb megnyomásával erősítse meg a választott forrás nyelvét.

4. Beszéd közben nyomja le és tartsa nyomva a B gombot, majd engedje el a fordítás megkezdéséhez.

5. Az angolra fordított szöveg megjelenik a képernyőn.

![Görgetés a nyelv kiválasztásához](media/iot-hub-arduino-iot-devkit-az3166-translator/select-language.jpg)

![Fordítás eredménye](media/iot-hub-arduino-iot-devkit-az3166-translator/translation-result.jpg)

A fordítási eredmények képernyőjén az alábbi műveleteket végezheti:

- Az A és a B gomb lenyomásával görgethet és kiválaszthatja a forrásnyelvet.

- A B gombbal beszélhet. A hang elküldéséhez és a fordított szöveg lekéréséhez engedje el a B gombot.

## <a name="how-it-works"></a>Működés

![mini-Solution-hang – Tweet – diagram](media/iot-hub-arduino-iot-devkit-az3166-translator/diagram.png)

A IoT fejlesztői készlet rögzíti a hangot, majd HTTP-kérést küld a Azure Functions indítására. Azure Functions meghívja a kognitív szolgáltatás Speech Translator API-ját a fordítás végrehajtásához. Miután Azure Functions lekéri a fordítási szöveget, C2D üzenetet küld az eszköznek. Ekkor megjelenik a fordítás a képernyőn.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákba ütközik, tekintse meg a [IoT fejlesztői készlet GYIK](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) -t, vagy forduljon hozzánk a következő csatornákon keresztül:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan használhatja a IoT-fejlesztői készlet fordítóként Azure Functions és Cognitive Services használatával. Ebben a útmutatóban megtanulta, hogyan teheti meg a következőket:

> [!div class="checklist"]
> * A Felhőbeli céltartalékok automatizálása a Visual Studio Code használatával
> * Azure IoT-eszköz kapcsolási karakterláncának konfigurálása
> * Az Azure-függvény üzembe helyezése
> * A hangüzenet fordításának tesztelése

Folytassa a további oktatóanyagokkal:

> [!div class="nextstepaction"]
> [A IoT fejlesztői készlet AZ3166 és az Azure IoT távoli monitorozási megoldásának gyorsítása](./iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)