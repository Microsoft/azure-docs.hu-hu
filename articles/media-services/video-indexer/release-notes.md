---
title: Azure Media Services Video Indexer kibocsátási megjegyzések | Microsoft Docs
description: Annak érdekében, hogy naprakész maradjon a legújabb fejlesztésekkel, ez a cikk a legújabb frissítéseket biztosítja a Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 03/30/2021
ms.author: juliako
ms.openlocfilehash: b3602d421718cbd1de3509751491ec6db65b1b01
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532898"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer kibocsátási megjegyzések

>Az RSS-hírcsatorna olvasójára másolva és beillesztésével értesítést kap arról, hogy mikor kell újralátni az oldalon a `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` frissítéseket.

A legújabb fejlesztésekkel való naprakészen maradás érdekében ez a cikk a következő információkkal rendelkezik:

* A legújabb kiadások
* Ismert problémák
* Hibajavítások
* Elavult funkciók

## <a name="march-2021"></a>2021. március

### <a name="audio-analysis"></a>Hangelemzés 

A hangelemzés mostantól további új hangszolgáltatásokat kínál különböző árakon. Az új **Alapszintű hangelemzés** előzetes beállítás alacsony költségű lehetőséget biztosít, amely csak a beszédátírást, a fordítást és a kimeneti feliratok formázását teszi lehetővé. Az **Alapszintű hang beállítás** két különálló mérőeszközt hoz létre a számlán, köztük egy átiratsort, valamint egy külön sort a feliratok és feliratok formázásához. A díjszabásról további információt a [díjszabást Media Services oldalon.](https://azure.microsoft.com/pricing/details/media-services/)

Az újonnan hozzáadott csomag a fájl indexelhető vagy újraindexelhető, ha az Alapszintű hang beállítás speciális beállítását választja (a Videó   ->   **és hangindexelés** legördülő listában).

### <a name="new-developer-portal"></a>Új fejlesztői portál 

Video Indexer új fejlesztői portállal rendelkezik, próbálja ki az új Video Indexer API-kat, és keresse meg az összes releváns forrást egy helyen: [GitHub-adattár,](https://github.com/Azure-Samples/media-services-video-indexer) [Stack Overflow](https://stackoverflow.com/questions/tagged/video-indexer), Video Indexer kapcsolódó blogbejegyzésekkel, Video Indexer [gyakori](faq.md)kérdések, User [Voice](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016) a visszajelzések és a funkciók meghozása érdekében, valamint a widgetek kódminkáira mutató ["CodePen"](https://codepen.io/videoindexer) hivatkozás. [](https://api-portal.videoindexer.ai/) [](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Speciális testreszabási képességek az elemzés widgethez 

Az SDK mostantól beágyazható Video Indexer saját szolgáltatásba, és testre szabhatja annak stílusát és adatait. Az SDK támogatja a standard Video Indexer insights widgetet és egy teljes mértékben testreszabható elemzési vezérlőt. A kódminta a [GitHub Video Indexer ban érhető el.](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization) Ezzel a speciális testreszabási lehetőséggel a megoldásfejlesztő egyéni stílust alkalmazhat, és saját AI-adatokat hozhat létre az ügyféltől, és ezt bemutatja az elemzés widgetben (elemzésekkel vagy Video Indexer nélkül). 

### <a name="video-indexer-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Indexer az USA északi középső régiója, az USA nyugati régiója és Közép-Kanada területén 

Most már létrehozhat egy fizetős Video Indexer az USA északi középső régiójában, az USA nyugati régiójában és a Közép-Kanada régióban
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Új forrásnyelvek támogatása a beszédfelismeréshez (STT), a fordításhoz és a kereséshez 

Video Indexer már támogatja az STT-t, a fordítást és a keresést dán ('da-DK'), norvég('nb-NO'), svéd('sv-SE'), finn('fi-FI'), kanadai francia ('fr-CA'), thai('th-TH'), arab ('ar-BH', "ar-EG", "ar-IQ", "ar-JO", "ar-FOG", "ar-LB", "ar-OM", "ar-QA", "ar-S", és "ar-SY"), és török('tr-TR'). Ezek a nyelvek az API-ban és a Video Indexer is elérhetők. 
 
### <a name="search-by-topic-in-video-indexer-website"></a>Keresés témakör szerint az Video Indexer webhelyén 

Most már használhatja a keresési funkciót az Video Indexer webhelyének tetején, és adott témaköröket [Video Indexer](https://www.videoindexer.ai/account/login) videók keresésére. 

## <a name="february-2021"></a>2021. február

### <a name="multiple-account-owners"></a>Több fióktulajdonos 

A fióktulajdonosi szerepkör hozzá lett adva a Video Indexer. Felhasználókat adhat hozzá, módosíthat és távolíthat el; a szerepkörük módosítása. A fiókok megosztásával kapcsolatos részletekért lásd: [Felhasználók meghívása.](invite-users.md)

### <a name="audio-event-detection-public-preview"></a>Hangesemények észlelése (nyilvános előzetes verzió)

> [!NOTE]
> Ez a funkció csak próbafiókok esetében érhető el. 

Video Indexer a következő hanghatásokat észleli a tartalom nem beszéd szegmensében: aggodalom, üvegrezúdolás, riasztás, siren, robbanás, kutyagörgetés, becsergető, becsergő, közösségi visszacsatolás (sivár, klónozás és booing) és csend. 

Az újonnan hozzáadott hang hatással van a funkcióra, amikor indexeli a fájlt a Speciális beállítás Speciális hangbeállítás kiválasztásával  ->   (a Videó + hangindexelés alatt). A standard indexelés csak csendet **és** közösségi **reakciót tartalmaz.** 

Az **előző hanghatás-modellben** található klónozási eseménytípus most már a közösségi reagálás eseménytípusának  egy részét kinyeri.

Ha úgy dönt, **hogy** a videóval kapcsolatos elemzéseket a Video Indexer [webhelyén](https://www.videoindexer.ai/) látja, az Audio Effects (Hangeffektusok) adatokat láthatja az oldalon.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Hangesemények észlelése":::

### <a name="named-entities-enhancement"></a>A nevestűdő entitások fejlesztése  

A személyek és a tartózkodási hely kinyert listája ki lett bontva, és általánosságban frissült. 

Emellett a modell most már olyan személyeket és helyeket is tartalmaz a környezetben, amelyek nem híresek, például egy "Sam" vagy "Kezdőlap" a videóban. 

## <a name="january-2021"></a>2021. január

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer az USA kormányzati felhőszolgáltatásában van üzembe helyezni 

Most már létrehozhat egy fizetős Video Indexer az USA kormányzati felhőjében Virginia és Arizona régióban. Video Indexer ingyenes próbaverzió nem érhető el az említett régióban. További információt a dokumentációban Video Indexer talál. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer közép-india régióban üzembe helyezett 

Most már létrehozhat egy fizetős Video Indexer az közép-indiai régióban. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Új sötét mód a Video Indexer webhelyhez

A Video Indexer webhely már sötét módban is elérhető. A sötét mód engedélyezéséhez nyissa meg a Beállítások panelt, és váltsa a **Sötét mód beállítást.** 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Sötét mód beállítása":::

## <a name="december-2020"></a>2020. december

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer Nyugat-Svájcban és Észak-Svájcban van telepítve

Most már létrehozhat egy fizetős Video Indexer Nyugat- és Svájc északi régiójában.

## <a name="october-2020"></a>2020. október

### <a name="animated-character-identification-improvements"></a>Az animált karakterek azonosításának fejlesztései  

Video Indexer egyéni látásmódokkal való integráció révén támogatja az animált tartalmakban szereplő karakterek észlelését, csoportosítását és Cognitive Services felismerését. Az észlelés és a karakterek felismerésében jelentős fejlesztéseket vezettünk be az AI-algoritmusban, mivel az eredményül kapott elemzés pontossága és az azonosított karakterek jelentősen javultak.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>A Video Indexer webhely hitelesítésének tervezett módosításai

2021. március 1-től nem fog tudni bejelentkezni az [Video Indexer-webhely](https://www.videoindexer.ai/) [](video-indexer-use-apis.md) fejlesztői portáljára a Facebook vagy a LinkedIn használatával.

A regisztrációt és bejelentkezést a következő szolgáltatók egyikének használatával tudja majd: Azure AD, Microsoft és Google.

> [!NOTE]
> A Video Indexer a LinkedInhez és a Facebookhoz csatlakoztatott fiókok 2021. március 1. után nem lesznek elérhetők. 
> 
> Érdemes [meghívni egy](invite-users.md) Azure AD-, Microsoft- vagy Google-e-mailt, amely a Video Indexer-fiók tulajdonában van, hogy továbbra is hozzáférjen. A támogatott szolgáltatók további tulajdonosát is hozzáadhatja a meghívással leírtak [szerint.](invite-users.md) <br/>
> Másik lehetőségként létrehozhat egy fizetős fiókot, és mibatálhatja az adatokat.

## <a name="august-2020"></a>2020. augusztus

### <a name="mobile-design-for-the-video-indexer-website"></a>Mobileszközök tervezése a Video Indexer webhelyhez

A Video Indexer webhely felhasználói élménye már támogatja a mobileszközöket. A felhasználói felület rugalmasan alkalmazkodik a mobil képernyő méretének megfelelően (a testreszabási felhasználói felületeket kivéve). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Akadálymentesség javításai és hibajavítások 

A WCAG (webes tartalom akadálymentességére vonatkozó irányelvek) részeként a Video Indexer webhely a Microsoft Accessibility Standards részeként a C osztályhoz igazodik. A billentyűzetnavigációs, a programozott hozzáféréssel és a képernyőolvasóval kapcsolatos számos hiba és fejlesztés megoldódott. 

## <a name="july-2020"></a>2020. július

### <a name="ga-for-multi-language-identification"></a>GA a többnyelvű azonosításhoz

A többnyelvű azonosítást áthelyezték az előzetes verzióból az GA-be, és készen állnak a hatékony használatra.

Az előzetes verzióról az ga ga verzióra való áttérés nem befolyásolja a díjszabást.

### <a name="video-indexer-website-improvements"></a>Video Indexer webhely fejlesztései

#### <a name="adjustments-in-the-video-gallery"></a>A videógaléria módosításai

Új keresősáv lett hozzáadva a mély elemzések kereséséhez további szűrési képességekkel. A keresési eredmények is ki voltak javítva.

Új listanézet, amely lehetővé teszi a videóarchívum több fájlból történő rendezését és kezelését.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Új panel az egyszerű kijelöléshez és konfiguráláshoz

Az egyszerű kijelöléshez és a felhasználói konfigurációhoz hozzáadott oldalpanel egyszerű és gyors fiók-létrehozást és -megosztást, valamint a konfiguráció beállítását teszi lehetővé.

Az oldalpanel a felhasználói beállításokhoz és segítséghez is használható.

## <a name="june-2020"></a>2020. június

### <a name="search-by-topics"></a>Keresés témakörök szerint

Most már használhatja a keresési API-t az adott témakörökben (csak API-kban) található videók keresésére.

A témakörök a (választható paraméter) részeként `textScope` vannak hozzáadva. A [részletekért lásd: API.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos)  

### <a name="labels-enhancement"></a>A címkék fejlesztése

A címkecímkéző frissítve lett, és most már több azonosítható vizualizációcímkét tartalmaz.

## <a name="may-2020"></a>2020. május

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer az USA keleti régiója régiója

Most már létrehozhat egy fizetős Video Indexer az USA keleti régiójában.
 
### <a name="video-indexer-url"></a>Video Indexer URL-cím

Video Indexer regionális végpontok egységesek voltak, hogy csak a www-val kezdjenek. Nincs szükség műveletelemre.

Mostantól elérheti a www.videoindexer.ai, hogy widgeteket ágyazhat be, vagy Video Indexer webalkalmazásba.

A wus.videoindexer.ai a www webhelyre lesz átirányítva. További információ: [Beágyazási Video Indexer widgetek az alkalmazásokban.](video-indexer-embed-widgets.md)

## <a name="april-2020"></a>2020. április

### <a name="new-widget-parameters-capabilities"></a>Új widgetparaméter-képességek

Az **Insights** widget új paramétereket tartalmaz: `language` és `control` .

A **Lejátszó** vezérlő új `locale` paraméterrel rendelkezik. A `locale` és a paraméter is a lejátszó `language` nyelvét vezéreli.

További információkért lásd a [widgettípusok szakaszt.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Új lejátszófelület

Egy új, frissített kialakítású lejátszófelület.

### <a name="prepare-for-upcoming-changes"></a>Felkészülés a közelgő változásokra

* Jelenleg a következő API-k egy fiókobjektumot adnak vissza:

    * [Fizetős fiók létrehozása](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Fiók lekérte](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Fiókok hitelesítésének le kérése](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Fiókok le szolgáltatása jogkivonattal](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Az Account objektum rendelkezik egy mezővel, amely a webhely `Url` [Video Indexer mutat.](https://www.videoindexer.ai/)
Fizetős fiókok esetén a mező jelenleg a nyilvános webhely helyett egy belső `Url` URL-címre mutat.
A következő hetekben ezt módosítjuk, és az Video Indexer (próbaverziós és [fizetős)](https://www.videoindexer.ai/) fiók url-címét fogjuk visszaadni.

    Ne használja a belső URL-címeket, a nyilvános [API Video Indexer kell használnia.](https://api-portal.videoindexer.ai/)
* Ha Video Indexer URL-címeket ágyaz be az alkalmazásokba, és az URL-címek nem az [Video Indexer](https://www.videoindexer.ai/) webhelyre vagy az Video Indexer API-végpontra ( ) mutatnak, hanem egy regionális végpontra (például ), újragenerálják az `https://api.videoindexer.ai` `https://wus2.videoindexer.ai` URL-címeket.

   Ezt a következő két okkal lehet megtenni:

    * Az URL-cím lecserélve az Video Indexer widget API-jére (például az [insights widgetre)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget)mutató URL-címre
    * Új beágyazott URL Video Indexer létrehozása az Video Indexer webhely használatával:
         
         Kattintson **a Lejátszás** gombra a videó oldalának **&lt; / &gt;** > a Beágyazás gombra, > másolja az URL-címet az alkalmazásba:
   
    A regionális URL-címek nem támogatottak, és a következő hetekben le lesznek tiltva.

## <a name="january-2020"></a>2020. január
 
### <a name="custom-language-support-for-additional-languages"></a>Egyéni nyelvi támogatás további nyelvekhez

Video Indexer támogatja az egyéni nyelvi modelleket a , a és a `ar-SY` `en-UK` esetében `en-AU` (csak API esetén).
 
### <a name="delete-account-timeframe-action-update"></a>Fiók időkeret-műveletének frissítése

A Fiók törlése művelet 48 óra helyett 90 napon belül törli a fiókot.
 
### <a name="new-video-indexer-github-repository"></a>Új Video Indexer GitHub-adattár

A GitHub Video Indexer új, különböző projekteket, első lépések útmutatót és kódmintákat is tartalmaz: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger-frissítés

Video Indexer **hitelesítéseket** és műveleteket egyetlen Video Indexer [OpenAPI-specifikációban (swagger) használ.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation)  Az API-kat a fejlesztői portálon [Video Indexer találhatják meg.](https://api-portal.videoindexer.ai/)

## <a name="december-2019"></a>2019. december

### <a name="update-transcript-with-the-new-api"></a>Átirat frissítése az új API-val

Frissítse az átirat egy adott szakaszát az [Update-Video-Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) API használatával.

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>A fiók konfigurációjának kijavítva a Video Indexer portálról

Most már frissítheti a Media Services konfigurációját, hogy önsegítő megoldásokat használva megoldhatja a következő problémákat: 

* helytelen Azure Media Services erőforrás
* jelszóváltozások
* Media Services erőforrások előfizetések között áthelyezve  

A fiók konfigurációjának kijavításához a Video Indexer portálon navigáljon a Beállítások > Fiók lapra (tulajdonosként).

### <a name="configure-the-custom-vision-account"></a>A Custom Vision-fiók konfigurálása

Konfigurálja a Custom Vision-fiókot fizetős fiókokon a Video Indexer portál használatával (korábban ezt csak az API támogatta). Ennek érdekében jelentkezzen be a Video Indexer portálra, és válassza a Modell testreszabása és > animált karakterek > lehetőséget. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Jelenetek, felvételek és kulcsképek – mostantól egyetlen elemzés panelen

A jelenetek, felvételek és kulcsképek mostantól egyetlen elemzésben egyesíthetők a könnyebb használat és navigáció érdekében. A kívánt jelenet kiválasztásakor láthatja, milyen felvételekből és kulcskockákból áll. 

### <a name="notification-about-a-long-video-name"></a>Értesítés egy hosszú videónévről

Ha egy videó neve hosszabb 80 karakternél, a Video Indexer egy leíró hibát jelez a feltöltéskor.

### <a name="streaming-endpoint-is-disabled-notification"></a>A streamvégpont le van tiltva értesítés

Ha a streamvégpont le van tiltva, Video Indexer egy leíró hibaüzenet jelenik meg a lejátszó oldalán.

### <a name="error-handling-improvement"></a>Hibakezelés fejlesztései

A 409-es állapotkódot a rendszer visszaadja a Videó újraindexelése és a Videóindex frissítése [API-kból](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) arra az esetre, ha egy videó aktív indexelést használ, hogy megelőzhető legyen a jelenlegi újraindexelési módosítások véletlen felülírása. [](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index)

## <a name="november-2019"></a>2019. november
 
* A koreai egyéni nyelvi modellek támogatása

    A Video Indexer mostantól támogatja az egyéni nyelvi modelleket koreai () nyelven az `ko-KR` API-ban és a portálon is. 
* A beszédfelismerés (STT) által támogatott új nyelvek

    Video Indexer API-k mostantól támogatják az STT-t az arab Levantine-ban (ar-SY), az egyesült királyságbeli dialektust (en-GB) és az angol ausztráliai dialektust (en-AU).
    
    Videófeltöltés esetén a zh-HANS helyett a zh-CN van támogatva, de a zh-CN ajánlott és pontosabb.
    
## <a name="october-2019"></a>2019. október
 
* Animált karakterek keresése a katalógusban

    Animált karakterek indexelhetőek, és most már megkeresheti őket a fiók videogalajában. További információ: [Animált karakterek felismerése.](animated-characters-recognition.md)

## <a name="september-2019"></a>2019. szeptember
 
Több fejlesztést jelentettünk be az IBC 2019-ben:
 
* Animált karakterfelismerés (nyilvános előzetes verzió)

    Képesség a csoportos ad-felismerési karakterek animált tartalomban való felismerésére egyéni látássokkal való integráció révén. További információ: [Animált karakterészlelés.](animated-characters-recognition.md)
* Többnyelvű azonosítás (nyilvános előzetes verzió)

    Különböző nyelvű szegmensek felismerése a hangsávon, és többnyelvű átirat létrehozása ezek alapján. Kezdeti támogatás: angol, spanyol, német és francia. További információért tekintse meg a [többnyelvű tartalom automatikus azonosításával és átírásával](multi-language-identification-transcription.md) foglalkozó cikket.
* Nevestitás-kinyerés személyekhez és helyhez

    Kinyeri a márkákat, helyeket és személyeket a beszédből és a vizuális szövegből természetes nyelvi feldolgozás (NLP) segítségével.
* Szerkesztői felvétel típusbesorolása

    A felvételek felcímkézése szerkesztői típusokkal, például közeli, közepes, két oltó, beltéri, küllős stb. További információ: [Szerkesztői felvétel típusának észlelése.](scenes-shots-keyframes.md#editorial-shot-type-detection)
* Témakör-következtetési fejlesztések – mostantól a 2. szintet fedik le
    
    A témakör következtetési modellje mostantól támogatja az IPTC-taxonóniának részletesebbségét. Részletes információkért olvassa el [Azure Media Services új, AI-alapú innovációról.](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)

## <a name="august-2019"></a>2019. augusztus
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer üzembe helyezett Egyesült Királyság déli régiója

Most már létrehozhat egy fizetős Video Indexer az Egyesült Királyság déli régiójában.

### <a name="new-editorial-shot-type-insights-available"></a>Új szerkesztői felvétel típusú elemzések érhetők el

A videófelvételek új címkéi szerkesztői "felvételtípusokat" érhetők el, amelyek a tartalom-létrehozási munkafolyamatban használt gyakori szerkesztői kifejezésekkel azonosíthatók, például: szélsőséges közeli, közeli, széles, közepes, két oltó, küllős, beltéri, bal és jobb arc (A JSON-fájlban érhető el).

### <a name="new-people-and-locations-entities-extraction-available"></a>Új személyek és helyek entitások kinyerése elérhető

Video Indexer azonosítja a megnevezett helyeket és személyeket természetes nyelvi feldolgozással (NLP) a videó OCR-ében és átiratában. Video Indexer gépi tanulási algoritmussal felismeri, hogy mikor hívnak ki egy videóban bizonyos helyeket (például az Tower-et) vagy személyeket (például John Doe-t).

### <a name="keyframes-extraction-in-native-resolution"></a>Kulcsképkockák kinyerése natív felbontásban

A Video Indexer által kinyert kulcsképek a videó eredeti felbontásában érhetők el.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA egyéni arcmodellek betanítása képekről

Az arcok betanítása az előzetes verzióról az ga verzióra (API-n keresztül és a portálon keresztül) áthelyezett képekről történik.

> [!NOTE]
> Az előzetes verzióról az ga ga verzióra való áttérés nem befolyásolja a díjszabást.

### <a name="hide-gallery-toggle-option"></a>Katalógus elrejtése váltógomb

A felhasználó dönthet úgy, hogy elrejti a katalógus lapját a portálon (hasonlóan ahhoz, hogy elrejtse a minták lapját).
 
### <a name="maximum-url-size-increased"></a>Maximális URL-méret megnövelve

A 4096-os URL-lekérdezési sztring támogatása (2048 helyett) egy videó indexelhető.
 
### <a name="support-for-multi-lingual-projects"></a>Többnyelvű projektek támogatása

A projektek most már különböző nyelveken (csak API-kban) indexelt videók alapján is létrehozhatóak.

## <a name="july-2019"></a>2019. július

### <a name="editor-as-a-widget"></a>Szerkesztő widgetként

A Video Indexer AI-szerkesztő mostantól widgetként is elérhető az ügyfélalkalmazásokbe beágyazva.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Egyéni nyelvi modell frissítése feliratfájlból a portálról

Az ügyfelek VTT-, SRT- és TTML-fájlformátumokat biztosítanak a nyelvi modellek bemeneteként a portál testreszabási oldalán.

## <a name="june-2019"></a>2019. június

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer Kelet-Japánban üzembe helyezett

Most már létrehozhat egy fizetős Video Indexer Kelet-Japán régióban.

### <a name="create-and-repair-account-api-preview"></a>Fiók API létrehozása és javítása (előzetes verzió)

Új API hozzáadása, amely lehetővé teszi az Azure Media Service kapcsolati [végpontjának vagy kulcsának frissítését.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)

### <a name="improve-error-handling-on-upload"></a>Hibakezelés javítása feltöltéskor 

A rendszer leíró üzenetet ad vissza a mögöttes fiók helytelen Azure Media Services esetén.

### <a name="player-timeline-keyframes-preview"></a>Lejátszó idővonala – A KeyFrames előzetes verziója 

Most már minden alkalommal megjelenik a kép előnézete a lejátszó idővonalán.

### <a name="editor-semi-select"></a>Szerkesztő – félig kiválasztva

Most már megtekintheti az összes olyan elemzés előnézetét, amely egy adott elemzési időkeret szerkesztőben való kiválasztásának eredményeként van kiválasztva.

## <a name="may-2019"></a>2019. május

### <a name="update-custom-language-model-from-closed-caption-file"></a>Egyéni nyelvi modell frissítése feliratfájlból

[Az egyéni nyelvi modell létrehozása és](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) az [Egyéni](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) nyelvi modellek frissítése API-k mostantól támogatják a VTT, SRT és TTML fájlformátumokat a nyelvi modellek bemeneteként.

A [Videófrissítési átirat API hívatakor](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)a rendszer automatikusan hozzáadja az átiratot. A videóhoz társított betanító modell is automatikusan frissül. A nyelvi modellek testreszabásával és betanításával kapcsolatos információkért lásd: Nyelvi modell [testreszabása a Video Indexer.](customize-language-model-overview.md)

### <a name="new-download-transcript-formats--txt-and-csv"></a>Új letöltési átiratformátumok – TXT és CSV

A már támogatott feliratozási formátum (SRT, VTT és TTML) mellett a Video Indexer mostantól txt és CSV formátumban is támogatja az átirat letöltését.

## <a name="next-steps"></a>Következő lépések

[Áttekintés](video-indexer-overview.md)
