---
title: Kattintson az Analytics Auto-Collection beépülő modul Application Insights JavaScript SDK-hoz
description: Az Analytics automatikus gyűjtési beépülő moduljának telepítése és használata Application Insights JavaScript SDK-hoz.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: lagayhar
ms.openlocfilehash: 5ad3e1a5a4ff47fe3d5fee8b8bc79235838995b8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593630"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Kattintson az Analytics Auto-Collection beépülő modul Application Insights JavaScript SDK-hoz

Ez a beépülő modul automatikusan nyomon követi a weblapok eseményeit, és a HTML-elemek adatattribútumait használja az Event telemetria feltöltéséhez.

## <a name="getting-started"></a>Első lépések

A felhasználók az NPM-on keresztül állíthatják be a Click Analytics automatikus gyűjtés beépülő modulját.

### <a name="npm-setup"></a>NPM-telepítő

NPM-csomag telepítése:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="how-to-effectively-use-the-plugin"></a>A beépülő modul hatékony használata

1. A Click eseményekről generált telemetria-adatok tárolása a `customEvents` Azure Portal Application Insights szakaszában történik.
2. A `name` customEvent az alábbi szabályok alapján tölti fel:
    1.  A rendszer a (z `id` `data-*-id` ) customEvent nevet használja. Ha például a HTML elemre kattintott, az "adatminta-azonosító" = "Button1" attribútummal rendelkezik, akkor a "Button1" érték lesz a customEvent neve.
    2. Ha nincs ilyen attribútum, és ha a `useDefaultContentNameOrId` beállítása `true` a konfigurációra van beállítva, akkor a rendszer az elem HTML-attribútumát `id` vagy a tartalom nevét fogja használni a customEvent neveként.
    3. Ha a `useDefaultContentNameOrId` értéke false (hamis), akkor a customEvent neve "not_specified" lesz.

    > [!TIP]
    > A Javaslatunk igaz értékre van állítva `useDefaultContentNameOrId` az értelmes adat létrehozásához.  
3. `parentDataTag` két dolgot tesz:
    1. Ha ez a címke megtalálható, a beépülő modul beolvassa az `data-*` attribútumokat és az értékeket a rákattintott elem összes SZÜLŐ HTML-eleméből.
    2. A hatékonyság növelése érdekében a beépülő modul ezt a címkét jelölőként használja, amikor a rendszer leállítja a DOM (Document Object Model) további feldolgozását.
    
    > [!CAUTION]
    > `parentDataTag`A használat után az SDK megkezdi a szülő címkék keresését a teljes alkalmazásban, és nem csak az Ön által használt HTML-elemet.
