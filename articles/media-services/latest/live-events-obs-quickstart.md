---
title: Élő stream létrehozása a OBS Studióval
description: Megtudhatja, hogyan hozhat létre Azure Media Services élő streamet a portál és a OBS Studio használatával
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/20/2021
ms.openlocfilehash: d52affbdc4dc433c40be687f2e56afae4bcf4c2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104949890"
---
# <a name="create-an-azure-media-services-live-stream-with-obs"></a>Azure Media Services élő stream létrehozása a OBS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a rövid útmutató segítséget nyújt egy Media Services élő esemény létrehozásához a Azure Portal és a Broadcast for Open Broadcasting Studio (OBS) használatával. Feltételezi, hogy rendelkezik Azure-előfizetéssel, és létrehozott egy Media Services fiókot.

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

   ![Adatfolyam-végpontok menüeleme](media/live-events-obs-quickstart/streaming-endpoints.png)
1. Ha az alapértelmezett folyamatos átviteli végpont állapota leáll, válassza ki. Ezzel a lépéssel megtekintheti az adott végpont lapját.
1. Válassza az **Indítás** elemet.

   ![A streaming végpont Start gombja.](media/live-events-obs-quickstart/start.png)

## <a name="set-up-an-azure-media-services-live-stream"></a>Azure Media Services élő stream beállítása

1. Nyissa meg a Azure Media Services fiókot a portálon belül, majd válassza az **élő közvetítés** lehetőséget a **Media Services** listában.

   ![Élő közvetítés hivatkozása.](media/live-events-obs-quickstart/select-live-streaming.png)
1. Válassza az **élő esemény hozzáadása** lehetőséget az új élő közvetítési esemény létrehozásához.

   ![Élő esemény ikonjának hozzáadása](media/live-events-obs-quickstart/add-live-event.png)
1. Adja meg az új esemény nevét (például *TestLiveEvent*) az **élő esemény neve** mezőben.

   ![Élő esemény neve mező](media/live-events-obs-quickstart/live-event-name.png)
1. A **Leírás** mezőben adja meg az esemény opcionális leírását.
1. Válassza a **pass-through – nincs felhőalapú kódolási** beállítás lehetőséget.

   ![Felhőalapú kódolási lehetőség.](media/live-events-obs-quickstart/cloud-encoding.png)
1. Válassza az **RTMP** lehetőséget.
1. Győződjön meg arról, hogy a **nincs** lehetőség van kiválasztva az **élő esemény indításához**, hogy elkerülje az élő esemény számlázását, mielőtt a rendszer készen áll. (A számlázás az élő esemény indításakor kezdődik.)

   ![Élő esemény megkezdése lehetőség.](media/live-events-obs-quickstart/start-live-event-no.png)
1. A beállítások áttekintéséhez kattintson a **felülvizsgálat + létrehozás** gombra.
1. Válassza a **Létrehozás** gombot az élő esemény létrehozásához. Ezután visszatér az élő események listájára.
1. Válassza ki a létrehozott élő eseményre mutató hivatkozást. Figyelje meg, hogy az esemény leáll.
1. Tartsa meg a lapot a böngészőben. Később ismét vissza fogunk térni.

## <a name="set-up-a-live-stream-by-using-obs-studio"></a>Élő stream beállítása a OBS Studio használatával

A OBS egy alapértelmezett jelenettel kezdődik, de nincsenek bemenetek kijelölve.

   ![OBS alapértelmezett képernyő](media/live-events-obs-quickstart/live-event-obs-default-screen.png)

### <a name="add-a-video-source"></a>Videó forrásának hozzáadása

1. Az új forrásoldali eszköz kiválasztásához válassza a **források** panel **Hozzáadás** ikonját. Ekkor megnyílik a **források** menü.

1. Válassza ki a **videó rögzítése eszközt** a forrás eszköz menüjéből. Ekkor megnyílik a **Forrás létrehozása/kiválasztása** menü.

   ![OBS-források menü kiválasztva.](media/live-events-obs-quickstart/live-event-obs-video-device-menu.png)

1. Jelölje be a **meglévő választógomb hozzáadása** gombot, majd kattintson **az OK gombra**. Ekkor megnyílik a **videoeszköz tulajdonságai** menü.

   ![Új OBS menü megnyitása meglévő kijelölés hozzáadásával.](media/live-events-obs-quickstart/live-event-obs-new-video-source.png)

