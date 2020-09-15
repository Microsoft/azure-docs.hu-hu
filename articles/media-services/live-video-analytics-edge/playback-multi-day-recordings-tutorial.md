---
title: Több napos felvétel lejátszása – Azure
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Media Service API-kat többnapos folyamatos videofelvételek lejátszásához.
ms.topic: tutorial
ms.date: 05/27/2020
ms.openlocfilehash: e01c8603869f17ef2d68a39861f11818a4cea975
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530573"
---
# <a name="tutorial-playback-of-multi-day-recordings"></a>Oktatóanyag: több napos felvétel lejátszása  

Ez az oktatóanyag a [folyamatos videofelvételek](continuous-video-recording-concept.md) (CVR) oktatóanyagán alapul. Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Media Service API-kat a többnapos folyamatos videofelvételek a felhőből való lejátszásához. 

Ez hasznos lehet olyan forgatókönyvek esetén, mint például a közbiztonság, ahol egy kamerából több napra (vagy hetekre) kell rögzíteni a felvételek felvételét, és alkalmanként meg kell figyelni a felvételek adott részeit.

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Futtassa a minta alkalmazást, amely bemutatja, hogyan böngészhet a többnapos felvétel tartalma
> * A rögzítés kiválasztott részeinek megtekintése

## <a name="suggested-pre-reading"></a>Javasolt előzetes olvasás  

Javasoljuk, hogy olvassa el a következő dokumentációs oldalakat:

* [Élő videó-elemzések IoT Edge áttekintése](overview.md)
* [Élő videó-elemzések IoT Edge terminológiában](terminology.md)
* [A Media Graph koncepciója](media-graph-concept.md)
* [Folyamatos videófelvétel](continuous-video-recording-concept.md) 
* [Útmutató: felvételek lejátszása](playback-recordings-how-to.md)
* [Oktatóanyag: folyamatos videofelvétel](continuous-video-recording-tutorial.md)

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be a [CVR-oktatóanyagot](continuous-video-recording-tutorial.md). Ez az oktatóanyag és a kapcsolódó API-k az [oktatóanyagban](continuous-video-recording-tutorial.md) tárgyaltak: a folyamatos videofelvételek az 5 percnél hosszabb felvételekre vonatkoznak, és ha nem, akkor érdemes 5 órát rögzíteni a videóból. A felvételek tallózásához használt API-k a legjobban nagy számú felvételsel vannak kimutatva.
* Azt javasoljuk, hogy futtassa ezt az oktatóanyagot, miközben az [oktatóanyag: folyamatos videó rögzítése](continuous-video-recording-tutorial.md) még mindig fut – vagyis a videó a felhőbe való rögzítése még folyamatban van.

## <a name="run-the-sample"></a>Minta futtatása 

A [CVR oktatóanyag](continuous-video-recording-tutorial.md)részeként létrehozott egy Media Service-fiókot. Ebben az oktatóanyagban ehhez a fiókhoz teljes API-hozzáféréssel kell rendelkeznie. A [hitelesítő adatok beolvasása az Media Services API-hoz való hozzáféréshez](../latest/access-api-howto.md?tabs=portal) című cikkben ismertetett lépéseket követve hozhat létre egyszerű szolgáltatást. A következőhöz hasonló Azure Portal JSON-blokkot kell tudnia:

```
{
    "AadClientId": "<<INSERT_AZURE_AD_APP_ID_HERE>>",
    "AadSecret": "<<INSERT_AZURE_AD_APP_SECRET_HERE>>",
    "AadTenantDomain": "<<YOUR_TENANT_DOMAIN>>",
    "AadTenantId": "<<YOUR_TENANT_ID>>",
    "AccountName": "<<YOUR_ACCOUNT_NAME>>",
    "Location": "<<YOUR_REGION_NAME>>",
    "ResourceGroup": "<<YOUR_RESOURCE_GROUP>>",
    "SubscriptionId": "<<YOUR_SUBSCRIPTION_ID>>",
    "ArmAadAudience": "https://management.core.windows.net",
    "ArmEndpoint": "https://management.azure.com"
}
```

Ezután a Visual Studio Code-ban nyissa meg az src/AMS-Asset-Player elemet. Ez a mappa tartalmazza az oktatóanyaghoz szükséges fájlokat. Nyissa meg a appsettings.jsfájlt, és másolja a tartalmát egy új fájlba, appsettings.development.jsbe. Végezze el a következő módosításokat az utóbbi fájlon:

```
  "AMS" : {
    "subscriptionId" : "Use value of SubscriptionId above",
    "resourceGroup" : "Use value of ResourceGroup above",
    "accountId" : "Use value of AccountName above",
    "aadTenantId" : "Use value of AadTenantId above",
    "aadClientId" : "Use value of AadClientId above",
    "aadSecret" : "Use value of AadSecret above"
} 
```

