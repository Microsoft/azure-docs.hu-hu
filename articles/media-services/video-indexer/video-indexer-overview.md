---
title: Mi az az Azure Media Services Video Indexer?
titleSuffix: Azure Media Services
description: Ez a cikk áttekintést nyújt a Azure Media Services Video Indexer szolgáltatásról.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/05/2021
ms.author: juliako
ms.openlocfilehash: 12d23ec471329bd4e0ecb502750198e946e58872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530229"
---
# <a name="what-is-azure-media-services-video-indexer"></a>Mi az az Azure Media Services Video Indexer?

[!INCLUDE [regulation](./includes/regulation.md)]

A Video Indexer (VI) a Azure Media Services AI-megoldás, amely az Azure Cognitive Services Brand részét képezi. A Video Indexer lehetővé teszi a részletes elemzések kinyerését (az adatok elemzése és a kódolási képességek nélkül) a gépi tanulási modellek használatával több csatornán (hang, vokál, vizualizáció). Tovább testreszabhatja és betaníthatja a modelleket. A szolgáltatás lehetővé teszi a mélyreható keresést, csökkenti a működési költségeket, lehetővé teszi az új bevételi lehetőségeket, és új felhasználói élményeket hoz létre a videók nagy archívumában (alacsony belépési korlátokkal).

Az Video Indexerekkel való kinyerésének megkezdéséhez létre kell hoznia egy fiókot, és fel kell töltenie a videókat. A videók Video Indexer való feltöltésekor a vizualizációkat és a hanganyagokat különböző AI-modellek futtatásával elemezze. Ahogy Video Indexer elemzi a videót, az AI-modellek által kinyert elemzéseket.

Amikor létrehoz egy Video Indexer fiókot, és összekapcsolja Media Services, az adathordozó-és metaadat-fájlokat a rendszer az adott Media Services fiókhoz társított Azure Storage-fiókban tárolja. További információ: az [Azure-hoz csatlakoztatott video Indexer-fiók létrehozása](connect-to-azure.md).

A következő ábra egy illusztráció, nem pedig technikai magyarázat arról, hogy a Video Indexer hogyan működik a háttérben.

