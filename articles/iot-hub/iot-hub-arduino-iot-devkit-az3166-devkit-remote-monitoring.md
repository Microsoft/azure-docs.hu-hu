---
title: A MXChip IoT fejlesztői készlet összekötése az Azure IoT Hub távoli monitorozásával
description: Ebből az oktatóanyagból megtudhatja, hogyan küldheti el az érzékelők állapotát a IoT fejlesztői készlet AZ3166 az Azure IoT távoli figyelési megoldásának gyorsítása érdekében.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 6912124ce8d1741731d625dccfea445417b2488d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98785016"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>A MXChip IoT fejlesztői készlet és az Azure IoT távoli monitorozási megoldásának gyorsítása

Ebből az oktatóanyagból megtudhatja, hogyan futtathat egy minta alkalmazást a fejlesztői készlet, hogy elküldje az érzékelők adatait az Azure IoT-alapú távoli monitorozási megoldásának gyorsítása érdekében.

A [MXChip IoT fejlesztői készlet](https://aka.ms/iot-devkit) egy all-in-One Arduino-kompatibilis tábla, amely gazdag perifériákkal és érzékelőkkel rendelkezik. Az Arduino-hez készült [Visual Studio Code-bővítmény](https://aka.ms/arduino)használatával fejlesztheti azt. Az egyre bővülő [projektek katalógusa](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) pedig a Microsoft Azure-szolgáltatások előnyeit kihasználó prototípus-eszközök internetes hálózata (IoT) megoldásokat ismerteti.

## <a name="what-you-need"></a>Amire szükség lesz

Fejezze be a [első lépések útmutatót](./iot-hub-arduino-iot-devkit-az3166-get-started.md) a következőre:

* A fejlesztői készlet csatlakoztatva van Wi-Fi
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha még nem rendelkezik ilyennel, a következő két módszer egyikével regisztrálhat:

* Ingyenes, [30 napos próbaidőszak Microsoft Azure fiók](https://azure.microsoft.com/free/) aktiválása

* Azure- [kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) igénylése MSDN-vagy Visual Studio-előfizető esetén

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Azure IoT-alapú távoli monitorozási megoldás-gyorsító létrehozása

1. Lépjen az [Azure IoT megoldás-gyorssegédek webhelyére](https://www.azureiotsolutions.com/) , és kattintson **az új megoldás létrehozása** lehetőségre.

   ![Válassza ki az Azure IoT-megoldás gyorsító típusát](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Alapértelmezés szerint ez a minta egy S2 IoT hub hoz létre, miután létrehoz egy IoT távoli figyelési megoldás-gyorsító. Ha ez az IoT hub nem használatos nagy számú eszközzel, javasoljuk, hogy az S2-től S1-ig visszaminősítse azt, és törölje a IoT távoli figyelési megoldását, így a kapcsolódó IoT Hub is törölhető, ha már nincs rá szükség. 

2. Válassza a **távoli figyelés** lehetőséget.

3. Adja meg a megoldás nevét, válassza ki az előfizetést és a régiót, majd kattintson a **megoldás létrehozása** lehetőségre. Előfordulhat, hogy a megoldás üzembe helyezése hosszabb ideig tart.
  
   ![Megoldás létrehozása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. A kiépítés befejezése után kattintson az **Indítás** gombra. Néhány szimulált eszköz jön létre a megoldáshoz a létesítési folyamat során. A kijelentkezéshez kattintson az **eszközök** elemre.

   ![Irányítópult](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Console](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Kattintson **az eszköz hozzáadása** lehetőségre.

6. Kattintson az **új hozzáadása** **Egyéni eszközhöz** elemre.
  
   ![Új eszköz hozzáadása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Kattintson **a saját eszköz azonosítójának definiálása** elemre, írja be a értéket `AZ3166` , majd kattintson a **Létrehozás** gombra.
  
   ![AZONOSÍTÓval rendelkező eszköz létrehozása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Jegyezze fel **IoT hub hostname nevet**, és kattintson a **kész** gombra.

## <a name="open-the-remotemonitoring-sample"></a>A RemoteMonitoring minta megnyitása

1. Ha csatlakoztatva van, válassza le a fejlesztői készlet a számítógépről.

2. Indítsa el a VS Code-ot.

3. Kapcsolódjon a fejlesztői készlet a számítógéphez. A VS Code automatikusan észleli a fejlesztői készlet, és megnyitja a következő lapokat:

   * A fejlesztői készlet-bevezetés oldal.
   * Arduino-példák: gyakorlati minták a fejlesztői készlet megkezdéséhez.

4. Bontsa ki a bal oldali **ARDUINO-példák** szakaszt, és keresse meg a **PÉLDÁKAT a MXCHIP AZ3166 > AzureIoT**, és válassza a **RemoteMonitoring** lehetőséget. Megnyílik egy új VS Code-ablak a projekt mappájával.

   > [!NOTE]
   > Ha véletlenül bezárná a panelt, újra megnyithatja. `Ctrl+Shift+P`A (MacOS: `Cmd+Shift+P` ) paranccsal nyissa meg a parancssort, írja be az **Arduino** parancsot, majd keresse meg és válassza ki az **Arduino: példák** elemet.

## <a name="provision-required-azure-services"></a>Szükséges Azure-szolgáltatások kiépítése

A megoldás ablakban futtassa a `Ctrl+P` (z) (MacOS:) feladatot a `Cmd+P` megadott szövegmező beírásával `task cloud-provision` .

A VS Code terminalban egy interaktív parancssor végigvezeti a szükséges Azure-szolgáltatások kiépítési folyamatán.

![Azure-erőforrások kiépítése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>Az eszköz kódjának létrehozása és feltöltése

1. Használja `Ctrl+P` a (MacOS: `Cmd + P` ) parancsot, és írja be a következőt: **Task config-Device--kapcsolatok**.

2. A terminál megkérdezi, hogy szeretné-e használni a lépésből lekért kapcsolódási karakterláncot `task cloud-provision` . Az "új létrehozása..." gombra kattintva is beírhatja a saját eszköz-kapcsolatok sztringjét.

3. A terminál felszólítja a konfigurációs mód megadására. Ehhez tartsa lenyomva az A gombot, majd nyomja le és szabadítsa fel az Alaphelyzetbe állítás gombot. A képernyőn a fejlesztői készlet-azonosító és a "konfiguráció" látható.

   ![Bemeneti kapcsolatok karakterlánca](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. `task config-device-connection`A befejezést követően kattintson ide `F1` a vs Code-parancsok betöltéséhez és a kijelöléshez `Arduino: Upload` . A VS Code elindítja az Arduino-vázlat ellenőrzését és feltöltését.
  
   ![Az Arduino-vázlat ellenőrzése és feltöltése](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

A DevKit újraindul, és elkezdi a kód futtatását.

## <a name="test-the-project"></a>A projekt tesztelése

A minta alkalmazás futtatásakor a fejlesztői készlet Wi-Fi-kapcsolaton keresztül küldi el az érzékelő adatait az Azure IoT távoli monitorozási megoldásának gyorsítása érdekében. Az eredmény megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az Azure IoT távoli figyelési megoldásának Gyorssegédjét, és kattintson az **irányítópult** elemre.

2. A távoli figyelési megoldás konzolján megjelenik a fejlesztői készlet-érzékelő állapota.

   ![Érzékelői az Azure IoT távoli monitorozási megoldásának gyorsítása](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Eszköz AZONOSÍTÓjának módosítása

Ha módosítani szeretné a hardcoded **AZ3166** a kód testreszabott eszköz-azonosítójára, módosítsa a [távoli figyelési példában](/previous-versions/azure/iot-accelerators/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoring-v2)megjelenő kód sorát.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákba ütközik, tekintse meg [a IoT Developer Kit GYIK](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) -t, vagy forduljon hozzánk a következő csatornákon keresztül:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan csatlakoztatható egy fejlesztői készlet-eszköz az Azure IoT távoli figyelési megoldás-gyorsító szolgáltatáshoz, és Hogyan jeleníthető meg az érzékelők adatai, az alábbiakban a javasolt következő lépések láthatók:

* [Az Azure IoT megoldás-gyorsítók áttekintése](/azure/iot-suite/)

* [IoT fejlesztői csomag](https://microsoft.github.io/azure-iot-developer-kit/)