4. `customDataPrefix` a felhasználó által megadott értéknek mindig a `data-` következővel kell kezdődnie, például: `data-sample-` . A HTML-ben a `data-*` globális attribútumok egy egyéni adatattribútumok nevű osztályt alkotnak, amely lehetővé teszi a tulajdonosi adatok cseréjét a HTML és a DOM-képviseletek között a parancsfájlok alapján. A régebbi böngészők (Internet Explorer, Safari) a nem értelmezhető attribútumokat fogják eldobni, kivéve, ha ezekkel kezdődnek `data-` .

    Előfordulhat, hogy a `*` -ben az `data-*`  alábbi korlátozásokkal helyettesítheti az [XML-nevek termelési szabályát](https://www.w3.org/TR/REC-xml/#NT-Name) követő bármelyik nevet:
    - A név nem kezdődhet "XML" karakterrel, bármilyen esetben a következő betűket használja.
    - A név nem tartalmazhat pontosvesszőt (U + 003A).
    - A név nem tartalmazhat nagybetűket.

## <a name="configuration"></a>Konfiguráció

| Név                  | Típus                               | Alapértelmezett | Description                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| az autocapture           | boolean                            | true    | Automatikus rögzítési konfiguráció.                                                                                                         |
| visszahívási              | [IValueCallback](#ivaluecallback)  | null    | Visszahívások konfigurálása.                                                                                                                 |
| pageTags              | sztring                             | null    | Lap címkéi                                                                                                                               |
| dataTags              | [ICustomDataTags](#icustomdatatags)| null    | Egyéni adatcímkék, amelyek az adatrögzítéshez használt alapértelmezett címkék felülbírálására szolgálnak.                                                           |
| urlCollectHash        | boolean                            | hamis   | Engedélyezi az értékek naplózását az URL-cím "#" karaktere után.                                                                          |
| urlCollectQuery       | boolean                            | hamis   | Engedélyezi az URL-cím lekérdezési karakterláncának naplózását.                                                                                      |
| behaviorValidator     | Függvény                           | null  | Az érték érvényesítéséhez használandó visszahívási függvény `data-*-bhvr` . További információért lépjen a [behaviorValidator szakaszra](#behaviorvalidator).|
| defaultRightClickBhvr | karakterlánc (vagy) száma                 | ''      | Alapértelmezett viselkedési érték, ha a jobb gombbal az esemény történt. A rendszer felülbírálja ezt az értéket, ha az elem rendelkezik az `data-*-bhvr` attribútummal. |
| dropInvalidEvents     | boolean                            | hamis   | Olyan események eldobásának jelzője, amelyek nem rendelkeznek hasznos kattintási értékkel.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Név               | Típus     | Alapértelmezett | Leírás                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Függvény | null    | Az alapértelmezett pageName-rögzítési viselkedés felülbírálására szolgáló függvény.                           |
| pageActionPageTags | Függvény | null    | Egy visszahívási függvény, amely kibővíti az pageAction esemény során összegyűjtött alapértelmezett pageTags.  |
| contentName        | Függvény | null    | Egy visszahívási függvény a testreszabott contentName feltöltéséhez.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Név                      | Típus    | Alapértelmezett   | HTML-kódban használandó alapértelmezett címke |   Description                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | hamis     | N/A         |A standard HTML attribútumot gyűjti a contentName, ha egy adott elem nem az alapértelmezett customDataPrefix van címkézve, vagy ha a felhasználó nem biztosít customDataPrefix. |
| customDataPrefix          | sztring  | `data-`   | `data-*`| A megadott előtaggal címkézett elemek tartalmának és értékének automatikus rögzítése. Például a ( `data-*-id` ) `data-<yourcustomattribute>` HTML-címkékben is használható.   |
| aiBlobAttributeTag        | sztring  | `ai-blob` |  `data-ai-blob`| A beépülő modul támogatja a JSON blob attribútumot az egyes `data-*` attribútumok helyett. |
| metaDataPrefix            | sztring  | null      | N/A  | A HTML-fej meta-elemének és tartalmának automatikus rögzítése a rögzítéskor megadott előtaggal. Például `custom-` a HTML meta-címkében is használható. |
| captureAllMetaDataContent | boolean | hamis     | N/A   | A HTML-fejléc összes meta elemének neve és tartalma automatikus rögzítése. Az alapértelmezett érték a false (hamis). Ha engedélyezve van, a rendszer felülbírálja a megadott metaDataPrefix. |
| parentDataTag             | sztring  | null      |  N/A  | Leállítja a DOM bejárását a tartalom nevének és az elemek értékének rögzítéséhez, amikor a rendszer ezt a címkét észlelte. `data-<yourparentDataTag>`Használhatja például a HTML-címkéket.|
| dntDataTag                | sztring  | `ai-dnt`  |  `data-ai-dnt`| Az attribútummal rendelkező HTML-elemeket a beépülő modul figyelmen kívül hagyja a telemetria-adatrögzítéshez.|

### <a name="behaviorvalidator"></a>behaviorValidator

A behaviorValidator függvények automatikusan ellenőrzik, hogy a kódban szereplő címkézett viselkedések megfelelnek-e egy előre definiált listának. Ez biztosítja, hogy a címkézett viselkedések összhangban legyenek a vállalata által bevezetett besorolással. Nem szükséges vagy várható, hogy a legtöbb Azure Monitor ügyfél ezt fogja használni, de speciális forgatókönyvekhez is elérhető. A bővítmény részeként három különböző behaviorValidator visszahívási függvény van kitéve. A felhasználók azonban használhatják a saját visszahívási funkcióit, ha az elérhető függvények nem oldják meg a követelményt. A cél az, hogy saját viselkedési adatstruktúrát vigyen fel, a beépülő modul ezt az érvényesítő függvényt használja, miközben Kinyeri a viselkedéseket az adatcímkékből.

| Név                   | Leírás                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Akkor használja ezt a visszahívási függvényt, ha a viselkedési adatstruktúra karakterláncok tömbje.|
| BehaviorMapValidator   | Akkor használja ezt a visszahívási funkciót, ha a viselkedési adatstruktúra egy szótár.       |
| BehaviorEnumValidator  | Akkor használja ezt a visszahívási függvényt, ha a viselkedési adatstruktúra enumerálás.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Mintavételezés a behaviorValidator használatával

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Minta alkalmazás

[Egyszerű webalkalmazás a Click Analytics automatikus gyűjtési beépülő moduljában engedélyezve](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a [GitHub-tárházat](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) és a [NPM csomagot](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) a Click Analytics automatikus gyűjtemény beépülő moduljában.
- Használja az [események elemzését használati élményben](usage-segmentation.md) a leggyakoribb kattintások és a szeletek elérhető dimenziók alapján történő elemzéséhez.
- Keresse meg az adatokat a CustomEvents tábla customDimensions attribútumában a Content (tartalom) mezőben a [log Analyticsban](../logs/log-analytics-tutorial.md#write-a-query). További útmutatásért lásd: [Sample app](https://go.microsoft.com/fwlink/?linkid=2152871) .
- Hozzon létre egy [munkafüzetet](../visualize/workbooks-overview.md) egyéni vizualizációk létrehozásához, kattintson az adatelemre.