1. Az **eszköz** legördülő listából válassza ki a szóráshoz használni kívánt videó bemenetet. Hagyja a többi beállítást egyelőre, és kattintson az **OK gombra**. A bemeneti forrás hozzá lesz adva a **források** panelhez, és a videó bemeneti nézete megjelenik az **előnézet** területen.

   ![OBS kamera beállításai](media/live-events-obs-quickstart/live-event-surface-camera.png)

### <a name="add-an-audio-source"></a>Hangforrás hozzáadása

1. Az új forrásoldali eszköz kiválasztásához válassza a **források** panel **Hozzáadás** ikonját. Ekkor megnyílik a forrás eszköz menüje.

1. Válassza a **hangbemenet rögzítése** lehetőséget a forrás eszköz menüjében. Ekkor megnyílik a **Forrás létrehozása/kiválasztása** menü.

   ![OBS-források menü a kiválasztott hangeszközzel.](media/live-events-obs-quickstart/live-event-obs-audio-device-menu.png)

1. Jelölje be a **meglévő választógomb hozzáadása** gombot, majd kattintson **az OK gombra**. Ekkor megnyílik a **hangbemenet rögzítése menü tulajdonságai** .

   ![OBS hangforrása a meglévő kijelölések hozzáadásával.](media/live-events-obs-quickstart/live-event-obs-new-audio-source.png)

1. Az **eszköz** legördülő listából válassza ki a szóráshoz használni kívánt hangrögzítési eszközt. Hagyja a többi beállítást egyelőre, és kattintson az OK gombra. A hangrögzítési eszköz hozzá lesz adva a hangkeverő panelhez.

   ![OBS hangeszköz kiválasztása legördülő lista](media/live-events-obs-quickstart/live-event-select-audio-device.png)

### <a name="set-up-streaming-and-advanced-encoding-settings-in-obs"></a>A streaming és a speciális kódolási beállítások beállítása a OBS-ben

A következő eljárásban visszatérhet Azure Media Services a böngészőben a bemeneti URL-cím másolásához a kimeneti beállításokba való belépéshez:

1. A portál Azure Media Services lapján kattintson a **Start** gombra az élő stream esemény elindításához. (A számlázás most kezdődik.)

   ![Start ikon.](media/live-events-obs-quickstart/start.png)
1. Állítsa be az **RTMP** -kapcsolót **RTMP**-re.
1. A **bemeneti URL-cím** mezőben másolja a vágólapra az URL-címet.

   ![Bemeneti URL-cím.](media/live-events-obs-quickstart/input-url.png)

1. Váltson a OBS alkalmazásra.

1. Kattintson a **Beállítások** gombra a **vezérlők** panelen. Ekkor megnyílik a beállítások lehetőség.

   ![A OBS vezérlők panel a kiválasztott beállításokkal.](media/live-events-obs-quickstart/live-event-obs-settings.png)

1. Válassza a **stream** lehetőséget a **Beállítások** menüből.

1. A **szolgáltatás** legördülő listából válassza az összes megjelenítése, majd az **Egyéni...** lehetőséget.

1. A **kiszolgáló** mezőben illessze be a vágólapra másolt RTMP URL-címet.

1. Írjon be valamit a **stream Key (adatfolyam-kulcs** ) mezőbe.  Nem igazán számít, hogy mi az, de értékkel kell rendelkeznie.

    ![OBS-adatfolyam beállításai.](media/live-events-obs-quickstart/live-event-obs-stream-settings.png)

1. Válassza a **kimenet** lehetőséget a **Beállítások** menüből.

1. Válassza ki a **kimeneti mód** legördülő menüt az oldal tetején, és válassza a **speciális** lehetőséget az összes rendelkezésre álló kódoló-beállítás eléréséhez.

1. A kódoló beállításához válassza a **folyamatos átvitel** lapot.

1. Válassza ki a megfelelő kódolót a rendszer számára.  Ha a hardver támogatja a GPU-gyorsítást, válassza az NVIDIA **NVENC** h. 264 vagy az Intel **QuickSync** h. 264 elemet. Ha a rendszer nem rendelkezik támogatott GPU-val, válassza az **x264** szoftver kódoló lehetőséget.