A Visual Studio Code-ban a bal oldalon (vagy a CTRL + SHIFT + D) a Futtatás ikonra kattintva megtekintheti az elérhető alkalmazásokat:

![Képernyőfelvétel: a Visual Studio Code egy menüje, amelyen a Futtatás elem van kiválasztva.](./media/playback-multi-day-recordings-tutorial/run.png)
 
Válassza ki az AMS Asset Player alkalmazást a legördülő listából az alább látható módon, és nyomja le az F5 billentyűt a hibakeresés megkezdéséhez.

![Képernyőfelvétel: egy menü megjelenítése a Visual Studio Code-ban az AMS Asset Player kiválasztásával.](./media/playback-multi-day-recordings-tutorial/debug.png)

A minta alkalmazás létrehozza és elindítja az alapértelmezett böngésző alkalmazást, és megnyitja az AMS-eszköz Player-lapját.

> [!NOTE]
> A böngésző biztonsági beállításaitól függően figyelmeztető üzenet jelenhet meg. Mivel a weblap helyileg fut, dönthet úgy, hogy figyelmen kívül hagyja a figyelmeztetést.

Az AMS-eszköz felhasználója megkéri, hogy adja meg egy Media Service-eszköz nevét. Az oktatóanyagban a videó rögzítéséhez használt eszköz nevét kell használnia [: folyamatos videofelvétel](continuous-video-recording-tutorial.md).

Az eszköz nevének beírása és a Küldés elküldése után a lejátszó kódja betölti a folyamatos átviteli URL-címet. További információkért lásd: útmutató [a felvételek lejátszásához](playback-recordings-how-to.md). Ha a javasolt módon továbbra is rögzíti az adategységet, a lejátszó észlelni fogja, és megkísérli a lejátszást a rögzített videó legfrissebb részébe. A Player bal felső sarkában látható a timestamp (UTC). Az alábbi képernyőképen vegye figyelembe, hogy az "élő" gomb van kiválasztva.

![Adatfolyam](./media/playback-multi-day-recordings-tutorial/assetplayer1.png)
 
A lejátszó jobb oldalán láthatja a vezérlőket az Archívum tallózásához. A vezérlőben szereplő évek, hónapok és dátumok a availableMedia API használatával vannak feltöltve a útmutatóban ismertetett [módon: a felvételek lejátszása](playback-recordings-how-to.md).
Ha a nap kibontásakor a CVR-oktatóanyagot több órán keresztül futtatja, a következőhöz hasonló eredményt fog látni:

![Archívum tallózása](./media/playback-multi-day-recordings-tutorial/results.png)

Az oktatóanyagban szereplő videó-hírcsatorna forrása egy MKV-fájl. Ha a RSTP-szimulátor (lásd: [Live555 Media Server](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555)) eléri a fájl végét, akkor véget ér az adatfolyam. A Media Graph RTSP-forrás csomópontja észleli ezt, és újból létrehozza a kapcsolatot, és a videó folytatódik. A fájl és az újrakapcsolás között a rögzített archívumban van egy rés, amely új bejegyzésként jelenik meg a availableMedia eredményei között.

![Results (Eredmények)](./media/playback-multi-day-recordings-tutorial/assetplayer2.png)
 
Ha a listában szereplő bármelyik bejegyzésre kattint, az alkalmazás létrehoz egy streaming URL-címet a megfelelő szűrővel, például https://{hostname}/{locatorId}/Content. ISM/manifest (Format = mpd-Time-CSF, kezdő időpont = éééé-hh-NNTÓÓ: PP: MM). A lejátszó betölti ezt az URL-címet, és a lejátszás a kívánt időpontban fog megkezdődni.

Azt is megteheti, hogy meghatározott kezdési és befejezési időpontokat használ az oldal alján található vezérlőkkel. A availableMedia-hívás eredményeit a jobb oldalon látható módon, a kezdő és a befejező időpontok megengedett értékeire mutató útmutatóként használhatja. Az időpontok és a befejezési szűrők részletes megkötéseit dokumentáljuk a útmutatóban [: a felvételek lejátszása](playback-recordings-how-to.md). Az időértékek kiválasztása után ha a Submit (küldés) gombra kattint, az alkalmazás betölti a lejátszót egy streaming URL-címmel, például: https://{hostname}/{locatorId}/Content. ISM/manifest (Format = mpd-Time-CSF, kezdő időpont = éééé-hh-NNTÓÓ: PP: MM, Befejezés = éééé-hh-NNTÓÓ: PP: mm)

## <a name="next-steps"></a>Következő lépések

[Esemény-alapú videofelvétel a felhőbe és a felhőből való lejátszásra](event-based-video-recording-tutorial.md)
