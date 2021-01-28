---
title: Élő stream létrehozása a OBS Studióval
description: Megtudhatja, hogyan hozhat létre Azure Media Services élő streamet a portál és a OBS Studio használatával
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 08/31/2020
ms.openlocfilehash: 83f072b67f5aa162137a55d2b311dccf0daf7f53
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956072"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Azure Media Services élő stream létrehozása a OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a rövid útmutató segítséget nyújt egy Azure Media Services élő stream létrehozásához a Azure Portal és a Open Broadcasting Studio (OBS) használatával. Feltételezi, hogy rendelkezik Azure-előfizetéssel, és létrehozott egy Media Services fiókot.

Ebben a rövid útmutatóban a következőket mutatjuk be:

- Helyszíni kódoló beállítása a OBS-mel.
- Élő stream beállítása.
- Élő stream-kimenetek beállítása.
- Alapértelmezett folyamatos átviteli végpont futtatása.
- A Azure Media Player használatával megtekintheti az élő streamet és az igény szerinti kimenetet.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Nyissa meg a webböngészőt, és lépjen a [Microsoft Azure Portal](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="set-up-an-on-premises-encoder-by-using-obs"></a>Helyszíni kódoló beállítása a OBS használatával

1. Töltse le és telepítse a OBS az operációs rendszerhez a [nyílt műsorszolgáltató szoftver webhelyén](https://obsproject.com/).
1. Indítsa el az alkalmazást, és tartsa megnyitva.

## <a name="run-the-default-streaming-endpoint"></a>Az alapértelmezett folyamatos átviteli végpont futtatása

1. Válassza a **folyamatos átviteli végpontok** lehetőséget a Media Services listájában.

   ![Adatfolyam-végpontok menüelem](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Ha az alapértelmezett folyamatos átviteli végpont állapota leáll, válassza ki. Ezzel a lépéssel megtekintheti az adott végpont lapját.
1. Válassza az **Indítás** elemet.

   ![A streaming végpont Start gombja](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Azure Media Services élő stream beállítása

1. Nyissa meg a Azure Media Services fiókot a portálon belül, majd válassza az **élő közvetítés** lehetőséget a **Media Services** listában.

   ![Élő közvetítés hivatkozása](media/live-events-obs-quickstart/select-live-streaming.png)
1. Válassza az **élő esemény hozzáadása** lehetőséget az új élő közvetítési esemény létrehozásához.

   ![Élő esemény ikonjának hozzáadása](media/live-events-obs-quickstart/add-live-event.png)
1. Adja meg az új esemény nevét (például *TestLiveEvent*) az **élő esemény neve** mezőben.

   ![Élő esemény neve mező](media/live-events-obs-quickstart/live-event-name.png)
1. A **Leírás** mezőben adja meg az esemény opcionális leírását.
1. Válassza a **pass-through – nincs felhőalapú kódolási** beállítás lehetőséget.

   ![Felhőalapú kódolási lehetőség](media/live-events-obs-quickstart/cloud-encoding.png)
1. Válassza az **RTMP** lehetőséget.
1. Győződjön meg arról, hogy a **nincs** lehetőség van kiválasztva az **élő esemény indításához**, hogy elkerülje az élő esemény számlázását, mielőtt a rendszer készen áll. (A számlázás az élő esemény indításakor kezdődik.)

   ![Élő esemény indítása lehetőség](media/live-events-obs-quickstart/start-live-event-no.png)
1. A beállítások áttekintéséhez kattintson a **felülvizsgálat + létrehozás** gombra.
1. Válassza a **Létrehozás** gombot az élő esemény létrehozásához. Ezután visszatér az élő események listájára.
1. Válassza ki az imént létrehozott élő eseményre mutató hivatkozást. Figyelje meg, hogy az esemény leáll.
1. Tartsa meg a lapot a böngészőben. Később ismét vissza fogunk térni.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Élő stream beállítása a OBS Studio használatával

A OBS egy alapértelmezett jelenettel kezdődik, de nincsenek bemenetek kijelölve.

   ![OBS alapértelmezett képernyő](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Videó forrásának hozzáadása

1. A **források** panelen kattintson a **Hozzáadás** ikonra egy új forrásoldali eszköz kiválasztásához. Ekkor megnyílik a **források** menü.

1. Válassza ki a **videó rögzítése eszközt** a forrás eszköz menüjéből. Ekkor megnyílik a **Forrás létrehozása/kiválasztása** menü.

   ![OBS-források menü kiválasztva](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Jelölje be a **meglévő választógomb hozzáadása** gombot, majd kattintson **az OK** gombra. Ekkor megnyílik a **videoeszköz tulajdonságai** menü.

   ![Új OBS menü megnyitása meglévő kijelölés hozzáadásával](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Az **eszköz** legördülő listából válassza ki a szóráshoz használni kívánt videó bemenetet. Hagyja a többi beállítást egyelőre, és kattintson az **OK** gombra. A bemeneti forrás hozzá lesz adva a **források** panelhez, és a videó bemeneti nézete megjelenik az **előnézet** területen.

   ![OBS kamera beállításai](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Hangforrás hozzáadása

1. A **források** panelen kattintson a **Hozzáadás** ikonra egy új forrásoldali eszköz kiválasztásához. Ekkor megnyílik a forrás eszköz menüje.

1. Válassza a **hangbemenet rögzítése** lehetőséget a forrás eszköz menüjében. Ekkor megnyílik a **Forrás létrehozása/kiválasztása** menü.

   ![OBS-források menü a kijelölt hangeszközzel](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Jelölje be a **meglévő választógomb hozzáadása** gombot, majd kattintson **az OK** gombra. Ekkor megnyílik a **hangbemenet rögzítése menü tulajdonságai** .

   ![OBS hangforrása a meglévő kijelölés hozzáadásával ](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Az **eszköz** legördülő listából válassza ki a szóráshoz használni kívánt hangrögzítési eszközt. Hagyja a többi beállítást egyelőre, és kattintson az OK gombra. A hangrögzítési eszköz hozzá lesz adva a hangkeverő panelhez.

   ![OBS hangeszköz kiválasztása legördülő lista](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-in-obs"></a>Folyamatos átvitel beállítása a OBS-ben

A következő eljárásban visszatérhet Azure Media Services a böngészőben a bemeneti URL-cím másolásához a kimeneti beállításokba való belépéshez:

1. A portál Azure Media Services lapján kattintson a **Start** gombra az élő stream esemény elindításához. (A számlázás most kezdődik.)

   ![Start ikon](media/live-events-obs-quickstart/start.png)
1. Állítsa be az **RTMP** -kapcsolót **RTMP**-re.
1. A **bemeneti URL-cím** mezőben másolja a vágólapra az URL-címet.

   ![Bemeneti URL-cím](media/live-events-obs-quickstart/input-url.png)

1. Váltson a OBS alkalmazásra.

1. Kattintson a **Beállítások** gombra a **vezérlők** panelen. Ekkor megnyílik a beállítások lehetőség.

   ![OBS vezérlők panel a kiválasztott beállításokkal](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Válassza a **stream** lehetőséget a **Beállítások** menüből.

1. A **szolgáltatás** legördülő listából válassza az összes megjelenítése, majd az **Egyéni...** lehetőséget.

1. A **kiszolgáló** mezőben illessze be a vágólapra másolt RTMP URL-címet.

1. Írjon be valamit a **stream Key (adatfolyam-kulcs** ) mezőbe.  Nem igazán számít, hogy mi az, de értékkel kell rendelkeznie.

    ![OBS-adatfolyam beállításai](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Válassza a **kimenet** lehetőséget a **Beállítások** menüből.

1. Adja meg a *2* értéket a **kulcsképek intervalluma** mezőben. Ez a kódrészlet hosszát 2 másodpercre állítja. Az alacsony késésű élő kézbesítéshez használja az 1 másodperces értéket.

1. Nem kötelező: állítsa be a **CPU-használat beállításkészletét** a *veryfast* értékre, ha olyan számítógépet használ, amely kevés a feldolgozási teljesítménynél. Igény szerint beállíthatja a kbit/s-ot, ha nem kívánatos hálózati feltételek vannak.

   ![OBS kimeneti beállításai](media/live-events-obs-quickstart/live-event-obs-advanced-output-settings.png)

1. Hagyja változatlanul a beállítások hátralévő részét, és kattintson **az OK** gombra.

### <a name="start-streaming"></a>Streaming indítása

1. A **vezérlők** panelen kattintson a **streaming indítása** lehetőségre.

    ![OBS Start streaming gomb](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

2. Váltson a böngészőben a Azure Media Services Live Event (élő esemény) képernyőre, és kattintson a **lejátszó újratöltésére** szolgáló hivatkozásra. Ekkor látnia kell a streamet az előzetes verzióban.

## <a name="set-up-outputs"></a>Kimenetek beállítása

Ez a rész beállítja a kimeneteket, és lehetővé teszi az élő stream rögzítésének mentését.  

> [!NOTE]
> A kimenet továbbításához a streaming végpontnak futnia kell. Lásd a következőt: az [alapértelmezett folyamatos átviteli végpont szakasz futtatása](#run-the-default-streaming-endpoint) .

1. Válassza a kimenetek **létrehozása** hivatkozást a **kimenetek** videó megjelenítője alatt.
1. Ha szeretné, szerkessze a kimenet nevét a **név** mezőben, hogy a felhasználó-barát legyen, így később könnyebben megtalálhatja.

   ![Kimenet neve mező](media/live-events-wirecast-quickstart/output-name.png)
1. Az összes többi dobozt csak most hagyja.
1. Kattintson a **tovább** gombra a folyamatos átviteli lokátor hozzáadásához.
1. Ha kívánja, változtassa meg a kereső nevét, hogy legyen valami felhasználóbarát.

   ![Lokátor neve mező](media/live-events-wirecast-quickstart/live-event-locator.png)
1. A képernyő minden más elemének kihagyása egyelőre.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="play-the-output-broadcast-by-using-azure-media-player"></a>A kimeneti szórás lejátszása Azure Media Player használatával

1. Másolja a streaming URL-címet a **kimeneti** videolejátszó alá.
1. A böngészőben nyissa meg a [Azure Media Player demót](https://ampdemo.azureedge.net/azuremediaplayer.html).
1. Illessze be a streaming URL-címet Azure Media Player **URL-** mezőjébe.
1. Kattintson a **lejátszó frissítése** gombra.
1. Az élő stream megjelenítéséhez válassza a videó **Lejátszás** ikonját.

## <a name="stop-the-broadcast"></a>A szórás leállítása

Ha úgy gondolja, hogy elég tartalmat továbbított, állítsa le a szórást.

1. A portálon válassza a **Leállítás** lehetőséget.

1. A OBS területen válassza a **továbbítás leállítása** gombot a **vezérlők** panelen. Ez a lépés leállítja a szórást a OBS.

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
