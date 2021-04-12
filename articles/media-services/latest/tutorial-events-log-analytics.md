---
title: Media Services események tárolása az Azure-ban Log Analytics
description: Megtudhatja, hogyan tárolhatja Azure Media Services eseményeit az Azure Log Analyticsban.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: 214deb290fc0c0992458c2806644e06ffa00b271
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963444"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Oktatóanyag: Azure Media Services-események tárolása az Azure-ban Log Analytics

## <a name="azure-media-services-events"></a>Események Azure Media Services

Azure Media Services v3 eseményt bocsát ki [Azure Event Grid](monitoring/media-services-event-schemas.md). Az eseményekre számos módon fizethet elő, és tárolhatja őket az adattárakban. Ebben az oktatóanyagban az Media Services eseményekre fog előfizetni a [log app flow](https://azure.microsoft.com/services/logic-apps/)használatával. A rendszer elindítja a logikai alkalmazást az egyes eseményekhez, és az esemény törzsét az Azure Log Analytics tárolja. Ha az események az Azure Log Analytics-ban vannak, más Azure-szolgáltatásokkal is létrehozhat irányítópultokat, figyelőket és riasztásokat ezekről az eseményekről, bár ez az oktatóanyag nem terjed ki erre a célra.

> [!NOTE]
> Hasznos lehet, ha már ismeri az FFmpeg használatát helyszíni kódolóként.  Ha nem, akkor ez rendben van. A videók továbbítására szolgáló parancssor és utasítások a következők:

Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Kód logikai alkalmazás nélküli folyamat létrehozása
> * Előfizetés Azure Media Services Event-témakörökre
> * Események elemzése és tárolása az Azure-ban Log Analytics
> * Események lekérdezése az Azure Log Analytics

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

> * [Azure-előfizetés](how-to-set-azure-subscription.md)
> * Egy [Media Services](account-create-how-to.md) fiók és erőforráscsoport.
> * Az [FFmpeg](https://ffmpeg.org/download.html) telepítése az operációs rendszerhez.
> * [Log Analytics](../../azure-monitor/logs/quick-create-workspace.md) munkaterület

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Előfizetés Media Services eseményre a Logic App használatával

1. Ha még nem tette meg, hozzon létre egy [log Analytics](../../azure-monitor/logs/quick-create-workspace.md) munkaterületet a Azure Portalban. Szüksége lesz a munkaterület-AZONOSÍTÓra és az egyik kulcsra, hogy a böngészőablak nyitva legyen. Ezután nyissa meg a portált egy másik lapon vagy ablakban.

1. Navigáljon a Azure Media Services-fiókjához, és válassza az **események** lehetőséget. Ekkor megjelenik az Azure Media Services eseményekre való feliratkozás összes módszere.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services portál](media/tutorial-events-log-analytics/select-events-01a.png)

1. A logikai alkalmazás létrehozásához kattintson a **Logic apps ikonra** . Ekkor megnyílik a Logic app Designer, ahol létrehozhatja a folyamatot az események rögzítéséhez, és leküldheti azokat Log Analyticsba. 
    > [!div class="mx-imgBorder"]
    > ![Logikai alkalmazás létrehozása](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Válassza a **+ ikont**, válassza ki a használni kívánt bérlőt, majd válassza a bejelentkezés lehetőséget. Ekkor megjelenik a Microsoft bejelentkezési kérése.
    > [!div class="mx-imgBorder"]
    > ![Kapcsolódás Azure Event Grid ](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
 ![ válassza ki a bérlőt](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. A Media Services eseményekre való előfizetéshez válassza a **tovább** lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Csatlakoztatva Azure Event Grid](media/tutorial-events-log-analytics/select-continue-04.png)

1. Az erőforrástípus listában keresse meg a "Microsoft. Media. MediaServices" **értéket** .
    > [!div class="mx-imgBorder"]
    >![Erőforrás-események Azure Media Services](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Válassza ki az **eseménytípus elemet**. Ekkor megjelenik az összes olyan esemény listája, Azure Media Services bocsátja ki. Kiválaszthatja azokat az eseményeket, amelyeket nyomon szeretne követni. Több eseménytípus is felvehető. (Később a logikai alkalmazás folyamatának egy kis módosítását kell elvégeznie, hogy az egyes eseménytípus tárolása külön Log Analytics naplóban történjen, és az eseménytípus nevét dinamikusan propagálja a Log Analytics-napló neveként.)
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services eseménytípus](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Válassza a **Mentés másként** lehetőséget.

1. Adjon nevet a logikai alkalmazásnak.  Az erőforráscsoport alapértelmezés szerint ki van választva. Hagyja meg a többi beállítást a kívánt módon, majd válassza a **Létrehozás** lehetőséget.  A rendszer visszaadja az Azure-Kezdőlap képernyőjét.
    > [!div class="mx-imgBorder"]
    > ![Logikai alkalmazás elnevezési felülete](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Művelet létrehozása

Most, hogy előfizetett az esemény (ek) ra, hozzon létre egy műveletet.

1. Ha a portál visszatér a kezdőképernyőn, térjen vissza az imént létrehozott logikai alkalmazáshoz, és keresse meg az alkalmazás nevére vonatkozó összes erőforrást.

1. Válassza ki az alkalmazást, majd válassza a **Logic app Designer** elemet. Ekkor megnyílik a tervező ablaktábla.

1. Válassza az **+ új lépés** lehetőséget.

1. Mivel az eseményeket az Azure Log Analytics szolgáltatásba kívánja leküldeni, keressen rá az "Azure Log Analytics" kifejezésre, és válassza az "Azure Log Analytics adatgyűjtő" lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics Data Collector](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. A Log Analytics munkaterülethez való kapcsolódáshoz szükség van a munkaterület AZONOSÍTÓJÁRA és az ügynök kulcsára. Nyissa meg a Azure Portal egy új lapon vagy ablakban, navigáljon az oktatóanyag elindítása előtt létrehozott Log Analytics munkaterülethez.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics-munkaterület azonosítója](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. A bal oldali menüben keresse meg az **ügynökök kezelése** elemet, és válassza ki. Ekkor megjelenik a generált ügynök kulcsa.
    > [!div class="mx-imgBorder"]
    > ![Azure Log Analytics-ügynökök kezelése](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Másolja a *munkaterület-azonosítót*.
    > [!div class="mx-imgBorder"]
    > ![Munkaterület-azonosító másolása](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. A másik böngésző lapon vagy ablakban az Azure Log Analytics adatgyűjtője lapon válassza az **adatküldés** lehetőséget, adja meg a kapcsolatok nevét, majd illessze be a *munkaterület* -azonosítót a **munkaterület-azonosító** mezőbe.

1. Térjen vissza a munkaterület-tallózó lapra vagy az ablakba, és másolja a *munkaterület kulcsát*.
    > [!div class="mx-imgBorder"]
    > ![Ügynökök kezelése elsődleges kulcs](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. A másik böngésző lapon vagy ablakban illessze be a *munkaterület* kulcsát a **munkaterület kulcs** mezőjébe.

1. Válassza a **Létrehozás** lehetőséget. Most létrehozza a JSON-kérés törzsét és az egyéni napló nevét.

1. Válassza a **JSON-kérelem törzse** mezőt.  Ekkor megjelenik a **dinamikus tartalom hozzáadására** szolgáló hivatkozás.

1. Válassza a **dinamikus tartalom hozzáadása** lehetőséget, majd válassza a **témakör** lehetőséget.

1. Tegye ugyanezt az **Egyéni napló neveként**.
    > [!div class="mx-imgBorder"]
    > ![Kiválasztott témakör](media/tutorial-events-log-analytics/topic-selected.png)

1. Válassza ki a logikai alkalmazás **kódjának nézetét** . Keresse meg a bemeneteket és a Log-Type vonalakat.
    > [!div class="mx-imgBorder"]
    > ![Két sor kódjának nézete](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Módosítsa az `body` értéket a értékre `"@triggerBody()?['topic']"` `"@{triggerBody()}"` . Ez a teljes üzenet Log Analytics való elemzésére szolgál.

1. Módosítsa a `Log-Type` elemet a következőre: `"@triggerBody()?['topic']"` `"@replace(triggerBody()?['eventType'],'.','')"` . (Ez a "." kifejezést fogja lecserélni, mivel ezek nem engedélyezettek Log Analytics-naplók neveiben.)
    > [!div class="mx-imgBorder"]
    > ![Logic app JSON a módosítás után](media/tutorial-events-log-analytics/changed-lines.png)

1. Kattintson a **Mentés** gombra.

1. Az ellenőrzéshez válassza a **Logic app Designer** elemet.
    > [!div class="mx-imgBorder"]
    > ![A törzs és a függvény lépéseinek ellenőrzése](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Ha megvizsgálja az erőforráscsoport összes erőforrását, egy logikai alkalmazás és két logikai alkalmazás API-összekötő jelenik meg, amelyek közül az egyik az esemény, egy pedig a Log Analytics. Event Grid rendszertémakörökkel kapcsolatos további információkért olvassa el [Event Grid Rendszertémaköröket](../../event-grid/system-topics.md).
    > [!div class="mx-imgBorder"]
    > ![Az erőforráscsoport összes új erőforrásának megjelenítése](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Tesztelés

A tényleges működésének teszteléséhez hozzon létre egy élő eseményt Azure Media Servicesban. Hozzon létre egy RTMP élő eseményt, és használja az FFmpeg-t egy "élő" stream küldésére egy. mp4-mintakód alapján. Az esemény létrehozása után szerezze be az RTMP betöltési URL-címét.

1. A Media Services-fiókban válassza az **élő közvetítés** lehetőséget.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services élő esemény létrehozása](media/tutorial-events-log-analytics/live-event.png)

1. Válassza az **élő esemény hozzáadása** lehetőséget.

1. Írjon be egy nevet az **élő esemény neve** mezőbe. (A **Leírás** mező megadása nem kötelező.)

1. Válassza a **normál** Felhőbeli kódolás lehetőséget.

1. Válassza az **alapértelmezett 720p** lehetőséget a kódolási beállításkészletnél.

1. Válassza az **RTMP** bemeneti protokoll lehetőséget.

1. Válassza az **Igen** lehetőséget az állandó bemeneti URL-címhez.

1. Az **Igen** gombra kattintva elindíthatja az eseményt az esemény létrehozásakor.

    > [!WARNING]
    > A számlázás akkor indul el, ha az Igen lehetőséget választja.  Ha úgy szeretné megvárni, hogy a streamet *csak* az FFmpeg-vel való közvetítés előtt indítsa el, válassza a **nem** lehetőséget, és ne feledje, hogy indítsa el az élő eseményt.

    > [!div class="mx-imgBorder"]
    > ![Élő esemény beállításai](media/tutorial-events-log-analytics/live-event-settings.png)

1. Válassza az **összes jogot a tartalom vagy fájl használatához** ... jelölőnégyzetet.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

1. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.  Ekkor megjelennek az élő események listája, és megjelenik az élő esemény betöltési URL-címe.

1. Másolja a betöltési **URL-címet** a vágólapra.

1. A gyártó nézet megjelenítéséhez válassza ki az **élő eseményt** a listában.

### <a name="stream-with-ffmpeg-cli"></a>Stream az FFmpeg CLI-vel

1. Használja a következő parancssort.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Váltson `<ingestURL>` át a vágólapra másolt betöltési URL-címre.
1. Váltson `<localpathtovideo>` arra a fájl helyi elérési útjára, amelyet az FFmpeg-ből szeretne továbbítani.
1. Adjon hozzá egy egyedi nevet a végén, például: `mystream` .
1. Állítsa be a parancssort úgy, hogy az tükrözze a teszt forrásfájljait és bármely más rendszerváltozót.
1. Futtassa a parancsot. Néhány másodperc elteltével a "producer nézet" lejátszónak adatfolyamot kell kezdenie. (Frissítse a lejátszót, ha a videó nem jelenik meg automatikusan.)

    > [!div class="mx-imgBorder"]
    > ![A megfelelő videók betöltésének ellenőrzése a producer előzetes verziójának lejátszójában](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Az események ellenőrzése

Az élő streamtel Azure Media Services a logikai alkalmazás folyamatát kiváltó különböző eseményeket bocsát ki. Az ellenőrzéshez navigáljon a logikai alkalmazáshoz, és állapítsa meg, hogy vannak-e olyan eseményindítók, amelyeket a Media Services eseményei kilőttek.

1. Navigáljon a Logic app áttekintő oldalára, és tekintse meg a "futtatási előzmények" listát, amely sikeresen befejeződött.
    > [!div class="mx-imgBorder"]
    > ![A sikeres feladatok végrehajtásának ellenőrzése a logikai alkalmazásban](media/tutorial-events-log-analytics/run-history.png)

1. Válasszon ki egy sikeres feladatot. A Futtatás során a rendszer megjeleníti a feladatok részleteit.
1. Válassza az **adatküldés** elemet a kibontásához. Ebben az esetben az `MicrosoftMediaLiveEventEncoderConnected` esemény azt mutatja, hogy rögzítve lett, valamint az elemzett törzs is. Ezt az Azure Log Analytics-munkaterületre küldi a rendszer.
    > [!div class="mx-imgBorder"]
    > ![Információk küldése](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>A naplók ellenőrzése

1. Navigáljon a korábban létrehozott Log Analytics munkaterületre.

1. Válassza a **naplók** lehetőséget.
1. Zárjuk be a példában szereplő lekérdezések előugró ablakát.
1. A rendszer egyéni naplókat listáz. Kattintson a lefelé mutató nyílra a kibontásához. Ekkor megjelenik az esemény neve `MicrosoftMediaLiveEventEncoderConnected` .
1. Válassza ki az esemény nevét a kibontásához.
1. A "szem" ikon kiválasztásakor a lekérdezés eredményének előnézete jelenik meg.
1. Válassza a **Megjelenítés a lekérdezés-szerkesztőben** elemet, majd válassza ki az elemet a **TimeGenerated UTC** -lista elem alatt a kibontásához és a nyers adatok megtekintéséhez.

![Tekintse meg a részletes esemény kimenetét Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Erőforrások törlése

Ha nem szeretné tovább használni az oktatóanyag során létrehozott erőforrásokat, győződjön meg róla, hogy törli az erőforráscsoport összes erőforrását, vagy továbbra is fizetnie kell.

## <a name="next-steps"></a>Következő lépések

Különböző lekérdezéseket hozhat létre, és mentheti őket. Ezek hozzáadhatók az [Azure irányítópulthoz](../../azure-monitor/visualize/tutorial-logs-dashboards.md).