#### <a name="x264-encoder-settings"></a>X264 kódoló beállításai

1. Ha az **x264** -kódolás lehetőséget választotta, jelölje be a **kimenet újraméretezése** jelölőnégyzetet. Ha standard (720P) élő eseményt használ, válassza a 1920 × 1080 lehetőséget, ha Media Services vagy 1280x720 Premium Live-eseményt használ.  Ha átmenő élő eseményt használ, választhatja az összes rendelkezésre álló felbontást is.

1. Állítsa a **sebességet a 1500** kbps és 4000 kbps közötti tetszőleges értékre. Ha standard kódolású élő eseményt használ a 720P-ban, ajánlott 2500 kbps. Ha 1080P Premium Live-eseményt használ, 4000 kbps ajánlott. Előfordulhat, hogy az elérhető CPU-képességek és a hálózat sávszélessége alapján szeretné beállítani a sebességet a kívánt minőségi beállítás eléréséhez.

1. Adja meg a *2* értéket a **kulcsképek intervalluma** mezőben. Az érték 2 másodpercre állítja be a kulcs keretének intervallumát, amely a Media Services HLS vagy KÖTŐJELen keresztül továbbított töredékek végső méretét vezérli. A kulcs keretének intervallumát ne állítsa 4 másodpercnél nagyobb értékre.  Ha a szórás nagy késést mutat, mindig ellenőrizze vagy tájékoztassa az alkalmazás felhasználóit, hogy mindig 2 másodpercre állítsa be ezt az értéket. Ha kisebb késleltetésű élő kézbesítést próbál elérni, beállíthatja, hogy ez az érték 1 másodpercnél rövidebb legyen.

1. Nem kötelező: állítsa be a CPU-használat beállításkészletét **veryfast** , és futtasson néhány kísérletet, hogy megtudja, a helyi CPU képes-e kezelni a bitrátát és a készletet elég terheléssel. Próbálja meg elkerülni azokat a beállításokat, amelyek a 80%-nál nagyobb átlagos PROCESSZORt eredményeznek az élő közvetítés során felmerülő problémák elkerülése érdekében. A minőség javítása érdekében a **gyorsabb** és **gyors** előre beállított beállításokkal tesztelheti a CPU-korlátozásokat.

   ![OBS x264-kódoló beállításai](media/live-events-obs-quickstart/live-event-obs-x264-settings.png)

1. Hagyja változatlanul a beállítások hátralévő részét, és válassza az **OK gombot**.

#### <a name="nvidia-nvenc-encoder-settings"></a>NVIDIA NVENC-kódoló beállításai

1. Ha a **NVENC** GPU-kódolás lehetőséget választotta, jelölje be a **kimenet újraméretezése** jelölőnégyzetet, és válassza a 1920 × 1080 lehetőséget, ha prémium szintű élő eseményt használ a Media Services-ban, vagy 1280x720, ha standard (720p) élő eseményt használ. Ha átmenő élő eseményt használ, választhatja az összes rendelkezésre álló felbontást is.

1. Állítsa a **sebesség vezérlőelemet** a CBR értékre az állandó bitráta-arány szabályozásához.

1. Állítsa a **bitrátát** bárhol, 1500 kbps és 4000 kbps között. Ha standard kódolású élő eseményt használ a 720P-ban, ajánlott 2500 kbps. Ha 1080P Premium Live-eseményt használ, 4000 kbps ajánlott. A kívánt minőségi beállítás eléréséhez dönthet úgy, hogy az elérhető CPU-képességek és sávszélesség alapján módosítja a hálózatot.

1. Állítsa a **kulcsképek intervallumát** 2 másodpercre a fentiekben leírtak szerint az x264 beállítások alatt. Ne haladja meg a 4 másodpercet, mivel ez jelentős hatással lehet az élő közvetítés késésére.

1. Állítsa be az **előre beállított** értéket az alacsony késleltetésű, Low-Latency teljesítményre vagy a Low-Latency minőségi értékre a helyi GÉPEN lévő CPU-sebességtől függően. Ezekkel a beállításokkal kísérletezhet a minőség és a CPU kihasználtsága közötti legjobb egyensúly elérése érdekében a saját hardverén.

