---
title: Twitter-üzenet beolvasása Azure Functions | Microsoft Docs
description: A mozgásérzékelő használata a megrázás észlelésére és a Azure Functions használata véletlenszerű tweetek kereséséhez a megadott hashtag használatával
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/07/2018
ms.author: liydu
ms.custom: devx-track-csharp
ms.openlocfilehash: af1685f6455c0642800cba7dd604fcc836bcd7a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147906"
---
# <a name="shake-shake-for-a-tweet----retrieve-a-twitter-message-with-azure-functions"></a>Shake, Shake for a tweet – Twitter-üzenet beolvasása Azure Functions

Ebből a projektből megtudhatja, hogyan indíthat el egy eseményt a mozgásérzékelővel Azure Functions használatával. Az alkalmazás egy véletlenszerű tweetet kérdez le egy, az Arduino-vázlatban konfigurált #hashtag. A tweet a fejlesztői készlet képernyőn jelenik meg.

## <a name="what-you-need"></a>Amire szükség lesz

Fejezze be a [első lépések útmutatót](./iot-hub-arduino-iot-devkit-az3166-get-started.md) a következőre:

* Csatlakoztassa a fejlesztői készlet a Wi-Fi-hez.
* Készítse elő a fejlesztési környezetet.

Aktív Azure-előfizetés. Ha még nem rendelkezik ilyennel, a következő módszerek egyikével regisztrálhat:

* Ingyenes, [30 napos próbaidőszak Microsoft Azure fiók](https://azure.microsoft.com/free/) aktiválása
* Azure- [kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) igénylése MSDN-vagy Visual Studio-előfizető esetén

## <a name="open-the-project-folder"></a>A Project mappa megnyitása

Először nyissa meg a Project mappát. 

### <a name="start-vs-code"></a>Start VS Code

* Győződjön meg arról, hogy a fejlesztői készlet csatlakoztatva van a számítógéphez.

* Indítsa el a VS Code-ot.

* Kapcsolódjon a fejlesztői készlet a számítógéphez.

   > [!NOTE]
   > A VS Code indításakor előfordulhat, hogy hibaüzenetet kap, hogy az Arduino IDE vagy a kapcsolódó tábla csomagja nem található. Ha ez a hiba bekövetkezik, akkor a VS Code bezárásával indítsa el újra az Arduino IDE-t. A VS Code-ban ekkor helyesen kell megkeresni az Arduino IDE-útvonalat.

### <a name="open-the-arduino-examples-folder"></a>Az Arduino-példák mappa megnyitása

Bontsa ki a bal oldali **ARDUINO-példák** szakaszt, és keresse meg a **PÉLDÁKAT a MXCHIP AZ3166 > AzureIoT**, és válassza a **ShakeShake** lehetőséget. Megnyílik egy új VS Code ablak, amely megjeleníti a projekt mappáját. Ha nem látja a MXCHIP AZ3166 szakaszt, győződjön meg arról, hogy az eszköz megfelelően van csatlakoztatva, és indítsa újra a Visual Studio Code-ot.  
a ![ mini-Solution példák](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/vscode_examples.png)

A minta projektet a parancs palettáról is megnyithatja. Kattintson `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P` ) a parancs paletta megnyitásához, írja be az **Arduino** parancsot, majd keresse meg és válassza ki az **Arduino: példák** elemet.

## <a name="provision-azure-services"></a>Azure-szolgáltatások kiépítése

A megoldás ablakban futtassa a `Ctrl+P` (z) (MacOS:) feladatot a következő `Cmd+P` megadásával: `task cloud-provision` .

A VS Code terminalban egy interaktív parancssor végigvezeti a szükséges Azure-szolgáltatások kiépítési folyamatán:

![A képernyőképen a Visual Studio Code Terminal interaktív parancssora látható.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-provision.png)

> [!NOTE]
> Ha a lap a betöltési állapotba kerül, amikor megpróbál bejelentkezni az Azure-ba, tekintse meg a ["bejelentkezési oldal lefagy" lépést a IoT fejlesztői készlet – gyakori kérdések című témakörben](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure).
 
## <a name="modify-the-hashtag"></a>A #hashtag módosítása

Nyissa meg és keresse meg a következő `ShakeShake.ino` kódrészletet:

```cpp
static const char* iot_event = "{\"topic\":\"iot\"}";
```

