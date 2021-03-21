---
title: A Bing Custom Search-élmény konfigurálása | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: A portálon létrehozhat egy olyan keresési példányt, amely meghatározza a webes szeleteket. tartományok, allapok és weblapok.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: b2d4eb75002474bbec57753bbf0255cc4c2b4a16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96338639"
---
# <a name="configure-your-bing-custom-search-experience"></a>A Bing Custom Search-élmény konfigurálása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Az egyéni keresési példány lehetővé teszi a keresési élmény testreszabását, hogy csak olyan webhelyekről tartalmazzon tartalmat, amelyekkel a felhasználók törődnek. Webes keresés helyett a Bing csak az Önt érdeklő webes szeleteket keresi. Az egyéni kívánalmaknak megfelelő webes keresés létrehozásához használja a Bing Custom Search [portált](https://www.customsearch.ai).

A portál lehetővé teszi, hogy olyan keresési példányt hozzon létre, amely meghatározza a Web: tartományok, aloldalak és weblapok szeleteit, amelyeket a Bingnek keresnie kell, és azokat, amelyeket nem kíván a keresésre használni. A portál arra is utalhat, hogy milyen tartalmat érdemes felvennie.

A webes szeletek definiálásához használja az alábbiakat:

| Szelet neve | Description                                                                                                                                                                                                                                                                                                |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tartomány     | A tartományi szeletek az internetes tartományon belül található összes tartalmat tartalmazzák. Például: `www.microsoft.com`. A kihagyás `www.` azt eredményezi, hogy a Bing a tartomány altartományait is keresi. Ha például a értéket adja meg `microsoft.com` , a Bing a vagy a eredményeit is visszaadja `support.microsoft.com` `technet.microsoft.com` . |
| Aloldalt    | Az aloldali szeletek a aloldalon és az alatta lévő elérési utakon található összes tartalmat tartalmazzák. Az elérési úton legfeljebb két aloldal adható meg. Például: `www.microsoft.com/en-us/windows/`                                                                                                                       |
| Weblap    | A weblap szeletek csak az adott weboldalt tartalmazhatják egyéni keresésekben. Opcionálisan megadhatja, hogy megjelenjenek-e az allapok.                                                                                                                                                                                  |

