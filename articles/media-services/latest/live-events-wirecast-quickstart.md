---
title: Azure Media Services élő stream létrehozása
description: Megtudhatja, hogyan hozhat létre Azure Media Services élő streamet a portál és a Wirecast használatával
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 76bbb980b6430f7cffc23ec078e2c932128dc637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89265269"
---
# <a name="create-an-azure-media-services-live-stream"></a>Azure Media Services élő stream létrehozása

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a rövid útmutató segítséget nyújt egy Azure Media Services élő stream létrehozásához a Azure Portal és a Wirecast használatával. Feltételezi, hogy rendelkezik Azure-előfizetéssel, és létrehozott egy Media Services fiókot.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőt, és lépjen a [Microsoft Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

Ebben a rövid útmutatóban a következőket mutatjuk be:

- Helyszíni kódoló beállítása a Wirecast ingyenes próbaidőszakával.
- Élő stream beállítása.
- Élő stream-kimenetek beállítása.
- Alapértelmezett folyamatos átviteli végpont futtatása.
- A Azure Media Player használatával megtekintheti az élő streamet és az igény szerinti kimenetet.

A dolgok egyszerű megtartása érdekében a Wirecast, az áteresztő felhő-kódolás és az RTMP használatával Azure Media Services kódolási beállításkészletet fogunk használni.

## <a name="set-up-an-on-premises-encoder-by-using-wirecast"></a>Helyszíni kódoló beállítása a Wirecast használatával

1. Töltse le és telepítse a Wirecast az operációs rendszeréhez a [webstream webhelyén](https://www.telestream.net).
1. Indítsa el az alkalmazást, és használja kedvenc e-mail-címét a termék regisztrálásához. Az alkalmazás nyitva tartása.
1. A kapott e-mailben ellenőrizze az e-mail-címét. Ezután az alkalmazás elindítja az ingyenes próbaverziót.
1. Ajánlott: Tekintse meg a videós oktatóanyagot az alkalmazás megnyitása képernyőn.

## <a name="set-up-an-azure-media-services-live-stream"></a>Azure Media Services élő stream beállítása

1. Nyissa meg a Azure Media Services fiókot a portálon belül, majd válassza az **élő közvetítés** lehetőséget a **Media Services** listában.

   ![Élő közvetítés hivatkozása](media/live-events-wirecast-quickstart/select-live-streaming.png)
1. Válassza az **élő esemény hozzáadása** lehetőséget az új élő közvetítési esemény létrehozásához.

   ![Élő esemény ikonjának hozzáadása](media/live-events-wirecast-quickstart/add-live-event.png)
1. Adja meg az új esemény nevét (például *TestLiveEvent*) az **élő esemény neve** mezőben.

   ![Élő esemény neve mező](media/live-events-wirecast-quickstart/live-event-name.png)
1. A **Leírás** mezőben adja meg az esemény opcionális leírását.
1. Válassza a **pass-through – nincs felhőalapú kódolási** beállítás lehetőséget.

   ![Felhőalapú kódolási lehetőség](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Válassza az **RTMP** lehetőséget.
1. Győződjön meg arról, hogy a **nincs** lehetőség van kiválasztva az **élő esemény indításához**, hogy elkerülje az élő esemény számlázását, mielőtt a rendszer készen áll. (A számlázás az élő esemény indításakor kezdődik.)

   ![Élő esemény indítása lehetőség](media/live-events-wirecast-quickstart/start-live-event-no.png)
1. A beállítások áttekintéséhez kattintson a **felülvizsgálat + létrehozás** gombra.
1. Válassza a **Létrehozás** gombot az élő esemény létrehozásához. Ezután visszatér az élő események listájára.
1. Válassza ki az imént létrehozott élő eseményre mutató hivatkozást. Figyelje meg, hogy az esemény leáll.
1. Tartsa meg a lapot a böngészőben. Később ismét vissza fogunk térni.

## <a name="set-up-a-live-stream-by-using-wirecast-studio"></a>Élő stream beállítása a Wirecast Studio használatával

1. A Wirecast alkalmazásban válassza a főmenü **Üres dokumentum létrehozása** elemét, majd kattintson a **Continue (folytatás**) gombra.

   ![Wirecast kezdőképernyő](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Vigye az egérmutatót az **Wirecast rétegek** területén lévő első réteg fölé.  Válassza a megjelenő **Hozzáadás** ikont, és válassza ki a továbbítani kívánt videó-bemenetet.

   ![Wirecast hozzáadása ikon](media/live-events-wirecast-quickstart/add-icon.png)

   Megnyílik az **1. fő réteg** párbeszédpanel.
1. Válassza a menüben a **videók rögzítése** lehetőséget, majd válassza ki a használni kívánt kamerát.

   ![Videó rögzítésének előnézeti területe](media/live-events-wirecast-quickstart/video-shot-selection.png)

   A kamera nézete az előnézet területén jelenik meg.
1. Vigye a kurzort a **Wirecast rétegek** területén található második réteg fölé. Válassza a megjelenő **Hozzáadás** ikont, és válassza ki az adatfolyamként használni kívánt hangbemenetet. Megnyílik a **2. fő réteg** párbeszédpanel.
1. Válassza a menüben a **hangrögzítés** lehetőséget, majd válassza ki a használni kívánt hangbemenetet.

   ![Bemenetek hangfelvételhez](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. A főmenüben válassza a **kimeneti beállítások** lehetőséget. Megjelenik a **kimeneti célhely kiválasztása** párbeszédpanel.
1. Válassza a **Azure Media Services** elemet a **cél** legördülő listából. A Azure Media Services kimeneti beállítása automatikusan kitölti a kimeneti beállítások *többségét* .

   ![Wirecast kimeneti beállítások képernyő](media/live-events-wirecast-quickstart/azure-media-services.png)


A következő eljárásban visszatérhet Azure Media Services a böngészőben a bemeneti URL-cím másolásához a kimeneti beállításokba való belépéshez:

1. A portál Azure Media Services lapján kattintson a **Start** gombra az élő stream esemény elindításához. (A számlázás most kezdődik.)

   ![Start ikon](media/live-events-wirecast-quickstart/start.png)
2. A **biztonságos/nem biztonságos** váltógomb beállítása **nem biztonságos**. Ez a lépés a protokollt a RTMP helyett RTMP-re állítja be.
3. A **bemeneti URL-cím** mezőben másolja a vágólapra az URL-címet.

   ![Bemeneti URL-cím](media/live-events-wirecast-quickstart/input-url.png)
4. Váltson a Wirecast alkalmazásra, és illessze be a **bemeneti URL** -címet a kimeneti beállítások **cím** mezőjébe.

   ![Wirecast bemeneti URL-címe](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Válassza az **OK** lehetőséget.

## <a name="set-up-outputs"></a>Kimenetek beállítása

Ez a rész beállítja a kimeneteket, és lehetővé teszi az élő stream rögzítésének mentését.  

> [!NOTE]
> A kimenet továbbításához a streaming végpontnak futnia kell. Lásd a következőt: az [alapértelmezett folyamatos átviteli végpont szakasz futtatása](#run-the-default-streaming-endpoint) .

1. Válassza a kimenetek **létrehozása** hivatkozást a **kimenetek** videó megjelenítője alatt.
1. Ha szeretné, a **név** mezőben adja meg a kimenet nevét, hogy még több felhasználóbarát legyen, így később könnyen megtalálhatja.
   
   ![Kimenet neve mező](media/live-events-wirecast-quickstart/output-name.png)
1. Az összes többi dobozt csak most hagyja.
1. Kattintson a **tovább** gombra a folyamatos átviteli lokátor hozzáadásához.
1. Ha kívánja, változtassa meg a lokátor nevét valami más felhasználóbarát értékre.
   
   ![Lokátor neve mező](media/live-events-wirecast-quickstart/live-event-locator.png)
1. A képernyő minden más elemének kihagyása egyelőre.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="start-the-broadcast"></a>A közvetítés elindítása

1. A Wirecast-ben válassza a **kimenet**  >  **indítása/leállítási szórás**  >  **indítása Azure Media Services: Azure Media Services** lehetőséget a főmenüben.

   ![Szórási menüelemek indítása](media/live-events-wirecast-quickstart/start-broadcast.png)

   Ha az adatfolyamot elküldjék az élő eseményre, a Wirecast **élő** ablaka megjelenik a Azure Media Services élő esemény lapján.

1. A Wirecast rétegekhez kiválasztott videó és hang közvetítésének megkezdéséhez kattintson az előnézet ablak alatti **Go (ugrás** ) gombra.

   ![Wirecast ugrás gomb](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Ha hiba merül fel, próbálja meg újra betölteni a lejátszót a lejátszó **újratöltése** hivatkozásra kattintva.

## <a name="run-the-default-streaming-endpoint"></a>Az alapértelmezett folyamatos átviteli végpont futtatása

1. Válassza a **folyamatos átviteli végpontok** lehetőséget a Media Services listájában.

   ![Adatfolyam-végpontok menüelem](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Ha az alapértelmezett folyamatos átviteli végpont állapota leáll, válassza ki. Ezzel a lépéssel megtekintheti az adott végpont lapját.
1. Válassza az **Indítás** elemet.
   
   ![A streaming végpont Start gombja](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>A kimeneti szórás lejátszása Azure Media Player használatával

1. Másolja a streaming URL-címet a **kimeneti** videolejátszó alá.
1. A böngészőben nyissa meg a [Azure Media Player demót](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Illessze be a streaming URL-címet Azure Media Player **URL-** mezőjébe.
1. Kattintson a **lejátszó frissítése** gombra.
1. Az élő stream megjelenítéséhez válassza a videó **Lejátszás** ikonját.

## <a name="stop-the-broadcast"></a>A szórás leállítása

Ha úgy gondolja, hogy elég tartalmat továbbított, állítsa le a szórást.

1. A Wirecast területen válassza a **szórás** gombot. Ez a lépés leállítja a szórást a Wirecast.
1. A portálon válassza a **Leállítás** lehetőséget. Ekkor megjelenik egy figyelmeztető üzenet arról, hogy az élő stream leáll, de a kimenet egy igény szerinti eszköz lesz.
1. Kattintson a **Leállítás** gombra a figyelmeztető üzenetben. Azure Media Player mostantól hibaüzenetet jelenít meg, mert az élő stream már nem érhető el.

## <a name="play-the-on-demand-output-by-using-azure-media-player"></a>Az igény szerinti kimenet lejátszása Azure Media Player használatával

A létrehozott kimenet elérhető az igény szerinti folyamatos átvitelhez, ha a streaming végpontja fut.

1. Nyissa meg az Media Services listáját, és válassza az **eszközök** elemet.
1. Keresse meg a korábban létrehozott esemény kimenetét, és válassza ki az eszközre mutató hivatkozást. Megnyílik az eszköz kimenete lap.
1. Másolja a streaming URL-címet az adott eszközhöz tartozó videolejátszó alá.
1. Térjen vissza Azure Media Player a böngészőben, és illessze be a streaming URL-címet az URL mezőbe.
1. Válassza a **lejátszó frissítése** lehetőséget.
1. Az igény szerinti eszköz megtekintéséhez válassza a videó **Lejátszás** ikonját.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

> [!IMPORTANT]
> Állítsa le a szolgáltatásokat! A rövid útmutató lépéseinek elvégzése után ne felejtse el leállítani az élő eseményt és a folyamatos átviteli végpontot, vagy ha a futása alatt marad. Az élő esemény leállításához tekintse meg a [szórási eljárás leállítása](#stop-the-broadcast) , 2. és 3. lépéseit.

A folyamatos átviteli végpont leállítása:

1. A Media Services listában válassza a **folyamatos átviteli végpontok** lehetőséget.
2. Válassza ki a korábban elindított alapértelmezett adatfolyam-végpontot. Ez a lépés megnyitja a végpont lapját.
3. Válassza a **Leállítás** lehetőséget.

> [!TIP]
> Ha nem szeretné megtartani az adott eseményből származó eszközöket, ne felejtse el törölni őket, hogy ne kelljen fizetnie a tárterületért.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Élő események és élő kimenetek Media Services](./live-events-outputs-concept.md)
