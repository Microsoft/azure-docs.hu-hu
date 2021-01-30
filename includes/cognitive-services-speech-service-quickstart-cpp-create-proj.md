---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 2960a4f55997c0db60c22e7841b4ba82708dd4e1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213994"
---
Visual Studio-projekt C++ asztali fejlesztéshez való létrehozásához be kell állítania a Visual Studio fejlesztői beállításait, létre kell hoznia a projektet, ki kell választania a cél architektúrát, és telepítenie kell a Speech SDK-t.

### <a name="set-up-visual-studio-development-options"></a>A Visual Studio fejlesztési lehetőségeinek beállítása

A kezdéshez győződjön meg arról, hogy megfelelően van-e beállítva a Visual Studióban a C++ asztali fejlesztéshez:

1. Nyissa meg a Visual Studio 2019 alkalmazást a **Start** ablak megjelenítéséhez.

   ![A Visual Studio 2019 "Start" ablakát bemutató képernyőkép.](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png)

1. Válassza a **Folytatás kód nélkül** lehetőséget a Visual Studio ide elemre.

1. A Visual Studio menüsávjában válassza az **eszközök** eszközök  >  **és szolgáltatások beolvasása** lehetőséget a Visual Studio telepítőjének megnyitásához, és tekintse meg a **módosítás** párbeszédpanelt.

   ![A Visual Studio telepítőjének "módosítás" párbeszédpanelének "számítási feladatok" lapját bemutató képernyőkép.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. A **munkaterhelések** lapon, a **Windows** alatt keresse meg az **asztali fejlesztés C++** munkaterheléssel című részt. Ha a munkaterhelés melletti jelölőnégyzet még nincs kiválasztva, válassza ki.

1. Az **egyes összetevők** lapon keresse meg a **NuGet csomagkezelő** jelölőnégyzetet. Ha a jelölőnégyzet nincs bejelölve, jelölje ki.

1. Kattintson a **Bezárás** vagy a **módosítás** gombra a sarokban. (A gomb neve attól függően változik, hogy van-e a telepítéshez kiválasztott szolgáltatások.) Ha a **módosítás** lehetőséget választja, a telepítés megkezdődik, ami eltarthat egy ideig.

1. Zárjuk be a Visual Studio telepítőjét.

### <a name="create-the-project-and-select-the-target-architecture"></a>Hozza létre a projektet, és válassza ki a cél architektúrát

Ezután hozza létre a projektet:

1. A Visual Studio menüsávjában válassza a **fájl**  >  **új**  >  **projekt** lehetőséget az **új projekt létrehozása** ablak megjelenítéséhez.

   ![Új projekt létrehozása, C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Keresse meg és válassza ki a **konzol alkalmazást**. Győződjön meg róla, hogy kijelöli a projekt típusú C++-verziót (a C# vagy a Visual Basic helyett).

1. Kattintson a **tovább** gombra az **új projekt konfigurálása** képernyő megjelenítéséhez.

   ![Az új projekt konfigurálása, C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. A **Project Name (projekt neve**) mezőben adja meg a értéket `helloworld` .

1. A **hely** területen navigáljon, és válassza ki vagy hozza létre azt a mappát, amelybe menteni szeretné a projektet.

Most válassza ki a cél platform architektúráját. A Visual Studio eszköztárán keresse meg a **megoldás platformok** legördülő listát. (Ha nem látja, válassza a **nézet**  >  lehetőséget. **Eszköztárak**  >  **Standard** a **megoldási platformokat** tartalmazó eszköztár megjelenítéséhez.) Ha 64 bites Windows rendszert futtat, válassza a legördülő listából az **x64** lehetőséget. a 64 bites Windows rendszer 32 bites alkalmazásokat is futtathat, így ha szeretné, válassza az **x86** lehetőséget.

### <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

Végül telepítse a [SPEECH SDK NuGet-csomagot](https://aka.ms/csspeech/nuget), és hivatkozzon a projektben található Speech SDK-ra:

1. **Megoldáskezelőban** kattintson a jobb gombbal a megoldásra, és válassza a megoldás **NuGet-csomagok kezelése** lehetőséget, hogy megnyissa a **NuGet-megoldás** ablakát.

1. Válassza a **Tallózás** lehetőséget.

   ![NuGet – megoldás lap, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. A **csomag forrása** területen válassza a **nuget.org** lehetőséget.

1. A **keresőmezőbe** írja be a kifejezést `Microsoft.CognitiveServices.Speech` , majd válassza ki a csomagot, miután az megjelenik a keresési eredmények között.

   ![Microsoft. CognitiveServices. Speech C++ csomag telepítése – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. A keresési eredmények melletti csomag állapota ablaktáblán válassza ki a **HelloWorld** -projektet.

1. Válassza a **Telepítés** gombot.

1. A **módosítások előnézete** párbeszédpanelen kattintson **az OK gombra**.

1. A **licenc elfogadása** párbeszédpanelen tekintse meg a licencet, majd válassza az **Elfogadom** lehetőséget. A csomag telepítése megkezdődik, és amikor a telepítés befejeződött, a **kimenet** ablaktáblán a következő szöveghez hasonló üzenet jelenik meg: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.15.0' to helloworld` .