> [!IMPORTANT]
> A megadott összes tartománynak, aloldalnak és weboldalnak nyilvánosnak kell lennie, és a Bing által indexelve kell lennie. Ha a keresésbe felvenni kívánt nyilvános webhely tulajdonosa, és a Bing nem indexelte azt, tekintse meg a Bing [Webmester dokumentációját](https://www.bing.com/webmaster/help/webmaster-guidelines-30fba23a) , amely részletesen ismerteti a Bing indexelését. Emellett a webmester dokumentációjában tájékozódhat arról, hogy a Bing hogyan tudja frissíteni a bejárt helyet, ha az index elavult.

## <a name="add-slices-of-the-web-to-your-custom-search-instance"></a>A webes szeletek hozzáadása az egyéni keresési példányhoz

Az egyéni keresési példány létrehozásakor megadhatja a Web: tartományok, aloldalak és weblapok szeleteit, amelyeket a keresési eredmények között be szeretne vonni vagy le kíván tiltani. 

Ha ismeri az egyéni keresési példányba felvenni kívánt szeleteket, adja hozzá őket a példány **aktív** listájához. 

Ha nem tudja, hogy mely szeletek szerepeljenek, a keresési lekérdezések a Bing szolgáltatásba küldhetők a **betekintő** ablaktáblán, és kiválaszthatja a kívánt szeleteket. Ehhez tegye a következőket: 

1. Válassza a "Bing" elemet a betekintő ablaktábla legördülő listából, és adjon meg egy keresési lekérdezést.

2. Kattintson a **hely hozzáadása** lehetőségre a felvenni kívánt eredmény mellett. Ezután kattintson az OK gombra.

>[!NOTE]
> [!INCLUDE[publish or revert](./includes/publish-revert.md)]

<a name="active-and-blocked-lists"></a>

### <a name="customize-your-search-experience-with-active-and-blocked-lists"></a>A keresési élmény testreszabása aktív és letiltott listával 

Az aktív és a blokkolt szeletek listáját az egyéni keresési példány **aktív** és **tiltott** lapjaira kattintva érheti el. Az aktív listához hozzáadott szeletek szerepelni fognak az egyéni keresésben. A letiltott szeletek nem lesznek megkeresve, és nem jelennek meg a keresési eredmények között.

Ha meg szeretné adni a kereséshez használni kívánt webes szeleteket, kattintson az **aktív** fülre, és adjon hozzá egy vagy több URL-címet. URL-címek szerkesztéséhez vagy törléséhez használja a **vezérlők** oszlopban lévő beállításokat. 

Ha URL-címeket ad hozzá az **aktív** listához, egyetlen URL-címet vagy egyszerre több URL-címet is hozzáadhat, ha feltölt egy szövegfájlt a feltöltés ikon használatával.

![Az Bing Custom Search aktív lap](media/file-upload-icon.png)

Fájl feltöltéséhez hozzon létre egy szövegfájlt, és határozzon meg soronként egy tartományt, egy allapot vagy egy weblapot. Ha a fájl nem megfelelően van formázva, a rendszer elutasítja a fájlt.

> [!NOTE]
> * Csak egy fájlt tölthet fel az **aktív** listára. Nem használható szeletek hozzáadásához a **blokkolt** listához.  
> * Ha a **letiltott** lista tartalmaz egy tartományt, allapot vagy weboldalt, amelyet a feltöltési fájlban megadott, akkor a rendszer eltávolítja a **letiltott** listából, és hozzáadja az **aktív** listához.
> * A rendszer figyelmen kívül hagyja a feltöltött fájlban lévő ismétlődő bejegyzéseket Bing Custom Search. 

### <a name="get-website-suggestions-for-your-search-experience"></a>Webhelyek javaslatainak beszerzése a keresési élményhez

Miután hozzáadta a webes szeleteket az **aktív** listához, a Bing Custom Search portál webhelyeket és aloldali javaslatokat fog generálni a lap alján. Ezek olyan szeletek, amelyekben Bing Custom Search gondolja, hogy érdemes-e belefoglalni. Kattintson a **frissítés** gombra az egyéni keresési példány beállításainak frissítése után a frissített javaslatok beszerzéséhez. Ez a szakasz csak akkor látható, ha a javaslatok elérhetők.

## <a name="search-for-images-and-videos"></a>Képek és videók keresése

A webes tartalomhoz hasonló képeket és videókat kereshet a [Bing custom Image Search API](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) vagy a [BING Custom Video Search API](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)használatával. Ezeket az eredményeket az [üzemeltetett felhasználói felület](hosted-ui.md)vagy az API-k segítségével jelenítheti meg. 

Ezek az API-k a nem egyéni [Bing Image Search](../Bing-Image-Search/overview.md) és [Bing Video Search](../bing-video-search/overview.md) API-kra hasonlítanak, de a teljes weben kereshetnek, és nem igénylik a `customConfig` lekérdezési paramétert. A képek és videók kezelésével kapcsolatos további információkért tekintse meg ezeket a dokumentációs készleteket. 

## <a name="test-your-search-instance-with-the-preview-pane"></a>A keresési példány tesztelése a betekintő ablaktáblával

A keresési példány teszteléséhez használja a portál jobb oldalán található előnézet ablaktáblát a keresési lekérdezések elküldéséhez, és tekintse meg az eredményeket. 

1. A keresőmező alatt válassza a **saját példány** lehetőséget. A keresési élmény eredményeit összehasonlíthatja a Bing-be a **Bing** lehetőség kiválasztásával. 
2. Válassza ki a biztonságos keresési szűrőt és a keresendő piacot (lásd a [lekérdezési paramétereket](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)).
3. Írjon be egy lekérdezést, és nyomja le az ENTER billentyűt, vagy kattintson a keresés ikonra az aktuális konfiguráció eredményeinek megtekintéséhez. A kívánt keresési típus módosításához kattintson a **web**, a **képek** vagy a **videó** lehetőségre a megfelelő eredmények eléréséhez. 

<a name="adjustrank"></a>

## <a name="adjust-the-rank-of-specific-search-results"></a>Adott keresési eredmények rangsorának módosítása

A portál lehetővé teszi a tartalom keresési rangsorolásának módosítását adott tartományokból, allapokból és weblapokból. Miután elküldte a keresési lekérdezést a betekintő ablaktáblán, minden keresési eredmény tartalmazza az általa elérhetővé tenni kívánt beállítások listáját:  

| Idő beállítása | Description |
|------------|-------------|
| Letiltás      | Áthelyezi a tartományt, az allapot vagy a weblapot a blokkolt listára. A Bing kizárja a kijelölt helyről származó tartalmat a keresési eredmények között.                    |
| Boost      | Fokozza a tartalmat a tartományból vagy az aloldalról, hogy magasabb legyen a keresési eredmények között.                                                                                        |
| Lefokozás     | A keresési eredményekben a tartomány vagy az aloldal tartalmának lefokozása. Kiválaszthatja, hogy le kívánja-e fokozni a tartalmat a tartományból vagy aloldalból, amelyhez a weblap tartozik. |
| Rögzítés a tetejére | Áthelyezi a tartományt, az allapot vagy a weblapot a **rögzített** listára. Ez azt kényszeríti, hogy a weblap egy adott keresési lekérdezés legfelső szintű keresési eredményeként jelenjen meg.                   |

A rangsor módosítása nem érhető el a képek és a videók kereséséhez.

### <a name="boosting-and-demoting-search-results"></a>A keresési eredmények növelése és lefokozása

Az **aktív** lista bármely tartományának vagy aloldalának extra lendületet, lendületet vagy lefokozást végez. Alapértelmezés szerint a rendszer az összes szeletet rangsoroló beállítások nélkül adja hozzá. A rendkívül lendülettel vagy lendülettel ellátott webes szeletek a keresési eredményekben magasabban vannak rangsorolva (a Super Boost rangsora nagyobb, mint a Boost). A lefokozni kívánt elemek alacsonyabbak lesznek a keresési eredményekben.

Az elemeket az **aktív** listán szereplő **rangsorolási beállításokkal** vagy a betekintő ablaktáblán a kiemelés és a lefokozás vezérlők használatával lehet megerősíteni, fokozni vagy lefokozni. A szolgáltatás hozzáadja a szeletet az aktív listához, és ennek megfelelően módosítja a rangsort.

> [!NOTE] 
> A tartományok és aloldalak kiemelése és lefokozása számos módszer egyike, Bing Custom Search a keresési eredmények sorrendjét határozza meg. A különböző webes tartalmak rangsorolását befolyásoló egyéb tényezők miatt a rangsor módosítása eltérő lehet. A betekintő ablaktábla segítségével tesztelheti a keresési eredmények rangsorának módosításának hatásait. 

A Super Boost, a Boost és a lefokozás nem érhető el a képek és a videók kereséséhez.

## <a name="pin-slices-to-the-top-of-search-results"></a>Szeletek rögzítése a keresési eredmények tetejére

A portálon a **rögzített** lap használatával is rögzíthet URL-címeket az adott keresési kifejezések keresési eredményeihez. Adjon meg egy URL-címet és egy lekérdezést, amely megadja a felső eredményként megjelenő weblapot. Vegye figyelembe, hogy keresési lekérdezésekben legfeljebb egy weblapot rögzíthet, és csak indexelt weblapok jelennek meg a keresésekben. A rögzítés eredményei nem érhetők el a képek és a videók kereséséhez.

Két módon rögzíthet egy weblapot a tetejére:

* A **rögzített** lapon adja meg annak a weblapnak az URL-címét, amely a tetejére és a hozzá tartozó lekérdezésre van rögzítve.

* Az **előnézet** ablaktáblán írjon be egy keresési lekérdezést, és kattintson a Keresés gombra. Keresse meg a lekérdezéshez rögzíteni kívánt weblapot, majd kattintson a **rögzítés a tetejére** elemre. a rendszer hozzáadja a weboldalt és a lekérdezést a **rögzített** listához.

### <a name="specify-the-pins-match-condition"></a>A PIN-kód egyeztetési feltételének megadása

Alapértelmezés szerint a weblapok csak akkor vannak rögzítve a keresési eredmények tetejére, ha a felhasználó lekérdezési karakterlánca pontosan megegyezik a **rögzített** listán szereplővel. Ezt a viselkedést a következő egyeztetési feltételek egyikének megadásával módosíthatja:

> [!NOTE]
> A felhasználó keresési lekérdezése és a PIN-kód keresési lekérdezése közötti összes összehasonlítás a kis-és nagybetűk megkülönböztetése nélkül történik.

| Érték | Leírás                                                                          |
|---------------|----------------------------------------------------------------------------------|
| Ezzel kezdődik | A PIN-kód akkor egyezik, ha a felhasználó lekérdezési karakterlánca a PIN-kód lekérdezési karakterláncával kezdődik. |
| Végződik   | A PIN-kód akkor egyezik, ha a felhasználó lekérdezési karakterlánca a PIN-kód lekérdezési karakterláncával végződik.  |
| Contains    | A PIN-kód akkor egyezik, ha a felhasználó lekérdezési karakterlánca tartalmazza a PIN-kód lekérdezési karakterláncát.   |


A PIN-kód egyeztetési feltételének módosításához kattintson a PIN-kód szerkesztési ikonjára. A **lekérdezési egyeztetési feltétel** oszlopban kattintson a legördülő listára, és válassza ki a használni kívánt új feltételt. Ezután kattintson a Save (Mentés) ikonra a módosítás mentéséhez.

### <a name="change-the-order-of-your-pinned-sites"></a>Rögzített webhelyek sorrendjének módosítása

A PIN-kódok sorrendjének megváltoztatásához húzza át őket, vagy szerkessze a sorszámot a **rögzített** lista **vezérlők** oszlopában található "szerkesztés" ikonra kattintva.

Ha több PIN-kód is megfelel egy egyeztetési feltételnek, Bing Custom Search a listában a legmagasabbat fogja használni.

## <a name="view-statistics"></a>Statisztikák megtekintése

Ha a megfelelő szinten előfizetett az egyéni keresésre (lásd a [díjszabási oldalakat](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)), a rendszer a **statisztikák** lapot adja hozzá az éles példányokhoz. A Statistics (statisztika) lapon az egyéni keresési végpontok használatáról olvashat részletesen, beleértve a hívások mennyiségét, a leggyakoribb lekérdezéseket, a földrajzi eloszlást, a reagálási kódokat és a biztonságos keresést. A megadott vezérlők használatával szűrheti a részleteket.

## <a name="usage-guidelines"></a>Használati irányelvek

- Az egyes egyéni keresési példányok esetében az **aktív** és a **blokkolt** szeletekre vonatkozó rangsorolási módosítások maximális száma 400.
- Ha egy szeletet ad hozzá az aktív vagy a letiltott lapokhoz, egy rangsorolási beállításnak számít.
- A szám növelése és lefokozása két rangsorolási beállításként.
- Az egyes egyéni keresési példányok esetében az elvégezhető PIN-kódok maximális száma a 200-ra korlátozódik.

## <a name="next-steps"></a>Következő lépések

- [Egyéni keresés meghívása](./search-your-custom-view.md)
- [Üzemeltetett felhasználói felület konfigurálása](./hosted-ui.md)
- [Díszítő jelölők használata szövegkiemeléshez](../bing-web-search/hit-highlighting.md)
- [Weboldalak lapozása](../bing-web-search/paging-search-results.md)