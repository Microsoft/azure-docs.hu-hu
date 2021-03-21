---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001097"
---
A Windows-fejlesztéshez készült Visual Studio-projekt létrehozásához létre kell hoznia a projektet, be kell állítania a Visual studiót a .NET Desktop-fejlesztéshez, telepítenie kell a Speech SDK-t, és ki kell választania a cél architektúrát.

### <a name="create-the-project-and-add-the-workload"></a>A projekt létrehozása és a munkaterhelés hozzáadása

A kezdéshez hozza létre a projektet a Visual Studióban, és győződjön meg arról, hogy a Visual Studio be van állítva a .NET Desktop Development szolgáltatáshoz:

1. Nyissa meg a Visual Studio 2019 alkalmazást.

1. A Start ablakban válassza az **új projekt létrehozása** lehetőséget. 

1. Az **új projekt létrehozása** ablakban válassza a **Console app (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább** gombra.

1. Az **új projekt konfigurálása** ablakban írja be a *HelloWorld* nevet a **projekt neve** mezőbe, válassza ki vagy hozza létre a könyvtár elérési útját a **helyen**, majd válassza a **Létrehozás** lehetőséget.

1. A Visual Studio menüsávban válassza az **eszközök**  >  **beolvasása eszközök és szolgáltatások** elemet, amely megnyitja a Visual Studio telepítőjét, és megjeleníti a **módosítás** párbeszédpanelt.

1. Győződjön meg arról, hogy elérhető-e a **.net Desktop fejlesztői** munkaterhelése. Ha a munkaterhelés még nincs telepítve, jelölje be a jelölőnégyzetet, majd kattintson a **módosítás** gombra a telepítés elindításához. A letöltése és telepítése néhány percet is igénybe vehet.

   Ha a **.net Desktop-fejlesztés** melletti jelölőnégyzet már be van jelölve, kattintson a **Bezárás** gombra a párbeszédpanelből való kilépéshez.

   ![A .NET asztali fejlesztés engedélyezése](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zárjuk be a Visual Studio telepítőjét.

### <a name="install-the-speech-sdk"></a>A Speech SDK telepítése

A következő lépés a [SPEECH SDK NuGet-csomag](https://aka.ms/csspeech/nuget)telepítése, így a kódban hivatkozhat rá.

1. A Megoldáskezelő kattintson a jobb gombbal a **HelloWorld** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget a NuGet csomagkezelő megjelenítéséhez.

   ![NuGet-csomagkezelő](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. A jobb felső sarokban keresse meg a **csomag forrása** legördülő listát, és ellenőrizze, hogy a **nuget.org** van-e kiválasztva.

1. A bal felső sarokban válassza a **Tallózás** lehetőséget.

1. A keresőmezőbe írja be a *Microsoft. CognitiveServices. Speech* kifejezést, és válassza az **ENTER billentyűt**.

1. A keresési eredmények közül válassza ki a **Microsoft. CognitiveServices. Speech** csomagot, majd válassza a **telepítés** lehetőséget a legújabb stabil verzió telepítéséhez.

   ![Telepítse a Microsoft. CognitiveServices. Speech NuGet csomagot](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. A telepítés elindításához fogadja el az összes szerződést és licencet.

   A csomag telepítése után a rendszer megerősítő üzenetet jelenít meg a **Package Manager konzol** ablakban.

### <a name="choose-the-target-architecture"></a>Válassza ki a cél architektúrát

A konzol alkalmazás létrehozásához és futtatásához hozzon létre egy, a számítógép architektúrájának megfelelő platform-konfigurációt.

1. A menüsávban válassza a **Build**  >  **Configuration Manager** elemet. Megjelenik a **Configuration Manager** párbeszédpanel.

   ![Configuration Manager párbeszédpanel](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Az **aktív megoldási platform** legördülő listában válassza az **új** lehetőséget. Megjelenik az **új megoldás-platform** párbeszédpanel.

1. A **típus vagy a válassza ki az új platform** legördülő listát:
   - Ha 64 bites Windows rendszert futtat, válassza az **x64** elemet.
   - Ha 32 bites Windows rendszert futtat, válassza az **x86** lehetőséget.

1. Válassza **az OK** , majd a **Bezárás** lehetőséget.
