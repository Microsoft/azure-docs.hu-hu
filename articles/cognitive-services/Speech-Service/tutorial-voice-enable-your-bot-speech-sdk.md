---
title: 'Oktatóanyag: hangok engedélyezése a robotnak a Speech SDK – Speech Service használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban egy echo-robotot hoz létre a Microsoft bot Framework használatával, üzembe helyezi az Azure-ban, és regisztrálja azt a robot Framework Direct line Speech Channel szolgáltatással. Ezután konfigurálhat egy Windows rendszerbeli ügyfélalkalmazás-alkalmazást, amely lehetővé teszi, hogy beszéljen a robothoz, és meghallgassa, hogy válaszoljon vissza Önnek.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fa449ad3d9a0e26bd0754a67581c8d63fa025e55
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552308"
---
# <a name="tutorial-voice-enable-your-bot-using-the-speech-sdk"></a>Oktatóanyag: hang – a robot engedélyezése a Speech SDK használatával

A beszédfelismerési szolgáltatással hangvezérelt csevegést használhat.

Ebben az oktatóanyagban egy olyan robotot hoz létre, amely megismétli, hogy mit mond.
A robotot a Microsoft bot Framework használatával hozza létre, majd az Azure-ba helyezi üzembe, és regisztrálja a robot Framework Direct line Speech Channel szolgáltatással.
Ezután konfiguráljon egy Windows rendszerbeli ügyfélalkalmazás-alkalmazást, amely lehetővé teszi, hogy beszéljen a robotról, és hallgassa meg a véleményét.

Ez az oktatóanyag olyan fejlesztők számára készült, akik Újdonságok az Azure-ban, a bot Framework bots, a közvetlen vonalas beszéd vagy a Speech SDK, és gyors, korlátozott kódolású munkarendszert szeretne létrehozni. Ezeknek a szolgáltatásoknak nem szükséges tapasztalata vagy ismerete.

Az oktatóanyagban elvégezhető hang-kompatibilis csevegési robot a következő lépéseket követi:

1. A minta ügyfélalkalmazás úgy van konfigurálva, hogy a Direct line Speech Channelhez és az ECHO Robothoz kapcsolódjon.
1. Amikor a felhasználó megnyomja a gombot, hang-adatfolyamokat a mikrofonból. (Vagy a rendszer folyamatosan rögzíti a hangot, ha egyéni kulcsszó van használatban.)
1. Ha egyéni kulcsszót használ, a Kulcsszóválasztó észlelés a helyi eszközön történik, és a kapuzás a felhőbe áramlik.
1. A Speech SDK használatával a minta ügyfélalkalmazás a Direct line Speech Channel és a Streams hanghoz csatlakozik.
1. Opcionálisan a szolgáltatásnál nagyobb pontosságú kulcsszó-ellenőrzés történik.
1. A rendszer átadja a hangot a beszédfelismerési szolgáltatásnak, és szöveget ad át.
1. A rendszer a felismert szöveget robot-keretrendszerbeli tevékenységként továbbítja az ECHO robotnak.
1. A szöveg-beszéd (TTS) szolgáltatás a válaszüzenetet a hanganyagba helyezi, és a lejátszáshoz az ügyfélalkalmazás felé áramlik.

<!-- svg src in User Story 1754106 -->
![diagram – címke](media/tutorial-voice-enable-your-bot-speech-sdk/diagram.png "A beszédfelismerési csatorna folyamata")

> [!NOTE]
> Az oktatóanyag lépései nem igényelnek fizetős szolgáltatást. Új Azure-felhasználóként használhat krediteket az ingyenes Azure próbaverziós előfizetésből, és a beszédfelismerési szolgáltatás ingyenes szintjéről is elvégezheti ezt az oktatóanyagot.

Az oktatóanyag a következőket ismerteti:
> [!div class="checklist"]
> * Új Azure-erőforrások létrehozása
> * Az ECHO-robot minta létrehozása, tesztelése és üzembe helyezése egy Azure App Service
> * A robot regisztrálása közvetlen vonalas beszédfelismerési csatornával
> * A Windows Voice Assistant-ügyfél létrehozása és futtatása az ECHO-robottal való kommunikációhoz
> * Egyéni kulcsszó aktiválásának hozzáadása
> * Megtudhatja, hogyan módosíthatja a felismert és beszélt beszéd nyelvét

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőket kell elvégeznie:

- Windows 10 rendszerű számítógép, amely egy működő mikrofonnal és beszélővel (vagy fejhallgatóval) rendelkezik
- A [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) -es vagy újabb verziója, amelyen telepítve van a **ASP.net és a webes fejlesztési** számítási feladat
- [.NET-keretrendszer futtatókörnyezete 4.6.1](https://dotnet.microsoft.com/download) vagy újabb
- Egy Azure-fiók. [Regisztráljon ingyen](https://azure.microsoft.com/free/cognitive-services/)
- [GitHub](https://github.com/)-fiók
- [Git for Windows](https://git-scm.com/download/win)

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az oktatóanyagban létrehozandó ügyfélalkalmazás néhány Azure-szolgáltatást használ. Ha csökkenteni szeretné a robottól érkező válaszok időkorlátját, győződjön meg arról, hogy ezek a szolgáltatások ugyanabban az Azure-régióban találhatók. Ebben a szakaszban egy erőforráscsoportot hoz létre az **USA nyugati** régiójában. Ezt az erőforráscsoportot a bot Framework, a Direct line Speech Channel és a Speech Service egyedi erőforrásainak létrehozásakor fogjuk használni.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.ResourceGroup" target="_blank">Erőforráscsoport létrehozása </a>
1. A rendszer a következő információk megadását kéri:
   * **Előfizetés** beállítása az **ingyenes próbaverzióra** (meglévő előfizetést is használhat).
   * Adja meg az **erőforráscsoport** nevét. Javasoljuk, hogy **SpeechEchoBotTutorial-ResourceGroup**.
   * A régió legördülő menüben válassza az **USA nyugati** **régiója** lehetőséget.
1. Kattintson az **Áttekintés és létrehozás** elemre. Ekkor meg kell jelennie egy olyan szalagcímnek, amely beolvasta az **érvényesítést**.
1. Kattintson a **Létrehozás** lehetőségre. Az erőforráscsoport létrehozása eltarthat néhány percig.
1. Az oktatóanyag későbbi részében létrehozott erőforrásokhoz hasonlóan érdemes ezt az erőforráscsoportot az irányítópultra rögzíteni az egyszerű hozzáférés érdekében. Ha rögzíteni szeretné ezt az erőforráscsoportot, kattintson az erőforráscsoport nevének jobb oldalán lévő rögzítés ikonra.

### <a name="choosing-an-azure-region"></a>Azure-régió kiválasztása

Ha más régiót szeretne használni az oktatóanyaghoz, akkor ezek a tényezők korlátozhatják a lehetséges lehetőségeket:

* Győződjön meg arról, hogy [támogatott Azure-régiót](regions.md#voice-assistants)használ.
* A Direct line Speech Channel a szöveg-beszéd szolgáltatást használja, amely neurális és standard hangokat tartalmaz. Ezekben az [Azure-régiókban](regions.md#neural-and-standard-voices)mind a neurális, mind a standard hangok elérhetők.

A régiókkal kapcsolatos további információkért lásd: Azure-beli [telephelyek](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="create-resources"></a>Az erőforrások létrehozása

Most, hogy már van egy erőforráscsoport egy támogatott régióban, a következő lépés az oktatóanyagban használni kívánt egyes szolgáltatások egyedi erőforrásainak létrehozása.

### <a name="create-a-speech-service-resource"></a>Speech szolgáltatás erőforrásának létrehozása

A beszédfelismerési erőforrások létrehozásához kövesse az alábbi utasításokat:

1. <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Beszédfelismerési szolgáltatás erőforrásának létrehozása </a>
4. A rendszer a következő információk megadását kéri:
   * Adjon **nevet** az erőforrásnak. Javasoljuk, hogy **SpeechEchoBotTutorial**
   * **Előfizetés** esetén győződjön meg arról, hogy az **ingyenes próbaverzió** van kiválasztva.
   * A hely mezőben válassza az **USA nyugati** **régiója** lehetőséget.
   * A **díjszabási** szinten válassza a **F0** lehetőséget. Ez az ingyenes szintet.
   * Az **erőforráscsoport** területen válassza a **SpeechEchoBotTutorial-ResourceGroup** elemet.
5. Miután megadta az összes szükséges információt, kattintson a **Létrehozás** gombra. Az erőforrás létrehozása néhány percet is igénybe vehet.
6. Az oktatóanyag későbbi részében szüksége lesz a szolgáltatáshoz tartozó előfizetési kulcsokra. Ezeket a kulcsokat bármikor elérheti az erőforrás- **Áttekintés** (kulcsok kezelése) vagy a **kulcsok** használatával.

Ezen a ponton győződjön meg arról, hogy az erőforráscsoport (**SpeechEchoBotTutorial-ResourceGroup**) rendelkezik beszédfelismerési erőforrással:

| Név | Típus  | Hely |
|------|-------|----------|
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA nyugati régiója |

### <a name="create-an-azure-app-service-plan"></a>Azure App Service-csomag létrehozása

A következő lépés egy App Service terv létrehozása. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.AppServicePlanCreate" target="_blank">Azure App Service terv létrehozása </a>
4. A rendszer a következő információk megadását kéri:
   * **Előfizetés** beállítása az **ingyenes próbaverzióra** (meglévő előfizetést is használhat).
   * Az **erőforráscsoport** területen válassza a **SpeechEchoBotTutorial-ResourceGroup** elemet.
   * Adjon **nevet** az erőforrásnak. Javasoljuk, hogy **SpeechEchoBotTutorial-AppServicePlan**
   * **Operációs rendszer** esetén válassza a **Windows** lehetőséget.
   * A régió területen válassza az **USA nyugati** **régiója** lehetőséget.
   * Az **árképzési szint** esetében ellenőrizze, hogy a **standard S1** van-e kiválasztva. Ennek az alapértelmezett értéknek kell lennie. Ha nem, ügyeljen arra, hogy az operációs rendszert a fent ismertetett módon állítsa be a **Windows** **rendszerre** .
5. Kattintson az **Áttekintés és létrehozás** elemre. Ekkor meg kell jelennie egy olyan szalagcímnek, amely beolvasta az **érvényesítést**.
6. Kattintson a **Létrehozás** lehetőségre. Az erőforráscsoport létrehozása eltarthat néhány percig.

Ezen a ponton győződjön meg arról, hogy az erőforráscsoport (**SpeechEchoBotTutorial-ResourceGroup**) két erőforrással rendelkezik:

| Név | Típus  | Hely |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service Plan (App Service-csomag) | USA nyugati régiója |
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA nyugati régiója |

## <a name="build-an-echo-bot"></a>Visszhangos robot létrehozása

Most, hogy létrehozott néhány erőforrást, hozzon létre egy robotot. Kezdjük az ECHO bot-mintával, amelynek a neve azt jelenti, hogy a válaszként beírt szöveget is megismétli. Ne aggódjon, a mintakód készen áll arra, hogy módosítás nélkül használhassa. Úgy van konfigurálva, hogy működjön együtt a közvetlen vonalas hangcsatornával, amelyet a robot Azure-ba való üzembe helyezése után fogunk csatlakozni.

> [!NOTE]
> Az alábbi utasítások, valamint az ECHO robottal kapcsolatos további információk a [minta readme on githubon](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/02.echo-bot/README.md)érhetők el.

### <a name="run-the-bot-sample-on-your-machine"></a>A robot minta futtatása a gépen

1. A minták tárházának klónozása:

   ```bash
   git clone https://github.com/Microsoft/botbuilder-samples.git
   ```

2. Indítsa el a Visual studiót.
3. Az eszköztáron válassza a **fájl**  >  **nyitott**  >  **projekt/megoldás** lehetőséget, majd nyissa meg az ECHO robot Project megoldást:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

4. A projekt betöltését követően nyomja le az <kbd>F5</kbd> billentyűt a projekt létrehozásához és futtatásához.
5. Egy böngésző elindul, és egy ehhez hasonló képernyő jelenik meg.
    > [!div class="mx-imgBorder"]
    > [![A képernyőképen megjelenik a EchoBot lap, amelyen a robotja elkészült.](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png "A localhost-on futó EchoBot")](media/tutorial-voice-enable-your-bot-speech-sdk/echobot-running-on-localhost.png#lightbox)

### <a name="test-the-bot-sample-with-the-bot-framework-emulator"></a>Tesztelje a bot-mintát a bot Framework emulátorával

A [bot Framework Emulator](https://github.com/microsoft/botframework-emulator) egy asztali alkalmazás, amely lehetővé teszi a robot-fejlesztők számára a robotok helyi tesztelését és hibakeresését (vagy egy alagúton keresztül távolról). Az emulátor begépelt szöveget fogad bemenetként (nem hangként). A robot szintén válaszol a szövegre. Kövesse az alábbi lépéseket a bot Framework Emulator használatával a helyileg futtatott echo-robot teszteléséhez, szöveges bevitelsel és szöveges kimenettel. Miután üzembe helyezte a robotot az Azure-ban, hangbemenettel és hangkimenettel teszteljük.

1. A [bot Framework Emulator](https://github.com/Microsoft/BotFramework-Emulator/releases/latest) 4.3.0 vagy újabb verziójának telepítése
2. Indítsa el a robot Framework emulátort, és nyissa meg a robotját:
   * **Fájl**  ->  **Nyissa meg a robotot**.
3. Adja meg a robot URL-címét. Például:

   ```
   http://localhost:3978/api/messages
   ```
   kattintson a "kapcsolat" gombra.
4. A robotnak a "Hello and Welcome!" kifejezéssel kell köszönteni. üzenetet küldi vissza. Írjon be bármilyen szöveges üzenetet, és erősítse meg, hogy választ kap a robottól.
5. Az ECHO bot-példánnyal folytatott kommunikáció cseréje a következőhöz hasonló lehet: [ ![képernyőfelvétel a bot Framework emulatort mutatja be.](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png "Bot Framework emulátor")](media/tutorial-voice-enable-your-bot-speech-sdk/bot-framework-emulator.png#lightbox)

## <a name="deploy-your-bot-to-an-azure-app-service"></a>A robot üzembe helyezése egy Azure App Service

A következő lépés az ECHO-robot üzembe helyezése az Azure-ban. A robot üzembe helyezésének néhány módja van, de ebben az oktatóanyagban a Visual studióból való közzétételre fogunk összpontosítani.

> [!NOTE]
> Azt is megteheti, hogy az [Azure CLI](/azure/bot-service/bot-builder-deploy-az-cli) és a [telepítési sablonok](https://github.com/microsoft/BotBuilder-Samples/tree/master/samples/csharp_dotnetcore/adaptive-dialog/03.core-bot)használatával is üzembe helyez egy robotot.

> [!NOTE]
> Ha a **Közzététel...** nem jelenik meg az alábbi lépések elvégzése során, a Visual Studio telepítőjének használatával adja hozzá a **ASP.net és a webes fejlesztési** számítási feladatot.

1. A Visual studióból nyissa meg a Direct line Speech Channel használatára konfigurált echo-robotot:

   ```
   samples\csharp_dotnetcore\02.echo-bot\EchoBot.sln
   ```

1. A **megoldáskezelő** kattintson a jobb gombbal a **EchoBot** projektre, és válassza a **Közzététel...** lehetőséget.
1. Megnyílik egy új, **Közzététel** címmel ellátható ablak.
1. Válassza az **Azure** lehetőséget, kattintson a **tovább** gombra, válassza **Azure app Service (Windows)**, kattintson a **tovább** gombra, majd kattintson az **új Azure app Service létrehozása...** a zöld pluszjelre.
1. Amikor megjelenik a **app Service (Windows)** ablak:
   * Kattintson a **fiók hozzáadása** lehetőségre, és jelentkezzen be az Azure-fiókja hitelesítő adataival. Ha már bejelentkezett, válassza ki a kívánt fiókot a legördülő listából.
   * A **név** mezőben meg kell adnia a robot globálisan egyedi nevét. A rendszer ezt a nevet használja egy egyedi robot URL-cím létrehozásához. A rendszer az alapértelmezett értéket adja meg, beleértve a dátumot és az időt (például: "EchoBot20190805125647"). Használhatja az oktatóanyag alapértelmezett nevét.
   * Az **előfizetés** beállításnál állítsa az **ingyenes próbaverzióra**
   * **Erőforráscsoport** esetében válassza az **SpeechEchoBotTutorial-ResourceGroup** elemet.
   * Az **üzemeltetési csomag** esetében válassza az **SpeechEchoBotTutorial-AppServicePlan**
1. Kattintson a **Létrehozás** lehetőségre. A varázsló utolsó képernyőjén kattintson a **Befejezés** gombra.
1. A közzétételi képernyő jobb oldalán kattintson a **publish (közzététel** ) elemre. A Visual Studio üzembe helyezi a robotot az Azure-ban.
1. A Visual Studio kimeneti ablakában a következőhöz hasonló üzenetnek kell megjelennie:

   ```
   Publish Succeeded.
   Web App was published successfully https://EchoBot20190805125647.azurewebsites.net/
   ```

1. Az alapértelmezett böngészőnek meg kell nyitnia és meg kell jelennie az alábbi beolvasott oldalnak: "a robot elkészült!".
1. Ezen a ponton ellenőrizze, hogy az erőforráscsoport **SpeechEchoBotTutorial-ResourceGroup szerepel-** e a Azure Portalban, és erősítse meg a következő három erőforrást:

| Név | Típus  | Hely |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA nyugati régiója |
| SpeechEchoBotTutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA nyugati régiója |

## <a name="enable-web-sockets"></a>Webes szoftvercsatornák engedélyezése

Egy kis méretű konfigurációs módosítást kell végeznie, hogy a robot a webes szoftvercsatornák használatával kommunikáljon a közvetlen vonalas beszéd csatornával. A websocketek engedélyezéséhez kövesse az alábbi lépéseket:

1. Navigáljon a [Azure Portal](https://portal.azure.com), és kattintson a app Servicera. Az erőforrásnak a **EchoBot20190805125647** (egyedi alkalmazásnév) hasonló névvel kell rendelkeznie.
2. A bal oldali navigációs ablaktábla **Beállítások** területén kattintson a **konfiguráció** elemre.
3. Válassza az **általános beállítások** lapot.
4. Keresse meg a **webes szoftvercsatornák** váltógomb helyét, és állítsa **be a következőre:.**
5. Kattintson a **Mentés** gombra.

> [!TIP]
> A szolgáltatás leállításához vagy újraindításához használhatja a Azure App Service lap tetején látható vezérlőket. Ez a hibaelhárítás során hasznos lehet.

## <a name="create-a-channel-registration"></a>Csatorna regisztrációjának létrehozása

Most, hogy létrehozott egy Azure App Service a robot üzemeltetéséhez, a következő lépés egy **robot-csatornák regisztrációjának** létrehozása. A csatorna regisztrálásának előfeltétele, hogy regisztrálja a robotot a bot Framework csatornákon, beleértve a közvetlen vonalas hangcsatornát. Ha többet szeretne megtudni arról, hogy a robotok hogyan használják a csatornákat, tekintse meg [a bot összekapcsolását a csatornákhoz](/azure/bot-service/bot-service-manage-channels)című témakört.

1. <a href="https://ms.portal.azure.com/#create/Microsoft.BotServiceConnectivityGalleryPackage" target="_blank">Azure bot channels-regisztráció létrehozása </a>
2. A rendszer a következő információk megadását kéri:
   * A **bot Handle** esetében írja be a következőt: **SpeechEchoBotTutorial-BotRegistration-# # # #** , és cserélje le **####** a-t a tetszőleges számú elemre. Vegye figyelembe, hogy a robot-leírónak globálisan egyedinek kell lennie. Ha egy robot-leírót ad meg, de hibaüzenetet kap, _a kért bot-azonosító nem érhető el_, válasszon másik számot. Az alábbi példákban a 8726-et használtuk
   * Az **előfizetés** mezőben válassza az **ingyenes próbaverzió** lehetőséget.
   * Az **erőforráscsoport** területen válassza a **SpeechEchoBotTutorial-ResourceGroup** elemet.
   * A hely mezőben válassza az **USA nyugati** **régiója** lehetőséget.
     * A **díjszabási** szinten válassza a **F0** lehetőséget.
     * Az **üzenetkezelési végpontnál** adja meg a webalkalmazás URL-címét a `/api/messages` végén található elérési úttal. Például: Ha a globálisan egyedi alkalmazás neve **EchoBot20190805125647** volt, az üzenetkezelési végpont a következő lesz: `https://EchoBot20190805125647.azurewebsites.net/api/messages/` .
     * Az **Application ininsights** esetében ezt **kikapcsolhatja**. További információ: [bot Analytics](/azure/bot-service/bot-service-manage-analytics).
     * **Az alkalmazás azonosítójának és jelszavának automatikus létrehozása** figyelmen kívül hagyva.
5. A **bot channels-regisztráció** paneljének alján kattintson a **Létrehozás** gombra.

Ekkor a Azure Portalban keresse meg az **SpeechEchoBotTutorial-ResourceGroup** erőforráscsoportot. Ekkor legalább négy erőforrást kell megjelenítenie:

| Név | Típus  | Hely |
|------|-------|----------|
| EchoBot20190805125647 | App Service | USA nyugati régiója |
| SpeechEchoBotTutorial-AppServicePlan | App Service-csomag | USA nyugati régiója |
| SpeechEchoBotTutorial-BotRegistration-8726 | Robot-csatornák regisztrálása | Globális |
| SpeechEchoBotTutorial-Speech | Cognitive Services | USA nyugati régiója |

> [!IMPORTANT]
> A bot channels regisztrációs erőforrása akkor is megjelenik a globális régióban, ha az USA nyugati régióját választotta. Ez a várható eredmény.

## <a name="optional-test-in-web-chat"></a>Nem kötelező: teszt a webes csevegésben

Az Azure bot channels regisztrációs oldalán a **robot felügyelete** alatt **webes csevegési** lehetőség van. Alapértelmezés szerint nem fog működni a robotban, mivel a webes csevegésnek hitelesítenie kell a roboton. Ha a telepített robotot szövegbeviteli bemenettel szeretné tesztelni, kövesse az alábbi lépéseket. Vegye figyelembe, hogy ezek a lépések nem kötelezőek, és nem szükségesek az oktatóanyag következő lépéseinek folytatásához. 

1. Keresse meg és nyissa meg a **EchoBotTutorial-BotRegistration-#** # # # erőforrást a [Azure Portal](https://portal.azure.com)
1. A **robot kezelése** navigációs sávon válassza a **Beállítások** lehetőséget. Az érték másolása a **Microsoft app ID** alatt
1. Nyissa meg a Visual Studio EchoBot megoldást. A megoldás Explorerben keresse meg és kattintson duplán a **appsettings.js** elemre.
1. Cserélje le az üres karakterláncot a **MicrosoftAppId** mellett a JSON-fájlban a másolt azonosító értékkel.
1. Visszakapott a Azure Portalba, a **bot Management** navigáció területén válassza a **Beállítások** lehetőséget, majd kattintson a **Microsoft app ID** melletti **(kezelés)** elemre.
1. Kattintson az **új ügyfél titkára**. Adjon hozzá egy leírást (például "Web Chat"), majd kattintson a **Hozzáadás** gombra. Az új titok másolása
1. Cserélje le az üres karakterláncot a **MicrosoftAppPassword** mellett a JSON-fájlban a másolt titkos értékkel.
1. Mentse a JSON-fájlt. Ennek az alábbihoz hasonlónak kell lennie:
```json
{
  "MicrosoftAppId": "3be0abc2-ca07-475e-b6c3-90c4476c4370",
  "MicrosoftAppPassword": "-zRhJZ~1cnc7ZIlj4Qozs_eKN.8Cq~U38G"
}
```
9. Tegye közzé újra az alkalmazást (kattintson a jobb gombbal a **EchoBot** projektre a Visual Studio Solution Explorerben, válassza a **Közzététel...** lehetőséget, majd kattintson a **Közzététel** gombra)
10. Most már készen áll arra, hogy tesztelje a robotot a webes csevegésben!

## <a name="register-the-direct-line-speech-channel"></a>A közvetlen vonalas beszéd csatorna regisztrálása

Itt az ideje, hogy regisztrálja a robotot a közvetlen vonalas beszéd csatornával. Ez a csatorna létrehoz egy kapcsolatot a robotja és egy, a Speech SDK-val lefordított ügyfélalkalmazás között.

1. Keresse meg és nyissa meg a **SpeechEchoBotTutorial-BotRegistration-#** # # # erőforrást a [Azure Portalban](https://portal.azure.com).
1. A **robot kezelése** navigációs sávon válassza a **csatornák** lehetőséget.
   * A **további csatornák** alatt kattintson a **közvetlen vonalas beszéd** elemre.
   * Tekintse át a **közvetlen sortörés beállítása** című oldalon található szöveget, majd bontsa ki a **kognitív szolgáltatás fiókja** legördülő menüt.
   * Válassza ki a korábban létrehozott Speech-erőforrást (pl. **SpeechEchoBotTutorial-Speech**) a menüből, hogy a robotot a beszédfelismerési előfizetési kulcshoz rendelje.
   * Hagyja figyelmen kívül a választható mezők hátralévő részét.
   * Kattintson a **Mentés** gombra.

1. A **bot Management** navigációs ablaktáblán kattintson a **Beállítások** elemre.
   * Jelölje be a **streaming Endpoint engedélyezése** feliratú jelölőnégyzetet. Erre azért van szükség, hogy létrehozzon egy webes szoftvercsatornára épülő kommunikációs protokollt a robot és a közvetlen vonalas beszédfelismerési csatorna között.
   * Kattintson a **Mentés** gombra.

> [!TIP]
> Ha további információra van szüksége, tekintse meg [a bot összekapcsolását](/azure/bot-service/bot-service-channel-connect-directlinespeech)ismertető témakört. Ez az oldal további információkat és ismert problémákat tartalmaz.

## <a name="run-the-windows-voice-assistant-client"></a>A Windows Voice Assistant-ügyfél futtatása

Ebben a lépésben a Windows Voice Assistant-ügyfelet fogja futtatni. Az ügyfél egy Windows megjelenítési alaprendszer (WPF) alkalmazás a C#-ban, amely a [SPEECH SDK](./speech-sdk.md) használatával kezeli a robottal folytatott kommunikációt a közvetlen vonalas beszéd csatornán keresztül. Használhatja a robot használatát és tesztelését az egyéni ügyfélalkalmazás írása előtt. Nyílt forráskódú, így letöltheti a végrehajtható fájlt, és futtathatja, vagy felépítheti saját maga is.

A Windows Voice Assistant-ügyfél egy egyszerű felhasználói felülettel rendelkezik, amely lehetővé teszi a robothoz való kapcsolódás konfigurálását, a szöveges beszélgetés megtekintését, a bot Framework-tevékenységek megtekintését JSON formátumban, valamint az adaptív kártyák megjelenítését. Emellett támogatja az egyéni kulcsszavak használatát is. Ezt az ügyfelet fogja használni a robottal való kommunikációhoz és a Hangválaszok fogadásához.

> [!NOTE]
> Ezen a ponton ellenőrizze, hogy a mikrofon és a hangszórók engedélyezve vannak-e és működnek-e.

1. Navigáljon a [Windows Voice Assistant-ügyfél](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/README.md)GitHub-tárházához.
1. Kövesse az itt megadott utasításokat
   * egy előre elkészített végrehajtható fájl letöltése egy ZIP-csomagban a futtatáshoz, vagy
   * hozza létre a végrehajtható fájlt a tárház klónozásával és a projekt létrehozásával.

1. Indítsa el az `VoiceAssistantClient.exe` ügyfélalkalmazás, és konfigurálja úgy, hogy a robothoz kapcsolódjon, a GitHub-adattár utasításait követve.
1. Kattintson az **Újrakapcsolódás** lehetőségre, és győződjön meg arról, hogy az **új beszélgetés elindult – típus vagy a mikrofon gomb megnyomása**.
1. Tesztelje. Kattintson a mikrofon gombra, és beszéljen néhány szót angolul. A felismert szöveg a következőképpen jelenik meg:. Ha elkészült, a robot a saját hangján válaszol, amely az "Echo" szót követi, amelyet a felismert szavak követnek.
1. Szöveget is használhat a robottal való kommunikációhoz. Csak írja be a szöveget az alsó sávban. 

### <a name="troubleshooting-errors-in-windows-voice-assistant-client"></a>Hibaelhárítási hibák a Windows Voice Assistant-ügyfélben

Ha hibaüzenetet kap a fő alkalmazás ablakában, a következő táblázat segítségével azonosíthatja és elháríthatja a hibát:

| Hiba | Mi a teendője? |
|-------|----------------------|
|Hiba (AuthenticationFailure): a WebSocket frissítése hitelesítési hiba miatt meghiúsult (401). A megfelelő előfizetési kulcs (vagy engedélyezési jogkivonat) és a régió nevének keresése| Az alkalmazás beállítások lapján ellenőrizze, hogy helyesen adta-e meg a beszédfelismerési előfizetési kulcsot és annak régióját.<br>Győződjön meg arról, hogy helyesen adta meg a beszédfelismerési kulcsot és a kulcsfontosságú régiót. |
|Hiba (ConnectionFailure): a távoli állomás lezárta a kapcsolatokat. Hibakód: 1011. Hiba részletei: az üzenet elküldése előtt nem sikerült csatlakozni a robothoz | Győződjön meg arról, hogy [bejelölte az "adatfolyam-végpont engedélyezése"](#register-the-direct-line-speech-channel) és/vagy a [ **webes szoftvercsatornák**](#enable-web-sockets) bekapcsolva beállítást.<br>Győződjön meg arról, hogy a Azure App Service fut. Ha igen, próbálja meg újraindítani a App Service.|
|Hiba (ConnectionFailure): a távoli állomás lezárta a kapcsolatokat. Hibakód: 1002. Hiba részletei: a kiszolgáló a (z) 503 állapotkódot adta vissza, amikor a rendszer a (z) 101 állapotkódot várta. | Győződjön meg arról, hogy [bejelölte az "adatfolyam-végpont engedélyezése"](#register-the-direct-line-speech-channel) és/vagy a [ **webes szoftvercsatornák**](#enable-web-sockets) bekapcsolva beállítást.<br>Győződjön meg arról, hogy a Azure App Service fut. Ha igen, próbálja meg újraindítani a App Service.|
|Hiba (ConnectionFailure): a távoli állomás lezárta a kapcsolatokat. Hibakód: 1011. Hiba részletei: a válasz állapotkód nem a sikerre utal: 500 (InternalServerError)| A robot a kimeneti tevékenység [beszéd mezőjében](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) egy neurális hangot adott meg, de az előfizetési kulcshoz társított Azure-régió nem támogatja a neurális hangokat. Lásd: [neurális és standard hangok](./regions.md#neural-and-standard-voices).|

Ha a probléma nem szerepel a táblázatban, olvassa el a következő témakört [: hangsegédek: gyakori kérdések](faq-voice-assistants.md). Ha az oktatóanyag lépéseinek követése után továbbra sem tudja megoldani a problémát, adjon meg egy új problémát a  [Hangsegéd GitHub lapján](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/issues).

#### <a name="a-note-on-connection-time-out"></a>A kapcsolatok időtúllépésére vonatkozó Megjegyzés

Ha egy robothoz csatlakozik, és az elmúlt 5 percben nem történt tevékenység, akkor a szolgáltatás automatikusan lezárta a WebSocket-kapcsolatot az ügyféllel és a robottal. Ez az elvárt működés. Egy üzenet jelenik meg az alsó sávban: *"az aktív kapcsolat időtúllépés miatt megszakadt, de készen áll az igény szerinti újrakapcsolódásra"*. Nem kell megnyomnia az "Újrakapcsolódás" gombot – egyszerűen nyomja meg a mikrofon gombot, és írjon be egy szöveges üzenetet, vagy adja meg a kulcsszót (ha van ilyen). A rendszer automatikusan újrakezdi a kapcsolatokat.  
### <a name="view-bot-activities"></a>Bot-tevékenységek megtekintése

Minden robot küldi és fogadja a **tevékenységek** üzeneteit. A Windows Voice Assistant-ügyfél **tevékenység napló** ablakában az időbélyeggel ellátott naplókat láthatja az ügyfél által a robottól kapott tevékenységekről. Azt is megtekintheti, hogy az ügyfél milyen tevékenységeket küldhet a robotnak a [`DialogServiceConnector.SendActivityAsync`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.sendactivityasync)  metódus használatával. Amikor kijelöl egy naplóbejegyzést, a rendszer a társított tevékenység részleteit JSON-ként jeleníti meg.

Íme egy példa egy, az ügyfél által fogadott tevékenység JSON-fájlja:

```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration-8726"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

Ha többet szeretne megtudni a JSON-kimenetben visszaadott információkról, tekintse meg [a tevékenységek mezőit](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). Ebben az oktatóanyagban a [szöveg](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text) és a [beszéd](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) mezőkre koncentrálhat.

### <a name="view-client-source-code-for-calls-to-the-speech-sdk"></a>Az ügyfél forráskódjának megtekintése a Speech SDK-hívásokhoz

A Windows Voice Assistant-ügyfél a [Microsoft. CognitiveServices. Speech](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/)NuGet-csomagot használja, amely a Speech SDK-t tartalmazza. A mintakód áttekintésének kiindulópontja a InitSpeechConnector () metódusa [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) , amely a következő két SPEECH SDK-objektumot hozza létre:
- [`DialogServiceConfig`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconfig) – A konfigurációs beállításokhoz (például: beszéd előfizetési kulcs, fő régió)
- [`DialogServiceConnector`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.-ctor) – A Channel-kapcsolatok és az ügyfél-előfizetési események kezelése a felismert beszéd-és bot-válaszok kezeléséhez.

## <a name="add-custom-keyword-activation"></a>Egyéni kulcsszó aktiválásának hozzáadása

A Speech SDK támogatja az egyéni kulcsszó-aktiválást. A Microsoft Segédhez hasonlóan a "Hey Cortana" is megírhat egy alkalmazást, amely folyamatosan figyeli a kívánt kulcsszót. Ne feledje, hogy a kulcsszó lehet egyetlen szó vagy egy többszavas kifejezés.

> [!NOTE]
> A *kulcsszó* kifejezés gyakran a *Wake Word* kifejezéssel való szinonimaként használatos, és a Microsoft dokumentációjában is látható.

A kulcsszavak észlelése az ügyfélalkalmazás alapján történik. Ha kulcsszót használ, a rendszer csak akkor továbbítja a hanganyagot a közvetlen vonalas csatornára, ha a rendszer a kulcsszót észleli. A közvetlen vonalas hangcsatorna tartalmaz egy kulcsszó- *ellenőrzés (KWV)* nevű összetevőt, amely összetettebb feldolgozást végez a felhőben annak ellenőrzéséhez, hogy a kiválasztott kulcsszó a hangadatfolyam elején van-e. Ha a Key Word ellenőrzése sikeres, akkor a csatorna kommunikálni fog a robottal.

Kövesse az alábbi lépéseket egy kulcsszó-modell létrehozásához, konfigurálja a Windows Voice Assistant-ügyfelet a modell használatára, és végül tesztelje a robottal.

1. Kövesse ezeket az utasításokat [egy egyéni kulcsszó létrehozásához a Speech Service használatával](./custom-keyword-basics.md).
2. Bontsa ki az előző lépésben letöltött modell fájlt. A kulcsszó neve a következő lehet:. Egy nevű fájlt keres `kws.table` .
3. A Windows Voice Assistant-ügyfélben keresse meg a **Beállítások** menüt (keresse meg a fogaskerék ikont a jobb felső sarokban). Keresse meg a **modell fájljának elérési útját** , és adja meg a fájl teljes elérési útját a `kws.table` 2. lépésben.
4. Győződjön meg arról, hogy az engedélyezve feliratú jelölőnégyzet be **van** jelölve. Ez az üzenet jelenik meg a jelölőnégyzet mellett: "a következő kapcsolódáskor a kulcsszót fogja figyelni". Ha nem megfelelő fájlt vagy érvénytelen elérési utat adott meg, hibaüzenet jelenik meg.
5. Adja meg a Speech **előfizetés kulcsát**, az **előfizetési kulcs régióját**, majd kattintson az **OK** gombra a **Beállítások** menü bezárásához.
6. Kattintson az **újrakapcsolás** gombra. A következő üzenetnek kell megjelennie: "New beszélgetés kezdődött – típus, nyomja meg a mikrofon gombot, vagy mondja el a kulcsszót". Az alkalmazás mostantól folyamatosan figyel.
7. Beszéljen minden olyan kifejezésről, amely a kulcsszóval kezdődik. Például: "**{a kulcsszó}**, mi az idő?". A kulcsszó kimondása után nem kell szüneteltetni a szüneteltetést. Ha elkészült, két dolog történik:
   * Megtekintheti a küllőt
   * Röviddel azután, hogy meghallja a robot válaszát
8. Folytassa a kísérletet a robot által támogatott három bemeneti típussal:
   * Begépelt szöveg az alsó sávban
   * A mikrofon ikon és a beszéd megnyomása
   * A kulcsszóval kezdődő kifejezés

### <a name="view-the-source-code-that-enables-keyword"></a>A kulcsszót engedélyező forráskód megtekintése

A Windows Voice Assistant-ügyfél forráskódjában tekintse meg ezeket a fájlokat a kulcsszavak észlelésének engedélyezéséhez használt kód áttekintéséhez:

1. [`VoiceAssistantClient\Models.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/Models.cs) a beszédfelismerési SDK metódusának meghívása [`KeywordRecognitionModel.fromFile()`](/javascript/api/microsoft-cognitiveservices-speech-sdk/keywordrecognitionmodel#fromfile-string-) , amely a modell helyi fájlból való létrehozásához használatos a lemezen.
1. [`VoiceAssistantClient\MainWindow.xaml.cs`](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-wpf/VoiceAssistantClient/MainWindow.xaml.cs) a beszédfelismerési SDK metódusának hívását is tartalmazza [`DialogServiceConnector.StartKeywordRecognitionAsync()`](/dotnet/api/microsoft.cognitiveservices.speech.dialog.dialogserviceconnector.startkeywordrecognitionasync) , amely aktiválja a kulcsszó folyamatos észlelését.

## <a name="optional-change-the-language-and-bot-voice"></a>Választható A nyelv és a robot hangjának módosítása

Az Ön által létrehozott robot az angol nyelvű, alapértelmezett angol nyelvű szöveg – beszéd hang használatával fogja figyelni és válaszolni. Azonban nem kizárólag angol nyelvet használ, vagy az alapértelmezett hang. Ebből a szakaszból megtudhatja, hogyan módosíthatja a robot által figyelt és válaszoló nyelvet. Azt is megtudhatja, hogyan választhat másik hangot az adott nyelvhez.

### <a name="change-the-language"></a>A nyelv módosítása

A [beszéd – szöveg](language-support.md#speech-to-text) tábla bármelyik nyelve közül választhat. Az alábbi példában a nyelvet németre változtatjuk.

1. Nyissa meg a Windows Voice Assistant-ügyfélprogramot, kattintson a beállítások gombra (a jobb felső szintű fogaskerék ikonra), és írja be `de-de` a Language (nyelv) mezőbe (ez a területi beállítás, amely a [beszéd-szöveg](language-support.md#speech-to-text) táblában szerepel). Ezzel a beállítással megadhatja a beszélt nyelvet, felülbírálva az alapértelmezett értéket `en-us` . Ez arra is utasítja a közvetlen vonalas beszédfelismerési csatornát, hogy az alapértelmezett német hangon használja a bot-választ.
2. A beállítások lap bezárásához kattintson az újrakapcsolódás gombra, és hozzon létre egy új kapcsolatot az ECHO-robottal.
3. Kattintson a mikrofon gombra, és mondja el a német nyelvű kifejezést. Ekkor megjelenik a felismert szöveg és az ECHO-bot válasza az alapértelmezett német hangon.

### <a name="change-the-default-bot-voice"></a>Az alapértelmezett robot hangjának módosítása

A szöveg-beszéd hang és a vezérlő kiejtés kiválasztásával megteheti, hogy a bot egy [beszéd szintézis Markup Language](speech-synthesis-markup.md) (SSML) formában adja meg a választ egyszerű szöveg helyett. Az ECHO-bot nem használ SSML, de a kód egyszerűen módosítható. Az alábbi példában a SSML hozzáadjuk az ECHO bot-válaszhoz, így a német hang Stefan Apollo (egy férfi hangja) az alapértelmezett női hang helyett lesz felhasználva. Tekintse meg az Ön nyelvén támogatott [standard hangok](language-support.md#standard-voices) és [neurális hangok](language-support.md#neural-voices) listáját.

1. Kezdjük a megnyitásával `samples\csharp_dotnetcore\02.echo-bot\echo-bot.cs` .
2. Keresse meg a következő két sort:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replyText), cancellationToken);
    ```
3. Cserélje le őket a alábbiakra:
    ```csharp
    var replyText = $"Echo: {turnContext.Activity.Text}";
    var replySpeak = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='de-DE'>
                    <voice name='Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo)'>" +
                    $"{replyText}" + "</voice></speak>";
    await turnContext.SendActivityAsync(MessageFactory.Text(replyText, replySpeak), cancellationToken);
    ```
4. Hozzon létre egy megoldást a Visual Studióban, és javítsa ki az esetleges fordítási hibákat.

A "MessageFactory. Text" metódus második argumentuma beállítja a [tevékenység beszéd mezőt](https://github.com/Microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) a bot-válaszban. A fenti módosítás után a rendszer lecserélte az egyszerű szövegről a SSML-re, hogy nem alapértelmezett német hangvételt határozzon meg.

### <a name="redeploy-your-bot"></a>A robot újbóli üzembe helyezése

Most, hogy végrehajtotta a szükséges módosításokat a roboton, a következő lépés az újbóli közzététel a Azure App Serviceon, és próbálja ki:

1. A Megoldáskezelő ablakban kattintson a jobb gombbal a **EchoBot** projektre, és válassza a **Közzététel** lehetőséget.
2. A korábbi telepítési konfiguráció már be van töltve alapértelmezettként. Egyszerűen kattintson a **Közzététel** elemre a **EchoBot20190805125647-web Deploy** lehetőség mellett.
3. A **sikeres közzététel** üzenet megjelenik a Visual Studio kimeneti ablakában, és a "a robot készen áll!" üzenet jelenik meg.
4. Nyissa meg a Windows Voice Assistant-ügyfélprogramot, kattintson a beállítások gombra (a jobb felső szintű fogaskerék ikonra), és győződjön meg róla, hogy továbbra is `de-de` a Language (nyelv) mezőben van.
5. Kövesse a [Windows Voice Assistant-ügyfél futtatása](#run-the-windows-voice-assistant-client) az újonnan telepített robottal való újrakapcsolódáshoz című témakör utasításait, és beszéljen az új nyelven, és hallgassa meg a robot válaszát az új hanggal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a jelen oktatóanyagban üzembe helyezett echo-bot használatát, akkor a **SpeechEchoBotTutorial-ResourceGroup Azure-** erőforráscsoport törlésével eltávolíthatja azt és az összes hozzá tartozó Azure-erőforrást.

1. A [Azure Portal](https://portal.azure.com)kattintson az **Azure-szolgáltatások** navigációjának **erőforráscsoportok** elemére.
2. Keresse meg az erőforráscsoport neve: **SpeechEchoBotTutorial-ResourceGroup**. Kattintson a három pontra (...).
3. Válassza az **Erőforráscsoport törlése** elemet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Saját ügyfélalkalmazás létrehozása a Speech SDK-val](./quickstarts/voice-assistants.md?pivots=programming-language-csharp)

## <a name="see-also"></a>Lásd még

* Üzembe helyezés egy [közel található Azure-régióban](https://azure.microsoft.com/global-infrastructure/locations/) a bot válaszideje fejlesztéséhez
* [Magas színvonalú NEURÁLIS TTS-hangokat támogató Azure-régió](./regions.md#neural-and-standard-voices) üzembe helyezése
* A Direct line Speech Channel szolgáltatáshoz kapcsolódó díjszabás:
  * [A bot Service díjszabása](https://azure.microsoft.com/pricing/details/bot-service/)
  * [Beszédfelismerési szolgáltatás](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)
* Saját hang-kompatibilis bot kiépítése és üzembe helyezése:
  * Hozzon létre egy [robot Framework robotot](https://dev.botframework.com/). Regisztrálja a [Direct line Speech Channel](/azure/bot-service/bot-service-channel-connect-directlinespeech) használatával, és [szabja testre a robotját](/azure/bot-service/directline-speech-bot)
  * A meglévő [bot Framework-megoldások](https://microsoft.github.io/botframework-solutions/index)megismerése: [virtuális asszisztens](https://microsoft.github.io/botframework-solutions/overview/virtual-assistant-solution/) létrehozása és [kiterjesztése a közvetlen vonalas beszédre](https://microsoft.github.io/botframework-solutions/clients-and-channels/tutorials/enable-speech/1-intro/)
