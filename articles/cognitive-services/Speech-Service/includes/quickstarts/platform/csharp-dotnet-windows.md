---
title: 'Rövid útmutató: a .NET-keretrendszerhez készült Speech SDK (Windows) platform beállítása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ezzel az útmutatóval beállíthatja a C# platformját a Windowshoz készült .NET-keretrendszerben a Speech Service SDK-val.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-dotnet
ms.openlocfilehash: 1a9faf24c5a82b815b40afe15769480b69074dc9
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552059"
---
Ez az útmutató bemutatja, hogyan telepítheti a .NET-keretrendszerhez készült [SPEECH SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) -t (Windows). Ha csak azt szeretné, hogy a csomag neve megkezdhető legyen a saját számára, futtassa `Install-Package Microsoft.CognitiveServices.Speech` a parancsot a NuGet-konzolon.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következőkre van szükség:

* Windows rendszeren a platformhoz a [Microsoft Visual C++ terjeszthető változata szükséges a Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) -hez. Ha először telepíti az elsőt, újraindításra lehet szükség.
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

## <a name="create-a-visual-studio-project-and-install-the-speech-sdk"></a>Visual Studio-projekt létrehozása és a Speech SDK telepítése

Telepítenie kell a [SPEECH SDK NuGet-csomagot](https://aka.ms/csspeech/nuget) , hogy a kód alapján lehessen hivatkozni rá. Ehhez először létre kell hoznia egy **HelloWorld** -projektet. Ha már rendelkezik egy olyan projekttel, amelyen elérhető a **.net Desktop fejlesztői** munkaterhelése, használhatja ezt a projektet, és ugorjon [a NuGet csomagkezelő használatára a Speech SDK telepítéséhez](#use-nuget-package-manager-to-install-the-speech-sdk).

### <a name="create-helloworld-project"></a>HelloWorld projekt létrehozása

1. Nyissa meg a Visual Studio 2019 alkalmazást.

1. A Start ablakban válassza az **új projekt létrehozása** lehetőséget. 

1. Az **új projekt létrehozása** ablakban válassza a **Console app (.NET-keretrendszer)** lehetőséget, majd kattintson a **tovább** gombra.

1. Az **új projekt konfigurálása** ablakban írja be a *HelloWorld* nevet a **projekt neve** mezőbe, válassza ki vagy hozza létre a könyvtár elérési útját a **helyen**, majd válassza a **Létrehozás** lehetőséget.

1. A Visual Studio menüsávban válassza az **eszközök**  >  **beolvasása eszközök és szolgáltatások** elemet, amely megnyitja a Visual Studio telepítőjét, és megjeleníti a **módosítás** párbeszédpanelt.

1. Győződjön meg arról, hogy elérhető-e a **.net Desktop fejlesztői** munkaterhelése. Ha a munkaterhelés még nincs telepítve, jelölje be a jelölőnégyzetet, majd kattintson a **módosítás** gombra a telepítés elindításához. A letöltése és telepítése néhány percet is igénybe vehet.

   Ha a **.net Desktop-fejlesztés** melletti jelölőnégyzet már be van jelölve, kattintson a **Bezárás** gombra a párbeszédpanelből való kilépéshez.

   ![A .NET asztali fejlesztés engedélyezése](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Zárjuk be a Visual Studio telepítőjét.

### <a name="use-nuget-package-manager-to-install-the-speech-sdk"></a>A NuGet csomagkezelő használata a Speech SDK telepítéséhez

1. A Megoldáskezelő kattintson a jobb gombbal a **HelloWorld** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget a NuGet csomagkezelő megjelenítéséhez.

   ![NuGet-csomagkezelő](~/articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. A jobb felső sarokban keresse meg a **csomag forrása** legördülő listát, és ellenőrizze, hogy be van-e **`nuget.org`** jelölve.

1. A bal felső sarokban válassza a **Tallózás** lehetőséget.

1. A keresőmezőbe írja be a *Microsoft. CognitiveServices. Speech* kifejezést, és válassza az **ENTER billentyűt**.

1. A keresési eredmények közül válassza ki a **Microsoft. CognitiveServices. Speech** csomagot, majd válassza a **telepítés** lehetőséget a legújabb stabil verzió telepítéséhez.

   ![Telepítse a Microsoft. CognitiveServices. Speech NuGet csomagot](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. A telepítés elindításához fogadja el az összes szerződést és licencet.

   A csomag telepítése után a rendszer megerősítő üzenetet jelenít meg a **Package Manager konzol** ablakban.

### <a name="choose-target-architecture"></a>Cél architektúra kiválasztása

A konzol alkalmazás létrehozásához és futtatásához hozzon létre egy, a számítógép architektúrájának megfelelő platform-konfigurációt.

1. A menüsávban válassza a **Build**  >  **Configuration Manager** elemet. Megjelenik a **Configuration Manager** párbeszédpanel.

   ![Configuration Manager párbeszédpanel](~/articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Az **aktív megoldási platform** legördülő listában válassza az **új** lehetőséget. Megjelenik az **új megoldás-platform** párbeszédpanel.

1. A **típus vagy a válassza ki az új platform** legördülő listát:
   - Ha 64 bites Windows rendszert futtat, válassza az **x64** elemet.
   - Ha 32 bites Windows rendszert futtat, válassza az **x86** lehetőséget.

1. Válassza **az OK** , majd a **Bezárás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [windows](../quickstart-list.md)]
