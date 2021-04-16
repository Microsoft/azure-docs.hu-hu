---
title: Animált karakterészlelés Video Indexer segítségével
titleSuffix: Azure Media Services
description: Ez a módszer bemutatja, hogyan használható az animált karakterészlelés Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: e80699a043d4c18a1bc7ba75ce58c6972fae0fad
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531551"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Az animált karakterészlelés (előzetes verzió) használata a portállal és az API-val 

Azure Media Services Video Indexer támogatja az animált tartalmakban szereplő karakterek észlelését, csoportosítását és felismerését, ez a funkció a Azure Portal API-n keresztül érhető el. Tekintse [át ezt az áttekintő](animated-characters-recognition.md) témakört.

Ez a cikk bemutatja, hogyan használható az animált karakterészlelés a Azure Portal és a Video Indexer API-val.

## <a name="use-the-animated-character-detection-with-portal"></a>Animált karakterészlelés használata a portálon 

A próbafiókok esetében a Custom Vision integrációt a Video Indexer, elkezdheti létrehozni és használni az animált karaktereket használó modellt. Ha a próbafiókot használja, kihagyhatja a következő ("Custom Vision fiók csatlakoztatása") szakaszt.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>A Custom Vision csatlakoztatása (csak fizetős fiókok esetén)

Ha fizetős Video Indexer van, először csatlakoztatnia kell egy Custom Vision fiókot. Ha még nem rendelkezik fiókkal Custom Vision, hozzon létre egyet. További információ: [Custom Vision.](../../cognitive-services/custom-vision-service/overview.md)

> [!NOTE]
> Mindkét fióknak ugyanabban a régióban kell lennie. A Custom Vision integrációja jelenleg nem támogatott a japán régióban.

Azok a fizetős fiókok, amelyek hozzáféréssel Custom Vision fiókjukhoz, itt láthatják a modelleket és a címkézett képeket. További információ az [osztályozó javításáról](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)a Custom Vision. 

Vegye figyelembe, hogy a modell betanítását csak a Video Indexer kell, a Custom Vision nem. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Fiók csatlakoztatása Custom Vision API-val 

Az alábbi lépéseket követve csatlakoztathatja Custom Vision-fiókot Video Indexer-hoz, vagy módosíthatja a Custom Vision-fiók aktuálisan csatlakoztatott Video Indexer:

1. Nyissa meg a [www.customvision.ai](https://www.customvision.ai) és jelentkezzen be.
1. Másolja ki a betanítás és az előrejelzési erőforrások kulcsait:

    > [!NOTE]
    > Ahhoz, hogy az összes kulcsot meg tudja adni, két különálló erőforrással kell Custom Vision, egy a betanításhoz és egy az előrejelzéshez.
1. Adjon meg egyéb információkat:

    * Végpont 
    * Előrejelzési erőforrás azonosítója
1. Keresse meg a webhelyet, és jelentkezzen [be Video Indexer.](https://vi.microsoft.com/)
1. Kattintson az oldal jobb felső sarkában található kérdőjelre, és válassza az **API-referencia lehetőséget.**
1. A Termékek lapra való API Management ellenőrizze, hogy **előfizetett-e** a szolgáltatásra. Ha rendelkezik csatlakoztatott API-val, folytathatja a következő lépéssel, egyéb esetben pedig iratkozzon fel. 
1. A fejlesztői portálon kattintson a **Complete API Reference (API-referencia befejezése) elemre,** és keresse meg **a Operations (Műveletek) adatokat.**  
1. Válassza **a Connect Custom Vision Account (PREVIEW) (Csatlakozás Custom Vision fiókhoz (ELŐZETES VERZIÓ) lehetőséget, majd** kattintson a Try it **(Próbálja ki) gombra.**
1. Töltse ki a kötelező mezőket és a hozzáférési jogkivonatot, majd kattintson a **Küldés gombra.** 

    A hozzáférési jogkivonat lekért Video Indexer a fejlesztői [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account-Access-Token)portálon, és tekintse meg a [vonatkozó dokumentációt.](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api)  
1. Ha a hívás 200 OK választ ad vissza, a fiók csatlakoztatva lesz.
1. A kapcsolat ellenőrzéséhez lépjen a Video Indexer [)](https://vi.microsoft.com/)portálra:
1. Kattintson a **Tartalommodell testreszabása** gombra a jobb felső sarokban.
1. Ugrás az Animált **karakterek lapra.**
1. Miután rákattintott a Modellek kezelése elemre a Custom Vision, a Custom Vision csatlakoztatott fiókba kerül.

> [!NOTE]
> Jelenleg csak a Video Indexer létrehozott modellek támogatottak. A Custom Vision létrehozott modellek nem lesznek elérhetők. Emellett az ajánlott eljárás az, hogy a Video Indexer-ban létrehozott modelleket csak az Video Indexer-platformon keresztül szerkessze, mivel a Custom Vision módosítások nem szándékolt eredményeket okozhatnak.

### <a name="create-an-animated-characters-model"></a>Animált karaktermodell létrehozása

1. Nyissa meg a [Video Indexer](https://vi.microsoft.com/) webhelyét, és jelentkezzen be.
1. A fiókban lévő modell testreszabásához válassza a Tartalommodell testreszabása gombot az oldal bal oldalán. 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Tartalommodell testreszabása a Video Indexer":::
1. A modell **testreszabása szakaszban válassza** az Animált karakterek lapot.
1. Kattintson a **Modell hozzáadása elemre.**
1. Nevezze el a modellt, majd kattintson az Enter gombra a név mentéshez.

> [!NOTE]
> Az ajánlott eljárás egy egyéni látásmodell létrehozása minden animált sorozathoz. 

### <a name="index-a-video-with-an-animated-model"></a>Videó indexelése animált modellel

A kezdeti betanításhoz töltsön fel legalább két videót. A jó felismerési modellre való számítás előtt lehetőleg 15 percnél hosszabbnak kell lennie. Ha rövidebb az idő, javasoljuk, hogy legalább 30 percnyi videótartalmat töltsön fel a betanítás előtt. Ez lehetővé teszi, hogy egyesítsen olyan csoportokat, amelyek különböző jelenetekből és hátterekből származó azonos karakterhez tartoznak, és ezáltal növeli annak esélyét, hogy észlelni fogja a karaktert a következő indexelés során. Ahhoz, hogy több videón (videón) betanítsa a modellt, mindegyiket azonos animációs modellel kell indexelni. 

1. Kattintson a **Feltöltés gombra.**
1. Válassza ki a feltölteni kívánt videót (fájlból vagy URL-címből).
1. Kattintson a **Speciális beállítások elemre.**
1. A **Személyek / Animált karakterek alatt válassza az** **Animációs modellek lehetőséget.**
1. Ha van egy modellje, az automatikusan ki lesz választva, és ha több modellje van, kiválaszthatja a megfelelőt a legördülő menüből.
1. Kattintson a feltöltés gombra.
1. A videó indexelése után az észlelt karakterek az Insights panel **Animált** karakterek **szakaszában adatokat fognak** látni.

A modell címkézése és betanítás előtt minden animált karakter neve "Unknown #X". A modell betanítás után ezek is felismerhetők lesznek.

### <a name="customize-the-animated-characters-models"></a>Animált karaktereket bemutató modellek testreszabása

1. Nevezze el a karaktereket a Video Indexer.

    1. Miután a modell létrehozta a karaktercsoportot, javasoljuk, hogy tekintse át ezeket a csoportokat Custom Vision. 
    1. Ha animált karaktert szeretné megjelölni a videóban, kattintson az **Elemzések** lapra, és kattintson az ablak jobb felső sarkában található Szerkesztés    ****   gombra. 
    1. Az **Elemzések panelen** kattintson bármelyik észlelt animált karakterre, és módosítsa a nevüket "Ismeretlen #X" helyett egy ideiglenes névre (vagy a karakterhez korábban hozzárendelt   névre). 
    1. Miután begépelte az új nevet, kattintson az új név melletti pipa ikonra. Ezzel menti az új nevet a modellbe a Video Indexer. 
1. Csak fizetős fiókok esetén: Tekintse át a Custom Vision 

    > [!NOTE]
    > Azok a fizetős fiókok, amelyek hozzáféréssel Custom Vision fiókjukhoz, itt láthatják a modelleket és a címkézett képeket. További információ az [osztályozó javításáról](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)a Custom Vision. Fontos megjegyezni, hogy a modell betanítását csak az Video Indexer -on keresztül kell (a topidban leírtak szerint), és nem a Custom Vision keresztül. 

    1. A képernyő **Egyéni modellek lapjára Video Indexer** animált karakterek **lapot.** 
    1. Kattintson az Edit (Szerkesztés) gombra ahhoz a modellhez, amelynél dolgozik, hogy kezelni tudja Custom Vision. 
    1. Tekintse át az egyes karaktercsoportokat: 

        * Ha a csoport nem kapcsolódó képeket tartalmaz, javasoljuk, hogy törölje ezeket a Custom Vision webhelyén. 
        * Ha más karakterhez tartozó képek vannak, módosítsa a címkét ezeken a képeken. Ehhez kattintson a képre, adja hozzá a megfelelő címkét, és törölje a nem megfelelő címkét. 
        * Ha a csoport helytelen, ami azt jelenti, hogy főként nem karakterből származó képeket vagy több karakterből származó képeket tartalmaz, törölheti a adatokat egy Custom Vision webhelyen vagy a Video Indexer elemzésben. 
        * A csoportosítási algoritmus időnként felosztja a karaktereket különböző csoportokra. Ezért ajánlott az azonos karakterhez tartozó csoportoknak ugyanazt a nevet adni (az Video Indexer Insightsban), ami azonnal azt okozhatja, hogy az összes csoport ugyanúgy jelenik meg, mint a Custom Vision webhelyén. 
    1. A csoport finomhanglása után győződjön meg arról, hogy a kezdeti név, amelyen címkézett, tükrözi a csoportban szereplő karaktert. 
1. A modell betanítása 

    1. Miután végzett az összes kívánt név szerkesztésével, betanítja a modellt. 
    1. Miután betanított egy karaktert a modellbe, a rendszer felismeri azt a modellel indexelt következő videóban. 
    1. Nyissa meg a testreszabási oldalt, kattintson az **Animált karakterek** lapra, majd kattintson a Betanítás gombra a   modell betanítása érdekében. **** A videó közötti kapcsolat biztosítása érdekében 
    
Az indexelő és a modell nem csak az Custom Vision webhelyén betanítja a modellt (a fizetős fiókok Custom Vision webhelyhez férnek hozzá), csak Video Indexer. A betanítás után a modellel indexelt vagy újraindexelt videók felismerik a betanított karaktereket. 

## <a name="delete-an-animated-character-and-the-model"></a>Animált karakter és a modell törlése

1. Animált karakter törlése.

    1. Ha törölni szeretne egy animált karaktert a videóelemzésekből,  kattintson az **Elemzések** lapra, és kattintson az ablak jobb felső sarkában található Szerkesztés gombra.
    1. Válassza ki az animált karaktert, majd kattintson a **törlés gombra** a nevük alatt.

    > [!NOTE]
    > Ezzel törli a videóból származó elemzéseket, de nincs hatással a modellre.
1. Modell törlése.

    1. A felső menüben kattintson a **Tartalommodell testreszabása** gombra, és válassza az **Animált karakterek** lapot.
    1. Kattintson a törölni kívánt modelltől jobbra található három pontra, majd a Törlés gombra.
    
    * Fizetős fiók: a modell le lesz választva a Video Indexer és nem fog tudni újracsatlakozni.
    * Próbafiók: a modell is törölve lesz a Rifi visionből. 
    
        > [!NOTE]
        > A próbafiókban csak egy modellt használhat. A törlés után nem tud más modelleket betanítni.

## <a name="use-the-animated-character-detection-with-api"></a>Az animált karakterészlelés használata AZ API-val 

1. Csatlakozás Custom Vision fiókhoz.

    Ha fizetős Video Indexer van, először csatlakoztatnia kell egy Custom Vision fiókot. <br/>
    Ha még nem rendelkezik fiókkal Custom Vision, hozzon létre egyet. További információ: [Custom Vision.](../../cognitive-services/custom-vision-service/overview.md)

    [Csatlakoztassa a Custom Vision fiókját az API-val.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Connect-Custom-Vision-Account)
1. Animált karaktermodell létrehozása.

    Használja az [animációs modell létrehozása](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Animation-Model) API-t.
1. Videó indexelése vagy újraindexelése.

    Használja [az újraindexelési API-t.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) 
1. Az animált karakterek modelljeinek testreszabása.

    Használja az [animációs modell betanítás](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Train-Animation-Model) API-ját.

### <a name="view-the-output"></a>A kimenet megtekintése

Tekintse meg az animált karaktereket a létrehozott JSON-fájlban.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Korlátozások

* Az "animációazonosítás" funkció jelenleg nem támogatott a East-Asia régióban.
* Előfordulhat, hogy a videóban kis vagy távol szereplő karakterek nem azonosíthatók megfelelően, ha a videó minősége gyenge.
* Azt javasoljuk, hogy animált karakterekből (például animált sorozatonként) használjon modellt.

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)