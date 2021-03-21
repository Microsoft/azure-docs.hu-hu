---
title: E-mail küldése, ha az ajtót a Azure Functions használatával nyitja meg
description: Figyelje meg a mágneses érzékelőt annak észleléséhez, hogy mikor nyílik meg egy ajtó, és a Azure Functions használatával küldjön e-mail-értesítést.
author: liydu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: e3649ac786c07459695a1e6ca6ad52620e23d59f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148808"
---
# <a name="door-monitor----using-azure-functions-and-sendgrid-send-email-when-a-door-is-opened"></a>Ajtó figyelője – a Azure Functions és a SendGrid használatával e-mail küldése az ajtó megnyitásakor           

A MXChip IoT fejlesztői készlet beépített mágneses érzékelőt tartalmaz. Ebben a projektben egy közeli erős mágneses mező jelenlétét vagy hiányát fogja érzékelni – ebben az esetben egy kis, állandó mágnesből származik.

## <a name="what-you-learn"></a>Ismertetett témák

Ebben a projektben a következőket tanulhatja meg:
- A MXChip IoT fejlesztői készlet mágneses érzékelő használata a közeli mágnesek mozgásának észlelésére.
- A SendGrid szolgáltatás használata az e-mail-címre küldött értesítések küldéséhez.

> [!NOTE]
> A projekt gyakorlati használatához hajtsa végre a következő feladatokat:
> - Egy mágnes csatlakoztatása egy ajtó széléhez.
> - Csatlakoztassa a fejlesztői készlet az ajtó ajtófélfa a mágnes közelébe. Az ajtó megnyitása vagy bezárása elindítja az érzékelőt, így az eseményről értesítő e-mail-értesítést kap.

## <a name="what-you-need"></a>Amire szükség lesz

Fejezze be a [első lépések útmutatót](iot-hub-arduino-iot-devkit-az3166-get-started.md) a következőre:

* A fejlesztői készlet csatlakoztatva van Wi-Fi
* A fejlesztési környezet előkészítése

Aktív Azure-előfizetés. Ha még nem rendelkezik ilyennel, a következő módszerek egyikével regisztrálhat:

