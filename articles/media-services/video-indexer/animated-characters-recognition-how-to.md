---
title: Animált karakterek észlelése Video Indexer használatával
titleSuffix: Azure Media Services
description: Ez a témakör bemutatja, hogyan használható az animált karakterfelismerés a Video Indexer használatával.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.custom: references_regions
ms.topic: how-to
ms.date: 12/07/2020
ms.author: juliako
ms.openlocfilehash: 29dbdb896d1a6063fec277afa33327c84cb2f0cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880927"
---
# <a name="use-the-animated-character-detection-preview-with-portal-and-api"></a>Az animált karakterfelismerés (előzetes verzió) használata a portál és az API használatával 

Azure Media Services Video Indexer támogatja a karakterek észlelését, csoportosítását és felismerését az animált tartalomban, ez a funkció a Azure Portal és az API-n keresztül érhető el. Tekintse át [ezt az áttekintést](animated-characters-recognition.md) ismertető témakört.

Ez a cikk bemutatja, hogyan használható az animált karakterfelismerés a Azure Portal és a Video Indexer API használatával.

## <a name="use-the-animated-character-detection-with-portal"></a>Az animált karakterek észlelése a portál használatával 

A próbaverziós fiókokban a Custom Vision-integrációt Video Indexer felügyeli, megkezdheti az animált karakterek létrehozásának és használatának megkezdését. A próbaverziós fiók használata esetén kihagyhatja a következő ("a Custom Vision fiók összekötése") szakaszt.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Custom Vision-fiók összekötése (csak fizetett fiókok esetében)