Cserélje le a karakterláncot `iot` a kapcsos zárójelek között a kívánt hashtagre. A fejlesztői készlet később egy véletlenszerű tweetet kér le, amely tartalmazza az ebben a lépésben megadott hashtag-t.

## <a name="deploy-azure-functions"></a>Az Azure Functions üzembe helyezése

A `Ctrl+P` futtatásához használja a (MacOS: `Cmd+P` ) parancsot a `task cloud-deploy` Azure functions kód üzembe helyezésének megkezdéséhez:

![A képernyőfelvételen a Visual Studio Code látható, ahol futtathatja a Cloud-Deploy feladatot Azure Functions kód üzembe helyezéséhez.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/cloud-deploy.png)

> [!NOTE]
> Esetenként előfordulhat, hogy az Azure-függvény nem működik megfelelően. A probléma elhárításához olvassa el a [IoT fejlesztői készlet – gyakori kérdések című témakör "fordítási hiba" szakaszát](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#compilation-error-for-azure-function).

## <a name="build-and-upload-the-device-code"></a>Az eszköz kódjának létrehozása és feltöltése

Ezután hozza létre és töltse fel az eszköz kódját.

### <a name="windows"></a>Windows

1. `Ctrl+P`A futtatásához használja a parancsot `task device-upload` .

2. A terminál felszólítja a konfigurációs mód megadására. Ehhez tegye a következőket:

   * A gomb lenyomva tartása

   * Az Alaphelyzetbe állítás gomb leküldése és felszabadítása

3. A képernyőn a fejlesztői készlet-azonosító és a "konfiguráció" látható.

### <a name="macos"></a>macOS

1. Helyezze a fejlesztői készlet konfigurációs módba:

   Tartsa lenyomva az A gombot, majd nyomja le és szabadítsa fel az Alaphelyzetbe állítás gombot. A képernyő "Configuration" (konfiguráció) értéket jelenít meg.

2. A `Cmd+P` futtatásával `task device-upload` állítsa be a lépésből beolvasott kapcsolódási karakterláncot `task cloud-provision` .

### <a name="verify-upload-and-run"></a>Ellenőrzés, feltöltés és Futtatás

A rendszer most a kapcsolatok karakterláncát állítja be, ellenőrzi és feltölti az alkalmazást, majd futtatja azt. 

1. A VS Code elindítja az Arduino-vázlat ellenőrzését és feltöltését a fejlesztői készlet:

   ![A képernyőképen a Visual Studio Code az Arduino-vázlat ellenőrzése és feltöltése látható.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/device-upload.png)

2. A DevKit újraindul, és elkezdi a kód futtatását.

"Hiba: AZ3166: ismeretlen csomag" hibaüzenet jelenik meg. Ez a hiba akkor fordul elő, ha a tábla csomagjának indexe nem megfelelően frissül. A probléma megoldásához olvassa el a ["ismeretlen csomag" hibát a IoT fejlesztői készlet gyakori kérdések](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)című témakörben.

## <a name="test-the-project"></a>A projekt tesztelése

Az alkalmazás inicializálását követően kattintson A gombra, és az A gombot, majd óvatosan rázzuk fel a fejlesztői készlet-táblát. Ez a művelet egy véletlenszerű tweetet kérdez le, amely a korábban megadott hashtag-t tartalmazza. Néhány másodpercen belül egy Tweet jelenik meg a fejlesztői készlet képernyőn:

### <a name="arduino-application-initializing"></a>Arduino-alkalmazás inicializálása...

![Arduino – alkalmazás – inicializálás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-1.png)

### <a name="press-a-to-shake"></a>Nyomja meg A gombot a Shake...

![A-A-Shake megnyomása](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-2.png)

### <a name="ready-to-shake"></a>Felrázni kész...

![Készen áll a megrázás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-3.png)

### <a name="processing"></a>Feldolgozás folyamatban...

![Feldolgozás](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-4.png)

### <a name="press-b-to-read"></a>A B gomb megnyomásával olvashatja...

![A-B és a-Read gomb megnyomása](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-5.png)

### <a name="display-a-random-tweet"></a>Véletlenszerű Tweet megjelenítése...

![A-Random-Tweet megjelenítése](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/result-6.png)

- Nyomja le ismét A gombot, majd rázzuk fel egy új tweetet.
- A "B" gomb megnyomásával görgetheti a tweet többi részét.

## <a name="how-it-works"></a>Működés

![Az ábrán egy olyan mobileszköz látható, amely egy eseményt küld az Azure I o T-hubhoz, amely egy Azure Function app-alkalmazást indít el egy Tweet kéréséhez, amelyet visszaküld az alkalmazásnak, és továbbítja azokat a központba és a mobileszközön.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/diagram.png)

Az Arduino-vázlat egy eseményt küld az Azure IoT Hubnak. Ez az esemény elindítja a Azure Functions alkalmazást. A Azure Functions alkalmazás tartalmazza a Twitter API-hoz való kapcsolódáshoz és Tweet beolvasásához szükséges logikát. Ezután becsomagolja a tweet szövegét egy C2D (felhőből eszközre), és visszaküldi az eszközre.

## <a name="optional-use-your-own-twitter-bearer-token"></a>Opcionális: saját Twitter-tulajdonosi token használata

Tesztelési célból a minta projekt előre konfigurált Twitter-tulajdonosi jogkivonatot használ. Minden Twitter-fiók esetében azonban van egy [díjszabási korlát](https://dev.twitter.com/rest/reference/get/search/tweets) . Ha saját tokent szeretne használni, kövesse az alábbi lépéseket:

1. Lépjen a [Twitter fejlesztői portálra](https://dev.twitter.com/) egy új Twitter-alkalmazás regisztrálásához.

2. Az alkalmazás [fogyasztói kulcsának és fogyasztói titkainak beszerzése](https://support.yapsody.com/hc/en-us/articles/360003291573-How-do-I-get-a-Twitter-Consumer-Key-and-Consumer-Secret-key-) .

3. [Néhány segédprogrammal](https://gearside.com/nebula/utilities/twitter-bearer-token-generator/) hozhatja elő a Twitter-tulajdonosi tokent a két kulcsból.

4. A (z) {: Target = "_blank"} [Azure Portalban](https://portal.azure.com/)szerezze be az **erőforráscsoportot** , és keresse meg az Azure-függvényt (típus: app Service) a "Shake, Shake" projekthez. A név mindig tartalmazza a "Shake..." karakterlánc.

   ![A Azure Portal képernyőképe a projekthez tartozó app Service-t jeleníti meg.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function.png)

5. Frissítse a `run.csx` **functions > shakeshake-cs** és a saját jogkivonatát tartalmazó kódot:

   ```csharp
   string authHeader = "Bearer " + "[your own token]";
   ```
  
   ![Képernyőfelvétel: a függvény C# kódját jeleníti meg, ahol megadhatja a tokent.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/twitter-token.png)

6. Mentse a fájlt, és kattintson a **Futtatás** gombra.

## <a name="problems-and-feedback"></a>Problémák és visszajelzés

Problémák elhárítása vagy visszajelzés küldése. 

### <a name="problems"></a>Problémák

Egy probléma, amelyet láthat, ha a képernyő "nem tweetek" feliratot jelenít meg, miközben minden lépés sikeresen futott. Ez az állapot általában akkor fordul elő, amikor először telepíti és futtatja a mintát, mivel a Function app-nak egy percen belül bárhol kell lennie, hogy az alkalmazásból csak egy percet kelljen elindítani. 

Vagy a kód futtatásakor az alkalmazás újraindítását okozó visszavert jel is van. Ha ez történik, az eszközhöz tartozó alkalmazás időtúllépést kaphat a tweet beolvasásához. Ebben az esetben a következő módszerek valamelyikével próbálkozhat a probléma megoldásához:

1. Kattintson a fejlesztői készlet alaphelyzetbe állítás gombjára az eszköz alkalmazás újbóli futtatásához.

2. A [Azure Portal](https://portal.azure.com/)keresse meg a létrehozott Azure functions alkalmazást, és indítsa újra:

   ![Képernyőfelvétel: a Azure Portal Azure Functions alkalmazással és az újraindítás gombbal jeleníthető meg.](media/iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message/azure-function-restart.png)

Ha más problémákat tapasztal, tekintse meg a [IoT fejlesztői készlet GYIK](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) -et, vagy vegye fel velünk a kapcsolatot az alábbi csatornákon keresztül:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan csatlakoztatható egy fejlesztői készlet-eszköz az Azure IoT-alapú távoli figyelési megoldás-gyorsító szolgáltatáshoz, és hogyan kérhet le egy tweetet, a következő lépéseket ajánljuk:

* [Az Azure IoT távoli monitorozási megoldásának gyorsítása – áttekintés](/azure/iot-suite/)