![Azure Media Services Video Indexer folyamatábrája](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság

Fontos megjegyezni, hogy be kell tartania az összes vonatkozó törvényt a Video Indexer használatára vonatkozóan, és Ön nem használhatja Video Indexer vagy bármely Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy amelyek károsak lehetnek mások számára.

A videók vagy rendszerképek Video Indexerba való feltöltése előtt minden megfelelő jogosultsággal kell rendelkeznie a videó/rendszerkép használatához, beleértve a törvény által előírt jogokat, a videóban/képben, az adatok felhasználásához, feldolgozásához és tárolásához szükséges összes hozzájárulást a Video Indexer és az Azure-ban. Bizonyos joghatóságok bizonyos adatkategóriák, például biometrikus adatok gyűjtésére, online feldolgozására és tárolására vonatkozó különleges jogi követelményeket állapíthatnak meg. Mielőtt a Video Indexer és az Azure-t használja a különleges jogi követelmények hatálya alá eső bármilyen adat feldolgozásához és tárolásához, meg kell győződnie arról, hogy megfelel az Ön számára esetlegesen felmerülő jogi követelményeknek.

A megfelelőség, az adatvédelem és a biztonság megismeréséhez Video Indexer látogasson el a Microsoft [adatvédelmi központba](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx). A Microsoft adatvédelmi kötelezettségei, az adatkezelési és adatmegőrzési eljárások, beleértve az adatok törlésének módját is, tekintse át a Microsoft [adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement), az [online szolgáltatások használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") és az [adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Video Indexer használatával Ön vállalja, hogy az OST, a DPA és az adatvédelmi nyilatkozat köti.

## <a name="what-can-i-do-with-video-indexer"></a>Mire használhatom a Video Indexer?

Video Indexer az adatok számos forgatókönyvre alkalmazhatók, többek között a következők:

* Részletes *Keresés*: a videóból kinyert adatok segítségével javíthatja a keresési élményt a videó-tárak között. Például a kimondott szavak és arcok indexelése lehetővé teszi a keresési élmény megkeresését egy videóban, ahol egy személy bizonyos szavakat beszélt, vagy ha két személy együtt volt látható. A videók alapján végzett keresés a hírkiszolgáló, az oktatási intézmények, a műsorszolgáltatók, a szórakoztató tartalmak tulajdonosai, a vállalati LOB-alkalmazások és általában minden olyan iparág esetében érvényes, amelyre a felhasználóknak szüksége van.
* *Tartalom létrehozása*: hozzon létre pótkocsikat, jelöljön ki orsókat, közösségi médiatartalmakat vagy híreket a tartalomból video Indexer kivonatok alapján. A kulcsképek, a jelenetek jelölői és az időbélyegek a személyek és a címkék megjelenéséhez a létrehozási folyamat sokkal simább és egyszerűbb, és lehetővé teszi a videó azon részeinek elérését, amelyekre szüksége van a létrehozott tartalomhoz.
* *Kisegítő lehetőségek*: szeretné, hogy a tartalom elérhető legyen a fogyatékkal élők számára, vagy ha azt szeretné, hogy a tartalom különböző régiókban legyen szétosztva különböző nyelveken, a video Indexer által biztosított átírást és fordítást több nyelven is használhatja.
* *Monetizálása*: video Indexer segíthet a videók értékének növelésében. Például az ad-bevételre támaszkodó iparágak (a média, a közösségi média stb.) a releváns hirdetéseket a kinyert információk használatával továbbítják az ad-kiszolgáló további jeleiként.
* *Tartalom moderálása*: a szöveges és a vizuális tartalom moderálási modelljeinek használatával a felhasználók biztonságban maradhatnak a nem megfelelő tartalomtól, és ellenőrizhetik, hogy a közzétett tartalom megfelel-e a szervezet értékeinek. Automatikusan blokkolhat bizonyos videókat, vagy riasztást kaphat a felhasználóknak a tartalomról.
* *Javaslatok*: a videó-bepillantást a felhasználók bevonásával növelheti. Ha az egyes videókat további metaadatokkal címkézi, ajánlhatja a felhasználók számára a legfontosabb videókat, és kiemelheti a videó azon részeit, amelyek megfelelnek az igényeinek.

## <a name="features"></a>Funkciók

A következő lista azokat az elemzéseket mutatja be, amelyekkel lekérheti a videókat Video Indexer videó-és hangmodellek használatával:

### <a name="video-insights"></a>Videó-felismerések

* **Arcfelismerés**: Felismeri és csoportosítja a videóban megjelenő arcokat.
* **Celebrity-azonosítás**: video Indexer automatikusan azonosítja a több mint 1 000 000 hírességet, például a világ vezetőit, színészeit, színésznőit, sportolóit, kutatóit, üzleti és műszaki vezetőit. A hírességekkel kapcsolatos információk különböző webhelyeken is megtalálhatók (IMDB, wikipedia stb.).
* **Fiókalapú arcfelismerés**: A Video Indexer betanít egy modellt a megadott fióknak. Ezután a betanított modell alapján felismeri a videóban található arcokat. További információ: [személyre szabott modell testreszabása a video Indexer webhelyről](customize-person-model-with-website.md) és [személyre szabott modell testreszabása a video Indexer API-val](customize-person-model-with-api.md).
* **Bélyegképek kinyerése az arcoknál** ("legjobb arc"): automatikusan azonosítja a legjobban rögzített arcot az arcok mindegyik csoportjában (a minőség, a méret és az elülső pozíció alapján), és kinyeri azt képként.
* **Vizuális szövegek felismerése** (OCR): a videóban látható szöveg kibontása.
* **Vizuális tartalom moderálása**: Felismeri a felnőtt és/vagy kényes látványelemeket.
* **Címkeazonosítás**: Azonosítja a megjelenített vizuális objektumokat és műveleteket.
* **Jelenet szegmentálása**: meghatározza, hogy a jelenet Mikor változik a videóban a vizuális célzások alapján. Egy jelenet egyetlen eseményt ábrázol, amely egymást követő felvételekből áll, amelyek szemantikai kapcsolatban állnak egymással.
* **Shot észlelés**: meghatározza, hogy mikor változik a videó a vizualizációs útmutatók alapján. A shot olyan keretek sorozata, amelyek ugyanabba a mozgóképes kamerából származnak. További információ: [jelenetek, felvételek és kulcsképek](scenes-shots-keyframes.md).
* **Fekete keret észlelése**: Azonosítja a videóban megjelenő fekete kereteket.
* **Kulcsképkockák kinyerése**: Felismeri a videók stabil kulcsképkockáit.
* **Működés közbeni kreditek**: a TV-műsorok és-filmek végén a működés közbeni kreditek kezdetét és végét azonosítja.
* **Animált karakterek észlelése** (előzetes verzió): az animált tartalomban lévő karakterek észlelése, csoportosítása és felismerése [Cognitive Services egyéni jövőképtel](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)való integráció révén. További információ: [animált karakterek észlelése](animated-characters-recognition.md).
* **Szerkesztői shot Type észlelése**: a felvételek a típusuk alapján vannak megjelölve (például: Wide shot, Medium shot, közelkép, Extreme közelkép, két lövés, több személy, kültéri és beltéri stb.). További információ: [szerkesztési shot típusú észlelés](scenes-shots-keyframes.md#editorial-shot-type-detection).

### <a name="audio-insights"></a>Audio-elemzések

* **Hang átirata**: átalakítja a beszédet 12 nyelven, és lehetővé teszi a bővítmények használatát. A támogatott nyelvek: angol, spanyol, francia, német, olasz, mandarin kínai, japán, arab, orosz, portugál, hindi és koreai.
* **Automatikus nyelvmeghatározás**: Automatikusan meghatározza a domináns beszélt nyelvet. A támogatott nyelvek: angol, spanyol, francia, német, olasz, mandarin kínai, japán, orosz és portugál. Ha a nyelv nem azonosítható egyértelműen, a Video Indexer azt feltételezi, hogy a beszélt nyelv az angol. További információért tekintse meg a [nyelvazonosítási modellel](language-identification-model.md) foglalkozó cikket.
* **Többnyelvű beszéd azonosítása és átírása**: automatikusan azonosítja a beszélt nyelvet a különböző szegmensekben a hangból. A szolgáltatás elküldi a médiafájl egyes szegmenseit átírásra, majd egyesíti az átiratokat egyetlen összevont átírássá. További információért tekintse meg a [többnyelvű tartalom automatikus azonosításával és átírásával](multi-language-identification-transcription.md) foglalkozó cikket.
* **Hangfeliratok**: Hangfeliratokat hoz létre három formátumban: VTT, TTML, SRT.
* **Kétcsatornás feldolgozás**: automatikusan észleli a különálló átiratokat, és egyesíti az adott idővonalat.
* **Zajcsökkentés**: a telefonos hang-vagy zajos (skype-szűrők alapján) felvételek törlése.
* **Átirat testreszabása** (CRIS): az egyéni beszédeket a szöveges modellekhez az iparági specifikus átiratok létrehozásához. További információkért lásd: [nyelvi modell testreszabása a video Indexer webhelyről](customize-language-model-with-website.md) és a [nyelvi modell testreszabása a video Indexer API](customize-language-model-with-api.md)-kkal.
* **Beszélő enumerálása**: leképezi a térképeket, és megérti, hogy a beszélő mely szavakat és mikor szólalt meg. A rendszer 16 beszélőt képes észlelni egyetlen hangfájlban.
* A **beszélő statisztikái**: statisztikai adatokat biztosít a beszélők beszédének arányáról.
* **Szöveges tartalom moderálása**: Felismeri az explicit szövegeket a hanganyag alapján készült átiratban.
* **Hangeffektusok** (nyilvános előzetes verzió): a következő hanghatásokat észleli a tartalom nem beszéd szegmensében: lőtt, üveg összetörő, riasztás, sziréna, robbanás, kutya kéreg, sikítás, nevetés, tömegre adott válaszok (éljenzés, taps és booing) és csend. Megjegyzés: az események teljes készlete csak akkor érhető el, ha a feltöltési beállításkészletben a "speciális hangelemzés" lehetőséget választja, ellenkező esetben csak a "Silence" és a "Crowd Reactions" lesz elérhető.
* **Érzelem észlelése**: az érzelmeket a beszéd alapján azonosítja (mit mondott) és a hangtónust (ahogy azt mondják). Az érzelem lehet a Joy, a szomorúság, a düh vagy a félelem.
* **Fordítás**: Fordítást készít a hanganyag alapján készült átiratról 54 különböző nyelven.

### <a name="audio-and-video-insights-multi-channels"></a>Hang-és video-elemzések (több csatorna)

Ha egy csatorna indexelést végez, a modellek részleges eredménye lesz elérhető.

* **Kulcsszavak kinyerése**: kulcsszavak kibontása beszédből és képi szövegből.
* **Nevesített entitások kinyerése**: Kinyeri a márkákat, a helyszíneket és a beszédből és a vizualizációból származó személyeket természetes nyelvi feldolgozás (NLP) használatával.
* **Témakör-következtetés**: Kikövetkezteti a fő témaköröket az átiratokból. A 2. szintű IPTC-taxonómiát is tartalmazza.
* **Összetevők**: Rendkívül részletes összetevők széles választékát nyeri ki modellhez.
* **Hangulatelemzés**: Azonosítja a pozitív, negatív vagy semleges érzelmeket a beszédben és a vizuális szövegekben.

## <a name="how-can-i-get-started-with-video-indexer"></a>Hogyan szerezhetem be a Video Indexer?

A Video Indexer képességei háromféleképpen érhetők el:

* Video Indexer portál: egy könnyen használható megoldás, amellyel kiértékelheti a terméket, kezelheti a fiókot, és testre szabhatja a modelleket.

    További információ a portálról: Ismerkedés [a video Indexer webhellyel](video-indexer-get-started.md).  

* API-integráció: Video Indexer összes funkciója elérhető egy REST APIn keresztül, amely lehetővé teszi a megoldás integrálását az alkalmazásokba és az infrastruktúrába.

    A fejlesztőknek szóló első lépésekhez tekintse meg a [Video Indexer REST API használata](video-indexer-use-apis.md)című témakört.

* Beágyazható widget: lehetővé teszi az Video Indexer-adattartalom, a lejátszó és a szerkesztői élmény beágyazását az alkalmazásba.

    További információ: [Visual widgetek beágyazása az alkalmazásba](video-indexer-embed-widgets.md).

Ha a webhelyet használja, az adatok metaadatokként lesznek hozzáadva, és megjelennek a portálon. Ha API-kat használ, az eredmények JSON-fájlként érhetők el.

## <a name="supported-browsers"></a>Támogatott böngészők

A következő lista azokat a támogatott böngészőket mutatja be, amelyeket a Video Indexer webhelyhez, illetve a widgeteket beágyazó alkalmazásaihoz használhat. A lista a támogatott böngésző minimális verzióját is megjeleníti:

- Edge, verzió: 16
- Firefox, verzió: 54
- Chrome, verzió: 58
- Safari, verzió: 11
- Opera, verzió: 44
- Opera Mobile, verzió: 59
- Android böngésző, verzió: 81
- Samsung böngésző, verzió: 7
- Chrome Androidhoz, verzió: 87
- Androidos Firefox, verzió: 83

## <a name="next-steps"></a>Következő lépések

Készen áll a Video Indexer használatának megkezdésére. További információért tekintse át a következő cikkeket:

- Ismerkedjen meg [a video Indexer webhellyel](video-indexer-get-started.md).
- [Tartalom feldolgozása Video Indexer Rest APIsal](video-indexer-use-apis.md).
- [Vizualizációs widgetek beágyazása az alkalmazásba](video-indexer-embed-widgets.md).