Ha Video Indexer fizetős fiókkal rendelkezik, először egy Custom Vision-fiókot kell összekötnie. Ha még nincs Custom Vision fiókja, hozzon létre egyet. További információ: [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

> [!NOTE]
> Mindkét fióknak ugyanabban a régióban kell lennie. A Custom Vision integráció jelenleg nem támogatott a japán régióban.

A Custom Vision fiókjához hozzáféréssel rendelkező fizetős fiókok a modelleket és a címkézett képeket is láthatják. További információ az [osztályozó javítása Custom Visionban](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). 

Vegye figyelembe, hogy a modell betanítását csak Video Indexeron keresztül kell elvégezni, és nem a Custom Vision webhelyén keresztül. 

#### <a name="connect-a-custom-vision-account-with-api"></a>Custom Vision-fiók összekötése API-val 

Az alábbi lépéseket követve összekapcsolhatja Custom Vision fiókját Video Indexerhoz, vagy módosíthatja a jelenleg a Video Indexerhoz csatlakozó Custom Vision fiókot:

1. Tallózással keresse meg a [www.customvision.ai](https://www.customvision.ai) , és jelentkezzen be.
1. Másolja a betanítási és előrejelzési erőforrások kulcsait:

    > [!NOTE]
    > Az összes kulcs megadásához két különálló erőforrásra van szükség a Custom Visionban, egyet a betanításhoz, egyet pedig az előrejelzéshez.
1. További információk megadása:

    * Végpont 
    * Előrejelzési erőforrás azonosítója
1. Tallózással keresse meg és jelentkezzen be a [video Indexerba](https://vi.microsoft.com/).
1. Kattintson a lap jobb felső sarkában látható kérdőjelre, és válassza az API- **hivatkozás** lehetőséget.
1. Győződjön meg arról, hogy előfizetett API Management a **Products (termékek** ) lapra kattintva. Ha van egy API-val csatlakoztatva, folytassa a következő lépéssel, ellenkező esetben: előfizetés. 
1. A fejlesztői portálon kattintson a **teljes API-referenciára** , és keresse meg a **műveleteket**.  
1. Válassza a **kapcsolat Custom Vision fiók (előzetes verzió)** lehetőséget, majd kattintson a **kipróbálás** gombra.
1. Töltse ki a kötelező mezőket, valamint a hozzáférési jogkivonatot, és kattintson a **Küldés** gombra. 

    A Video Indexer hozzáférési token beszerzésével kapcsolatos további információkért látogasson el a [fejlesztői portálra](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?), és tekintse meg a [kapcsolódó dokumentációt](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Ha a hívás visszaadja a 200 OK választ, a fiókja csatlakoztatva van.
1. A kapcsolódás ellenőrzéséhez keresse fel a [video Indexer](https://vi.microsoft.com/)) portált:
1. Kattintson a **tartalmi modell testreszabása** gombra a jobb felső sarokban.
1. Nyissa meg az **animált karakterek** lapot.
1. Ha a Custom Visionban található modellek kezelése gombra kattint, a rendszer átviszi az imént csatlakoztatott Custom Vision-fiókba.

> [!NOTE]
> Jelenleg csak a Video Indexer használatával létrehozott modellek támogatottak. A Custom Vision használatával létrehozott modellek nem lesznek elérhetők. Emellett az ajánlott eljárás az, ha a Video Indexeron keresztül létrehozott modelleket csak az Video Indexer platformon keresztül hozta létre, mert a Custom Visionon végrehajtott módosítások nem szándékolt eredményeket eredményezhetnek.

### <a name="create-an-animated-characters-model"></a>Animált karakteres modell létrehozása

1. Nyissa meg a [Video Indexer](https://vi.microsoft.com/) webhelyét, és jelentkezzen be.
1. Ha testre szeretné szabni a fiókban lévő modelleket, válassza ki a **tartalom modell testreszabása** gombot a lap bal oldalán.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="A tartalmi modell testreszabása Video Indexer":::
1. Nyissa meg a modell testreszabása szakasz **animált karakterek** lapját.
1. Kattintson a **modell hozzáadása** lehetőségre.
1. Adja meg a modell nevét, majd kattintson az ENTER gombra a név mentéséhez.

> [!NOTE]
> Az ajánlott eljárás az, hogy az egyes animált sorozatokhoz egyetlen egyéni víziós modellt lehessen létrehozni. 

### <a name="index-a-video-with-an-animated-model"></a>Videó indexelése animált modellel

A kezdeti betanításhoz töltsön fel legalább két videót. A jó felismerési modell elvárta előtt mindegyiknek 15 percnél hosszabbnak kell lennie. Ha rövidebb epizódokkal rendelkezik, javasoljuk, hogy a betanítás előtt legalább 30 perces videotartalom feltöltését javasolja. Ez lehetővé teszi, hogy egyesítse azokat a csoportokat, amelyek ugyanahhoz a karakterhez tartoznak a különböző jelenetekben és háttérekben, és így növelik az esélyeket, hogy a következő epizódokban szereplő karaktert fogja érzékelni. Egy modell több videón való betanításához (epizód) az összeset ugyanazzal az animációs modellel kell indexelni. 

1. Kattintson a **feltöltés** gombra.
1. Válassza ki a feltölteni kívánt videót (fájlból vagy URL-címről).
1. Kattintson a **Speciális beállítások** elemre.
1. Az **emberek/animált karakterek** területen válassza az **animációs modellek** lehetőséget.
1. Ha az egyik modellt választja, a rendszer automatikusan kiválasztja, és ha több modellel rendelkezik, kiválaszthatja a legördülő menü megfelelő részét.
1. Kattintson a feltöltés gombra.
1. A videó indexelése után a **rendszer az** észlelt karaktereket az észlelések ablaktábla **animált karakterek** szakaszában tekinti meg.

A modell címkézése és betanítása előtt az összes animált karakter neve "Unknown #X" lesz. A modell betanítása után a rendszer felismeri a modellt is.

### <a name="customize-the-animated-characters-models"></a>Az animált karakterek modelljeinek testreszabása

1. Nevezze el a karaktereket a Video Indexerban.

    1. A modell létrehozása után javasolt a csoportok áttekintése Custom Visionban. 
    1. Ha egy animált karaktert szeretne címkézni a videóban, **lépjen az elemzések**   lapra, és kattintson az ****   ablak jobb felső sarkában található Szerkesztés gombra. 
    1.  **Az**   észlelések ablaktáblán kattintson bármelyik észlelt animált karakterre, és módosítsa a nevüket az "ismeretlen #X" értékről egy ideiglenes névre (vagy a korábban a karakterhez rendelt névre). 
    1. Az új név beírása után kattintson az új név melletti pipa ikonra. Ezzel az új nevet a modellben menti Video Indexerban. 
1. Csak fizetett fiókok: Tekintse át a csoportokat Custom Vision 

    > [!NOTE]
    > A Custom Vision fiókjához hozzáféréssel rendelkező fizetős fiókok a modelleket és a címkézett képeket is láthatják. További információ az [osztályozó javítása Custom Visionban](../../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md). Fontos megjegyezni, hogy a modell betanítását csak Video Indexeron keresztül kell elvégezni (az ebben a Topid leírtak szerint), és nem a Custom Vision weboldalán keresztül. 

    1. Nyissa meg Video Indexer **egyéni modellek** lapját, és válassza az **animált karakterek** lapot. 
    1. Kattintson a Szerkesztés gombra ahhoz a modellhez, amellyel a Custom Vision felügyelheti. 
    1. Tekintse át az egyes karakterek csoportjait: 

        * Ha a csoport nem kapcsolódó képeket tartalmaz, ajánlott törölni ezeket a Custom Vision webhelyén. 
        * Ha más karakterhez tartozó képek vannak, módosítsa az adott képek címkéjét. ehhez kattintson a képre, adja hozzá a megfelelő címkét, és törölje a helytelen címkét. 
        * Ha a csoport nem megfelelő, az azt jelenti, hogy főleg nem karakterből álló képeket vagy képeket tartalmaz több karakterből, Custom Vision webhelyén vagy Video Indexer-megállapításokban törölheti. 
        * A csoportosítási algoritmus időnként különböző csoportokba bontja a karaktereket. Ezért ajánlott az összes olyan csoportot megadni, amely ugyanahhoz a karakterhez tartozik (Video Indexer megállapításokban), ami azonnal kiváltja az összes ilyen csoport megjelenését a Custom Vision webhelyén. 
    1. A csoport finomítása után győződjön meg arról, hogy a címkével ellátott kezdeti név tükrözi a csoport karakterét. 
1. A modell betanítása 

    1. Miután befejezte az összes kívánt név szerkesztését, be kell tanítania a modellt. 
    1. Ha egy karaktert betanítanak a modellbe, azt a rendszer a következő, a modellel indexelt videót fogja felismerni. 
    1. Nyissa meg a Testreszabás lapot, és kattintson az **animált karakterek**   fülre, majd kattintson a **vonat** gombra a modell betanításához. A videó közötti kapcsolat megtartása érdekében 
    
Az indexelő és a modell nem tanítja a modellt a Custom Vision webhelyén (a fizetős fiókok hozzáférhetnek Custom Vision webhelyhez), csak Video Indexerban. A betanítást követően minden, a modellel indexelt vagy újraindexelt videó felismeri majd a betanított karaktereket. 

## <a name="delete-an-animated-character-and-the-model"></a>Animált karakter és modell törlése

1. Animált karakter törlése.

    1. Ha törölni szeretne egy animált karaktert a videójában, lépjen az elemzések lapra **,** és kattintson az ablak jobb felső sarkában található **Szerkesztés** gombra.
    1. Válassza ki az animált karaktert, majd kattintson a név alatt található **Törlés** gombra.

    > [!NOTE]
    > Ezzel törli az elemzést ebből a videóból, de a modellre nem lesz hatással.
1. Modell törlése.

    1. A felső menüben kattintson a **tartalmi modell testreszabása** gombra, és válassza az **animált karakterek** lapot.
    1. Kattintson a törölni kívánt modell jobb oldalán a három pontot ábrázoló ikonra, majd a Törlés gombra.
    
    * Fizetős fiók: a modell leválasztása Video Indexer, és nem fogja tudni újracsatlakozni.
    * Próbaverziós fiók: a modellt a rendszer a vámügyi jövőképből is törli. 
    
        > [!NOTE]
        > Egy próbaverziós fiókban csak egy modell használható. A törlés után más modelleket nem lehet betanítani.

## <a name="use-the-animated-character-detection-with-api"></a>Az animált karakterfelismerés használata API-val 

1. Custom Vision-fiók összekötése.

    Ha Video Indexer fizetős fiókkal rendelkezik, először egy Custom Vision-fiókot kell összekötnie. <br/>
    Ha még nincs Custom Vision fiókja, hozzon létre egyet. További információ: [Custom Vision](../../cognitive-services/custom-vision-service/overview.md).

    [Custom Vision-fiókjának összekapcsolásához használja az API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)-t.
1. Hozzon létre egy animált karakterekből álló modellt.

    Használja az [animációs modell létrehozása](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) API-t.
1. Egy videó indexelése vagy újraindexelése.

    Használja az [újbóli indexelés](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) API-t. 
1. Testreszabhatja az animált karakterek modelljeit.

    Használja a [Train Animation Model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) API-t.

### <a name="view-the-output"></a>A kimenet megtekintése

Tekintse meg az animált karaktereket a generált JSON-fájlban.

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

* Az "animációs azonosítás" képesség jelenleg nem támogatott East-Asia régióban.
* A videóban kis vagy nagy méretű karakterek nem azonosíthatók megfelelően, ha a videó minősége gyenge.
* Javasoljuk, hogy az animált karakterek (például egy animált sorozat) esetében használjon modellt.

## <a name="next-steps"></a>Következő lépések

[A Video Indexer áttekintése](video-indexer-overview.md)