* Aktiváljon egy [30 napos ingyenes próbaverziós Microsoft Azure fiókot](https://azure.microsoft.com/free/).
* Ha Ön MSDN-vagy Visual Studio-előfizető, igénybe veheti az [Azure-kreditet](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) .

## <a name="deploy-the-sendgrid-service-in-azure"></a>A SendGrid szolgáltatás üzembe helyezése az Azure-ban

A [SendGrid](https://sendgrid.com/) egy felhőalapú e-mail-kézbesítési platform. A szolgáltatás e-mail-értesítések küldésére szolgál.

> [!NOTE]
> Ha már telepített egy SendGrid-szolgáltatást, akkor [a IoT hub Azure-ban való üzembe helyezését](#deploy-iot-hub-in-azure)is folytathatja.

### <a name="sendgrid-deployment"></a>SendGrid üzembe helyezése

Az Azure-szolgáltatások kiépítéséhez használja az **üzembe helyezés az Azure** -ban gombot. Ez a gomb lehetővé teszi a nyílt forráskódú projektek gyors és egyszerű üzembe helyezését Microsoft Azure.

Kattintson az alábbi **üzembe helyezés az Azure-** ban gombra. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

Ha még nem jelentkezett be az Azure-fiókjába, jelentkezzen be most. 

Ekkor megjelenik a SendGrid regisztrációs űrlapja.

![SendGrid üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

Fejezze be a regisztrációs űrlapot:

   * **Erőforráscsoport**: hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás futtatásához, vagy használjon egy meglévőt. Lásd: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **Name (név**): a SendGrid szolgáltatás neve. Válasszon egy egyedi nevet, amely eltér az esetlegesen más szolgáltatástól.

   * **Password (jelszó**): a szolgáltatáshoz jelszó szükséges, amely nem használható a projekt minden részében.

   * **E-mail**: a SendGrid szolgáltatás ellenőrzést küld erre az e-mail-címre.

Jelölje be a **rögzítés az irányítópulton** lehetőséget, hogy az alkalmazás könnyebben megkereshető legyen a jövőben, majd kattintson a **vásárlás** gombra a bejelentkezési űrlap elküldéséhez.
 
### <a name="sendgrid-api-key-creation"></a>SendGrid API-kulcs létrehozása

Miután az üzembe helyezés befejeződött, kattintson rá, majd kattintson a **kezelés** gombra. Megjelenik a SendGrid-fiók oldala, ahol ellenőriznie kell az e-mail-címét.

![SendGrid kezelése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

A SendGrid lapon kattintson a **Beállítások**  >  **API-kulcsok**  >  **create API-kulcs** elemre.

![SendGrid első API létrehozása](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

Az **API-kulcs létrehozása** lapon adja meg az **API-kulcs nevét** , majd kattintson a **Létrehozás & nézet** elemre.

![SendGrid API létrehozása második](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

Az API-kulcs egyszerre csak egyszer jelenik meg. Ügyeljen arra, hogy biztonságosan másolja és tárolja, ahogy azt a következő lépésben használják.

## <a name="deploy-iot-hub-in-azure"></a>IoT Hub üzembe helyezése az Azure-ban

A következő lépések az Azure IoT kapcsolódó szolgáltatásainak kiépítését és Azure Functions üzembe helyezését teszik ki ehhez a projekthez.

Kattintson az alábbi **üzembe helyezés az Azure-** ban gombra. 

[![Üzembe helyezés az Azure-ban](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

Megjelenik a regisztrációs űrlap.

![IoTHub üzembe helyezése](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

Töltse ki a mezőket a regisztrációs űrlapon.

   * **Erőforráscsoport**: hozzon létre egy erőforráscsoportot a SendGrid szolgáltatás futtatásához, vagy használjon egy meglévőt. Lásd: [erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/manage-resource-groups-portal.md).

   * **IOT hub neve**: az IOT hub neve. Válasszon egy egyedi nevet, amely eltér az esetlegesen más szolgáltatástól.

   * **IOT hub SKU**: F1 (legfeljebb egy előfizetés) ingyenes. További díjszabási információk a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/iot-hub/)találhatók.

   * E **-mail-ből**: ebben a mezőben ugyanazt az e-mail-címet kell megadni, amelyet a SendGrid szolgáltatás beállításakor használt.

Jelölje be a **rögzítés az irányítópulton** lehetőséget, hogy az alkalmazás könnyebben megkereshető legyen a jövőben, majd kattintson a **vásárlás** elemre, amikor készen áll a következő lépésre.
 
## <a name="build-and-upload-the-code"></a>A kód létrehozása és feltöltése

Ezután töltse be a kódot a VS Code-ban, és a szükséges Azure-szolgáltatásokat kiépítse.

### <a name="start-vs-code"></a>Start VS Code

- Győződjön meg arról, hogy a fejlesztői készlet **nincs csatlakoztatva a** számítógéphez.
- Indítsa el a VS Code-ot.
- Kapcsolódjon a fejlesztői készlet a számítógéphez.

> [!NOTE]
> A VS Code indításakor hibaüzenetet kaphat arról, hogy az Arduino IDE vagy a kapcsolódó tábla csomagja nem található. Ha ezt a hibaüzenetet kapja, akkor a VS Code-ban nyissa meg újra az Arduino IDE-t, és a VS Code-nak helyesen kell megkeresnie az Arduino IDE-útvonalat

### <a name="open-arduino-examples-folder"></a>Az Arduino-példák mappájának megnyitása

Bontsa ki a bal oldali **ARDUINO-példák** szakaszt, és keresse meg a **PÉLDÁKAT a MXCHIP AZ3166 > AzureIoT**, és válassza a **DoorMonitor** lehetőséget. Ez a művelet egy új VS Code-ablakot nyit meg egy projekt mappájával.

![mini-Solution – példák](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

A példában szereplő alkalmazást a parancssorból is megnyithatja. `Ctrl+Shift+P`A (MacOS: `Cmd+Shift+P` ) paranccsal nyissa meg a parancssort, írja be az **Arduino** parancsot, majd keresse meg és válassza ki az **Arduino: példák** elemet.

### <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

A megoldás ablakban futtassa a Cloud kiépítési feladatot:
- Típus `Ctrl+P` (MacOS: `Cmd+P` ).
- Adja meg `task cloud-provision` a megadott szövegmezőt.

A VS Code terminalban egy interaktív parancssor végigvezeti a szükséges Azure-szolgáltatások kiépítési folyamatán. Válassza ki az összes olyan elemet a kért listából, amelyet korábban az Azure-beli [üzembe helyezési IoT hub](#deploy-iot-hub-in-azure)kiépített.

![Felhőbeli kiépítés](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> Ha a lap a betöltési állapotba kerül, amikor megpróbál bejelentkezni az Azure-ba, a probléma megoldásához tekintse meg az ["oldal lefagy a IoT fejlesztői készlet gyakori kérdések a bejelentkezéskor" című szakaszát](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure) . 

### <a name="build-and-upload-the-device-code"></a>Az eszköz kódjának létrehozása és feltöltése

Ezután töltse fel az eszköz kódját.

#### <a name="windows"></a>Windows

1. `Ctrl+P`A futtatásához használja a parancsot `task device-upload` .

2. A terminál felszólítja a konfigurációs mód megadására. Ehhez tartsa lenyomva az A gombot, majd nyomja le és szabadítsa fel az Alaphelyzetbe állítás gombot. A képernyő megjeleníti a fejlesztői készlet azonosító számát és a szó *konfigurációját*.

#### <a name="macos"></a>macOS

1. Helyezze a fejlesztői készlet konfigurációs módba: tartsa lenyomva a gombot, majd nyomja le és szabadítsa fel az Alaphelyzetbe állítás gombot. A képernyő "Configuration" (konfiguráció) értéket jelenít meg.

2. Kattintson `Cmd+P` a futtatáshoz `task device-upload` .

#### <a name="verify-upload-and-run-the-sample-app"></a>A minta alkalmazás ellenőrzése, feltöltése és futtatása

Az [Azure-szolgáltatások kiépítése](#provision-azure-services) lépésből beolvasott kapcsolatok karakterlánca most be van állítva. 

A VS Code ezután elkezdi ellenőrizni és feltölteni az Arduino-vázlatot a fejlesztői készlet.

![A képernyőképen a Visual Studio Code az Arduino-vázlat ellenőrzése és feltöltése látható.](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

A DevKit újraindul, és elkezdi a kód futtatását.

> [!NOTE]
> Időnként előfordulhat, hogy a "hiba: AZ3166: ismeretlen csomag" hibaüzenet jelenik meg. Ez a hiba akkor fordul elő, ha a tábla csomagjának indexe nem frissül megfelelően. A hiba megoldásához tekintse meg a [IoT fejlesztői készlet – gyakori kérdések című témakör fejlesztés szakaszát](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>A projekt tesztelése

A program először inicializálja, ha a fejlesztői készlet egy stabil mágneses mező jelenlétében van.

Az inicializálás után megjelenik `Door closed` a képernyőn. Ha megváltozik a mágneses mező, az állapot módosul `Door opened` . Minden alkalommal, amikor az ajtó állapota megváltozik, e-mailben értesítést kap. (Ezek az e-mail-üzenetek fogadása akár öt percet is igénybe vehet.)

![Az érzékelőhöz közeledő mágnesek: zárt ajtó](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "Az érzékelőhöz közeledő mágnesek: zárt ajtó")

![A mágnes el lett helyezve az érzékelőből: nyitott ajtó](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "A mágnes el lett helyezve az érzékelőből: nyitott ajtó")

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Ha problémákba ütközik, tekintse meg a [IoT fejlesztői készlet kapcsolatos gyakori kérdéseket](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) , vagy kapcsolódjon a következő csatornákon keresztül:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan csatlakoztatható egy fejlesztői készlet-eszköz az Azure IoT-alapú távoli monitorozási megoldáshoz, és a SendGrid szolgáltatás használatával küldjön e-mailt. Íme a javasolt következő lépés: az[Azure IoT távoli monitorozási megoldásának gyorsítása – áttekintés](/azure/iot-suite/)