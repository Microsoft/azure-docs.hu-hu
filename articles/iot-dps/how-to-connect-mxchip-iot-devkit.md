---
title: Az Azure IoT Hub Device Provisioning Service automatikus kiépítés használata a MXChip-IoT fejlesztői készlet regisztrálásához IoT Hub használatával | Microsoft Docs
description: Az Azure IoT Hub Device Provisioning Service (DPS) automatikus kiépítés használata a MXChip-IoT fejlesztői készlet a IoT Hubsal való regisztrálásához.
author: wesmc7777
ms.author: wesmc
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: d6b6649d03da319171b24baa24983972bf270679
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954545"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Az Azure IoT Hub Device Provisioning Service automatikus kiépítés használata a MXChip-IoT fejlesztői készlet regisztrálásához IoT Hub

Ez a cikk azt ismerteti, hogyan használható az Azure IoT hub Device Provisioning Service a MXChip-IoT fejlesztői készlet Azure-IoT hub való [kiépítés](about-iot-dps.md#provisioning-process) . Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

* Konfigurálja az eszköz kiépítési szolgáltatásának globális végpontját egy eszközön.
* X. 509 tanúsítvány létrehozásához használjon egyedi frissítési.
* Egyéni eszköz regisztrálása.
* Ellenőrizze, hogy az eszköz regisztrálva van-e.

A [MXChip IoT fejlesztői készlet](https://aka.ms/iot-devkit) egy all-in-One Arduino-kompatibilis tábla, amely gazdag perifériákkal és érzékelőkkel rendelkezik. A Visual Studio Code-ban az [Azure IoT Device Workbench](https://aka.ms/iot-workbench) vagy az [Azure IoT Tools](https://aka.ms/azure-iot-tools) Extension Pack használatával fejlesztheti azt. A fejlesztői készlet az Azure-szolgáltatások előnyeit kihasználó prototípus-eszközök internetes hálózata (IoT) megoldások útmutatója az egyre növekvő [projektekhez](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) .

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag lépéseinek elvégzéséhez először hajtsa végre a következő feladatokat:

* Konfigurálja a fejlesztői készlet Wi-Fiét, és készítse elő a fejlesztési környezetet a "fejlesztési környezet előkészítése" című szakaszban ismertetett lépéseket a [IoT fejlesztői készlet AZ3166 az Azure IoT hub a felhőben](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment)című szakaszának lépéseit követve.
* Frissítsen a legújabb belső vezérlőprogram-re (1.3.0 vagy újabb verzióra) az [Update fejlesztői készlet belső vezérlőprogram](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) -oktatóanyagával.
* Hozzon létre és csatoljon egy IoT Hubt egy eszköz kiépítési szolgáltatásának példányával a [IoT hub Device Provisioning Service beállítása a Azure Portal](./quick-setup-auto-provision.md)használatával című rész lépéseit követve.

## <a name="open-sample-project"></a>Minta projekt megnyitása

1. Győződjön meg arról, hogy a IoT fejlesztői készlet nincs **csatlakoztatva** a számítógéphez. Először indítsa el a VS Code-ot, majd kapcsolja össze a fejlesztői készlet a számítógéppel.

1. Kattintson a `F1` parancs palettájának megnyitásához, írja be a parancsot, majd válassza az **Azure IoT Device Workbench: Megnyitás példák..**. lehetőséget. Ezután válassza a **IoT fejlesztői készlet** lehetőséget.

1. A IoT Workbench-példák oldalon keresse meg az **eszközök regisztrációja a DPS-vel** és kattintson a **minta megnyitása** lehetőségre. Ezután kiválasztja a mintakód letöltésének alapértelmezett elérési útját.
    ![Minta megnyitása](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Egyedi eszköz titkos kulcsának mentése az eszköz biztonsági tárolóján

Az automatikus kiépítés konfigurálható az eszköz [igazolási mechanizmusa](concepts-service.md#attestation-mechanism)alapján. A MXChip IoT fejlesztői készlet az [eszköz Identity kompozíciós motorját](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) használja a [Trusted Computing Group](https://trustedcomputinggroup.org). Az eszköz egyedi [X. 509 tanúsítványának](concepts-x509-attestation.md)létrehozásához a rendszer az STSAFE biztonsági chipben ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) mentett egyedi frissítési- **kulcsot** használja. A tanúsítvány később a beléptetési folyamathoz használatos az eszköz kiépítési szolgáltatásában, és a regisztráció során futásidőben.

A tipikus frissítési egy 64 karakterből álló karakterlánc, amely a következő mintában látható:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

FRISSÍTÉSI mentése a fejlesztői készlet:

1. A VS Code-ban kattintson az állapotsorra a fejlesztői készlet COM-portjának kiválasztásához.
  ![COM-port kiválasztása](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. A fejlesztői készlet-on tartsa lenyomva az **a gombot**, leküldheti és felszabadíthatja az **Alaphelyzetbe állítás** gombot, majd **a "a" gombot**. A fejlesztői készlet konfigurációs módba lép.

1. Kattintson a `F1` parancs palettájának megnyitásához, írja be a parancsot, majd válassza az **Azure IoT Device Workbench: eszközbeállítások konfigurálása... > konfiguráció egyedi eszköz SZTRING (frissítési)** elemet.
  ![FRISSÍTÉSI konfigurálása](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Jegyezze fel a generált frissítési karakterláncot. Szüksége lesz rá az X. 509 tanúsítvány létrehozásához. Ezután nyomja meg a gombot `Enter` .
  ![FRISSÍTÉSI másolása](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Erősítse meg az értesítést arról, hogy a frissítési sikeresen konfigurálva lett a STSAFE.
  ![A frissítési konfigurálása sikeres](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Azt is megteheti, hogy a frissítési a soros porton keresztül konfigurálja olyan segédprogramok használatával, mint például a Putty. Kövesse a [konfigurációs mód használata beállítást](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) .

## <a name="update-the-global-device-endpoint-and-id-scope"></a>A globális eszköz végpontjának és AZONOSÍTÓjának hatókörének frissítése

Az eszköz kódjában meg kell adnia az [eszköz kiépítési végpontját](./concepts-service.md#device-provisioning-endpoint) és az azonosító hatókörét a bérlő elkülönítésének biztosításához.

1. A Azure Portal válassza ki az eszköz kiépítési szolgáltatásának **Áttekintés** paneljét, és jegyezze fel a **globális eszköz végpontját** és az **azonosító hatókörének** értékeit.
  ![Eszköz kiépítési szolgáltatás globális végpontja és azonosító hatóköre](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Nyissa meg a **DevKitDPS. Ino** programot. Keresse meg és `[Global Device Endpoint]` cserélje `[ID Scope]` le az imént feljegyzett értékeket.
  ![Eszköz kiépítési szolgáltatásának végpontja](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Töltse ki a `registrationId` változót a kódban. Csak alfanumerikus, kisbetűs és kötőjel kombináció megengedett, amely legfeljebb 128 karakterből állhat. Az értéket is fel kell jegyezni.
  ![Regisztrációs azonosító](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Kattintson `F1` , írja be és válassza ki az **Azure IoT Device Workbench: eszköz kódjának feltöltése** elemet. Elindítja a kód fordítását és feltöltését a fejlesztői készlet.
  ![Eszköz feltöltése](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>X. 509 tanúsítvány előállítása

A minta által használt [igazolási mechanizmus](./concepts-service.md#attestation-mechanism) X. 509 tanúsítvány. A létrehozásához egy segédprogramot kell használnia.

1. A VS Code-ban kattintson a elemre `F1` , írja be a parancsot, majd válassza az **új terminál megnyitása** lehetőséget a terminálablak megnyitásához.

1. Futtatás `dps_cert_gen.exe` a `tool` mappában.

1. A lefordított bináris fájl helyét a következőképpen adhatja meg: `..\.build\DevKitDPS` . Ezután illessze be az imént feljegyzett **frissítési** és **regisztrációban** . 
  ![X. 509 előállítása](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Egy `.pem` X. 509 tanúsítvány jön létre ugyanabban a mappában.
  ![X. 509 fájl](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Eszközregisztrációs bejegyzés létrehozása

1. A Azure Portal nyissa meg az eszköz üzembe helyezése szolgáltatást, navigáljon a regisztrációk kezelése szakaszhoz, és kattintson az **Egyéni regisztráció hozzáadása** lehetőségre.
  ![Egyéni regisztráció hozzáadása](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Kattintson a fájl ikonra az **elsődleges tanúsítvány. PEM vagy. cer fájl** mellett, hogy feltöltse a `.pem` létrehozott fájlt.
  ![Feltöltés. PEM](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Ellenőrizze, hogy az fejlesztői készlet regisztrálva van-e az Azure IoT Hub

Nyomja meg az **Alaphelyzetbe állítás** gombot a fejlesztői készlet. Látnia kell a **DPS Connected!** a fejlesztői készlet képernyőn. Az eszköz újraindítása után a következő műveletek végrehajtására kerül sor:

1. Az eszköz regisztrációs kérést küld a Device Provisioning Service-nek.
1. Az eszköz-kiépítési szolgáltatás olyan regisztrációs kihívást küld vissza, amelyhez az eszköz válaszol.
1. Sikeres regisztráció esetén az eszköz kiépítési szolgáltatása elküldi a IoT Hub URI-t, az eszköz AZONOSÍTÓját és a titkosított kulcsot az eszközre.
1. Az eszközön lévő IoT Hub ügyfélalkalmazás csatlakozik a központhoz.
1. Ha sikeresen csatlakozik a központhoz, megjelenik az eszköz a IoT Hub Device Explorer.
  ![Eszköz regisztrálva](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákba ütközik, tekintse meg a IOT fejlesztői készlet [Gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)című témakört, vagy forduljon a következő csatornákhoz:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan regisztrálhat egy eszközt biztonságosan az eszköz kiépítési szolgáltatásához az eszköz identitás-összeállítási motorjának használatával, hogy az eszköz automatikusan regisztrálja az Azure IoT Hub-t. 

Összefoglalva, megtanulta, hogyan végezheti el a következőket:

> [!div class="checklist"]
> * Konfigurálja az eszköz kiépítési szolgáltatásának globális végpontját egy eszközön.
> * X. 509 tanúsítvány létrehozásához használjon egyedi eszköz titkos kulcsot.
> * Egyéni eszköz regisztrálása.
> * Ellenőrizze, hogy az eszköz regisztrálva van-e.

Megtudhatja, hogyan [hozhat létre és helyezhet üzembe szimulált eszközt](./quick-create-simulated-device.md).