1. Ha nagyobb teljesítményű hardveres konfigurációt használ, állítsa a **profilt** "Main" vagy "High" (magas) értékre.

1. Hagyja üresen a **megjelenő** jelet. Ha nagyon nagy teljesítményű géppel rendelkezik, ezt megtekintheti.

1. Hagyja üresen a **pszicho vizualizáció finomhangolását** . Ha nagyon nagy teljesítményű géppel rendelkezik, ezt megtekintheti.

1. Állítsa a **GPU** -t 0-ra, hogy automatikusan eldöntse, melyik GPU-t kell lefoglalni. Ha szükséges, korlátozhatja a GPU-használatot.

1. A **maximális B-képkockák** beállítása 2 értékre

   ![OBS NVidia NVidia NVENC GPU kódoló beállításai.](media/live-events-obs-quickstart/live-event-obs-nvidia-settings.png)

#### <a name="intel-quicksync-encoder-settings"></a>Intel QuickSync-kódoló beállításai

1. Ha az Intel **QuickSync** GPU-kódolás lehetőséget választotta, jelölje be a **kimenet újraméretezése** jelölőnégyzetet, és válassza a 1920 × 1080 lehetőséget, ha prémium szintű élő eseményt használ a Media Services-ban, vagy 1280x720, ha standard (720p) élő eseményt használ. Ha átmenő élő eseményt használ, választhatja az összes rendelkezésre álló felbontást is.

1. Állítsa a **cél használatát** "kiegyensúlyozott" értékre, vagy módosítsa a processzor és a GPU együttes terhelése alapján. Szükség szerint módosítsa a processzort, és kísérletezzen a maximális CPU-kihasználtság 80%-ban átlagosan a hardver által előállítható minőségi minőség érdekében. Ha nagyobb mértékben korlátozott hardverre van szüksége, tesztelje a "Fast" (gyors) vagy a "nagyon gyors" kifejezést, ha teljesítménnyel kapcsolatos problémákat tapasztal.

1. Ha nagyobb teljesítményű hardveres konfigurációt használ, állítsa a **profilt** "Main" vagy "High" (magas) értékre.

1. Állítsa a **kulcsképek intervallumát** 2 másodpercre a fentiekben leírtak szerint az x264 beállítások alatt. Ne haladja meg a 4 másodpercet, mivel ez jelentős hatással lehet az élő közvetítés késésére.

1. Állítsa a **sebesség vezérlőelemet** a CBR értékre az állandó bitráta-arány szabályozásához.

1. Állítsa a **bitrátát** bárhol, 1500 és 4000 kbps között.  Ha standard kódolású élő eseményt használ a 720P-ban, ajánlott 2500 kbps. Ha 1080P Premium Live-eseményt használ, 4000 kbps ajánlott. A kívánt minőségi beállítás eléréséhez dönthet úgy, hogy az elérhető CPU-képességek és sávszélesség alapján módosítja a hálózatot.

1. Állítsa a **késést** az "alacsony" értékre.

1. Állítsa a **B képkockákat** 2 értékre.

1. Hagyja üresen a **szubjektív videó fejlesztéseit** .

   ![OBS Intel QuickSync GPU-kódoló beállításai.](media/live-events-obs-quickstart/live-event-obs-intel-settings.png)

### <a name="set-audio-settings"></a>Hangbeállítások megadása

A következő eljárásban módosítani fogja a hangkódolás beállításait.

1. Válassza a kimenet – >hang fület a beállítások területen.

1. Állítsa az 1. számú **Hangfrekvenciát** 128 kbps-ra.

   ![OBS az audió bitráta beállításait.](media/live-events-obs-quickstart/live-event-obs-audio-output-panel.png)

1. Válassza a hang fület a beállítások területen.

1. Állítsa a **mintavételezési sebességet** 44,1 kHz-re.

   ![OBS hangmintavételezési beállítások.](media/live-events-obs-quickstart/live-event-obs-audio-sample-rate-settings.png)

### <a name="start-streaming"></a>Streaming indítása

1. A **vezérlők** panelen kattintson a **streaming indítása** lehetőségre.

    ![OBS indítása gomb.](media/live-events-obs-quickstart/live-event-obs-start-streaming.png)

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
