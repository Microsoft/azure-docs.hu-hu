---
title: 'Rövid útmutató: hangsegéd létrehozása egyéni parancsok használatával'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban egy alapszintű egyéni parancsok alkalmazását hozza létre és teszteli a Speech Studio használatával.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.custom: references_regions
ms.openlocfilehash: e046f8cbf6fa0418244f20e9a0c6f75f6da34136
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102434627"
---
# <a name="create-a-voice-assistant-using-custom-commands"></a>Hangvezérelt asszisztens létrehozása egyéni parancsokkal

Ebben a rövid útmutatóban egy alapszintű egyéni parancsok alkalmazását hozza létre és teszteli a Speech Studio használatával. Ezt az alkalmazást egy Windows-ügyfélalkalmazás is elérheti.

## <a name="region-availability"></a>Régió elérhetősége
Jelenleg az egyéni parancsok támogatják a következő régiókban létrehozott beszédfelismerési előfizetéseket:
* USA nyugati régiója
* USA 2. nyugati régiója
* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati középső régiója
* Észak-Európa
* Nyugat-Európa
* Kelet-Ázsia
* Délkelet-Ázsia
* Közép-India

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Hozzon létre egy Azure Speech-erőforrást egy olyan régióban, amely támogatja az egyéni parancsokat.</a> A támogatott régiók listájáért tekintse meg a **régió elérhetőségét** ismertető szakaszt.
> * Töltse le a minta [Smart Room Lite](https://aka.ms/speech/cc-quickstart) JSON-fájlját.
> * Töltse le a [Windows Voice Assistant-ügyfél](https://aka.ms/speech/va-samples-wvac)legújabb verzióját.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ugrás a Speech Studio egyéni parancsaihoz

1. A böngészőben nyissa meg a [Speech studiót](https://speech.microsoft.com/).
1. Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

   Az alapértelmezett nézet a beszédfelismerési előfizetések listája.
   > [!NOTE]
   > Ha nem jelenik meg az előfizetés kiválasztása lap, akkor a felső sáv beállítások menüjében válassza a "Speech Resources" (beszédfelismerési erőforrások) lehetőséget.

1. Válassza ki a beszédfelismerési előfizetést, majd válassza a **Ugrás a studióba** lehetőséget.
1. Válassza az **egyéni parancsok** lehetőséget.

   Az alapértelmezett nézet a kiválasztott előfizetéshez tartozó egyéni parancsok alkalmazásának listája.

## <a name="import-an-existing-application-as-a-new-custom-commands-project"></a>Meglévő alkalmazás importálása új egyéni parancsok projektként

1. Projekt létrehozásához válassza az **új projekt** lehetőséget.

1. A **név** mezőbe írja be a Project Name (projekt neve `Smart-Room-Lite` ) nevet (vagy valami más választott).
1. A **nyelv** listában válassza az **angol (Egyesült Államok)** lehetőséget.
1. Válassza a **Fájlok tallózása** lehetőséget, majd a Tallózás ablakban válassza ki a **SmartRoomLite.js** fájlt.

    > [!div class="mx-imgBorder"]
    > ![Projekt létrehozása](media/custom-commands/import-project.png)

1.  A **Luis authoring Resource** listán válasszon ki egy authoring-erőforrást. Ha nincsenek érvényes authoring-erőforrások, hozzon létre egyet az  **új Luis authoring-erőforrás létrehozása** lehetőség kiválasztásával.

    > [!div class="mx-imgBorder"]
    > ![Erőforrás létrehozása](media/custom-commands/create-new-luis-resource.png)
    
    
    1. Az **Erőforrás neve** mezőbe írja be az erőforrás nevét.
    1. Az **erőforráscsoport** listában válasszon ki egy erőforráscsoportot.
    1. A **hely** listában válasszon ki egy helyet.
    1. A **díjszabási** csomag listáról válassza ki a kívánt szintet.
    
    
    > [!NOTE]
    > Az erőforráscsoportok létrehozásához írja be a kívánt erőforráscsoport nevét az "erőforráscsoport" mezőbe. Az erőforráscsoport akkor jön létre, amikor a **Létrehozás** beállítás van kiválasztva.


1. Ezután válassza a **Létrehozás** lehetőséget a projekt létrehozásához.
1. A projekt létrehozása után válassza ki a projektet.
Ekkor az új egyéni parancsok alkalmazás áttekintését kell látnia.

## <a name="try-out-some-voice-commands"></a>Próbáljon ki néhány hangutasítást
1. A jobb oldali ablaktábla tetején válassza a **betanítás** lehetőséget.
1. A betanítás befejezése után válassza a **test (tesztelés** ) lehetőséget, és próbálja ki a következő hosszúságú kimondott szöveg:
    - A TV bekapcsolása
    - A hőmérséklet beállítása 80 fokos értékre
    - Kikapcsolás
    - A TV
    - Ébresztés beállítása 5 óráig

## <a name="integrate-custom-commands-application-in-an-assistant"></a>Egyéni parancsok alkalmazásának integrálása asszisztensként
Ahhoz, hogy hozzáférhessen az alkalmazáshoz a Speech studión kívül, közzé kell tennie az alkalmazást. Az alkalmazások közzétételéhez konfigurálnia kell az előrejelzési LUIS-erőforrást.  

### <a name="update-prediction-luis-resource"></a>Előrejelzési LUIS-erőforrás frissítése


1. Válassza a bal oldali ablaktábla **Beállítások** elemét, majd a középső ablaktáblán válassza a  **Luis-erőforrások** lehetőséget.
1. Válasszon előrejelzési erőforrást, vagy hozzon létre egyet az **új erőforrás létrehozása** lehetőség kiválasztásával.
1. Kattintson a **Mentés** gombra.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS-erőforrások beállítása](media/custom-commands/set-luis-resources.png)

> [!NOTE]
> Mivel a szerzői erőforrás csak 1 000 előrejelzési végponti kérést támogat havonta, kötelezően kell beállítania a LUIS előrejelzési erőforrást az egyéni parancsok alkalmazásának közzététele előtt.

### <a name="publish-the-application"></a>Az alkalmazás közzététele

Válassza a  **Közzététel** elemet a jobb oldali ablaktábla tetején. A közzététel befejeződése után új ablak jelenik meg. Jegyezze fel az **alkalmazás azonosítóját** és a **beszédfelismerési erőforrás kulcsának** értékét. Erre a két értékre szüksége lesz ahhoz, hogy hozzáférjen az alkalmazáshoz a Speech studión kívülről.

Azt is megteheti, hogy ezeket az értékeket a **Beállítások**  >  **általános** szakaszban is bejelöli.

### <a name="access-application-from-client"></a>Alkalmazás elérése az ügyféltől

A jelen cikk hatókörében az előfeltételek részeként letöltött Windows Voice Assistant-ügyfelet fogjuk használni. Csomagolja ki a mappát.
1. **VoiceAssistantClient.exe** elindítása.
1. Hozzon létre egy új közzétételi profilt, és adja meg a **kapcsolatprofil** értékét. Az **általános beállítások** szakaszban adja meg az értékek **előfizetési kulcsát** (ez ugyanaz, mint az alkalmazás közzétételekor mentett **beszédfelismerési erőforrás kulcsának** értéke), az **előfizetési kulcs régiója** és az **egyéni parancsok alkalmazás-azonosítója**.
    > [!div class="mx-imgBorder"]
    > ![Képernyőfelvétel: a WVAC-profil létrehozásához szükséges általános beállítások szakasz.](media/custom-commands/create-profile.png)
1. Válassza **a Mentés és a profil alkalmazása** lehetőséget.
1. Most próbálja ki a következő bemeneteket beszéd/szöveg használatával
    > [!div class="mx-imgBorder"]
    > ![WVAC-profil létrehozása](media/custom-commands/conversation.png)


> [!TIP]
> A **tevékenység naplójának** bejegyzéseire kattintva ellenőrizheti az egyéni parancsok szolgáltatásból küldött nyers válaszokat.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy meglévő alkalmazást használt. A következő, [útmutató szakaszban](./how-to-develop-custom-commands-application.md)megtudhatja, hogyan tervezhet, fejleszthet, végezhet hibakeresést, tesztelheti és integrálhatja az egyéni parancsok alkalmazásait a semmiből.