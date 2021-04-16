---
title: Nagy méretekben használt Video Indexer megfontolni – Azure
titleSuffix: Azure Media Services
description: Ez a témakör ismerteti, hogy mit érdemes figyelembe venni a Video Indexer használatakor.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: f941d81df670f017d24a7c5011c55fcc4f082605
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531564"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Nagy méretekben használt Video Indexer megfontolni

Ha a Azure Media Services Video Indexerrel indexeli a videókat, és a videók archívuma növekszik, fontolja meg a skálázást. 

Ez a cikk a következő kérdésekre ad választ:

* Figyelembe kell venni valamilyen technológiai korlátozást?
* Van ennek intelligens és hatékony módja?
* Megakadályozhatom, hogy felesleges pénzt költsek a folyamat során?

A cikk hat ajánlott eljárásokat tartalmaz a nagy Video Indexer való használathoz.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Videók feltöltésekor fontolja meg egy URL-cím bájttömbön keresztüli használatát

Video Indexer választ a videók URL-címről való feltöltésére, vagy közvetlenül a fájl bájttömbként való elküldését, az utóbbira bizonyos korlátozások vonatkoznak. További információ: [Feltöltési szempontok és korlátozások)](upload-index-videos.md#uploading-considerations-and-limitations)

Először is a fájlméretre vonatkozó korlátozásokkal rendelkezik. A bájttömbfájl mérete az URL-cím használata esetén a 30 GB-os feltöltési méretkorláthoz képest legfeljebb 2 GB lehet.

Másodszor tekintsünk át néhány olyan problémát, amelyek hatással lehetnek a teljesítményére, és ezáltal a skálázással kapcsolatban:

* Ha többrészes fájlokat küld, az nagymértékű hálózati függőséget jelent. 
* szolgáltatás megbízhatósága, 
* Kapcsolat 
* feltöltési sebesség, 
* elveszett csomagok valahol a világhálón.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="A nagy léptékű Video Indexer használatának első szempontja":::

Amikor URL-cím használatával tölt fel videókat, csak meg kell adnia egy elérési utat egy médiafájl helyéhez, és a Video Indexer a többiről is gondoskodik (lásd a videófeltöltési `videoUrl` API [mezőjét).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)

> [!TIP]
> Használja `videoUrl` a videófeltöltési API opcionális paraméterét.

Ha látni akar egy példát arra, hogyan tölthet fel videókat URL-cím használatával, tekintse meg ezt [a példát.](upload-index-videos.md#code-sample) Az [AzCopyval](../../storage/common/storage-use-azcopy-v10.md) gyorsan és megbízhatóan is elküldheti a tartalmat egy tárfiókba, amelyről elküldheti a tartalmat a Video Indexer [SAS URL-cím használatával.](../../storage/common/storage-sas-overview.md)

## <a name="increase-media-reserved-units-if-needed"></a>Média számára fenntartott egységek növelése szükség esetén

Általában a koncepció igazolásának fázisában, amikor csak most kezd Video Indexer, nincs szükség nagy számítási teljesítményre. Ha nagyobb számú videót szeretne indexelni, és azt szeretné, hogy a folyamat az Ön használati esetének megfelelő ütemben legyen, fel kell skáláznunk a használati Video Indexer. Ezért érdemes lehet növelni a használt számítási erőforrások számát, ha a jelenlegi számítási teljesítmény éppen nem elegendő.

Ebben Azure Media Services a számítási teljesítmény és a párhuzamosítás növeléséhez figyelmet kell [](../latest/concept-media-reserved-units.md)fordítania a fenntartott médiaegységekre (RU-k). A ru-k azok a számítási egységek, amelyek meghatározzák a médiafeldolgozási feladatok paramétereit. A ru-k száma hatással van az egyes fiókokban egyidejűleg feldolgozható médiafeladatok számára, és a típusuk határozza meg a feldolgozási sebességet, és egy videóhoz egynél több RU szükséges lehet, ha az indexelés összetett. Ha a ru-k foglaltak, az új feladatok egy várólistán lesznek, amíg egy másik erőforrás elérhetővé nem válik.

A hatékony működés és annak érdekében, hogy az erőforrások az idő egy részében ne maradjanak tétlenek, az Video Indexer automatikus skálázható rendszert kínál, amely a ru-k leállnak, amikor kevesebb feldolgozásra van szükség, és a rendelkezésre álló idő alatt (az összes ru-t teljes mértékben kihasználhatja) felfelé. Ezt a funkciót [](manage-account-connected-to-azure.md#autoscale-reserved-units) az automatikus skálázás fiókbeállításokban való bekapcsolásával vagy az [Update-Paid-Account-Azure-Media-Services API használatával engedélyezheti.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services)

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="A nagy léptékű Video Indexer használatának második szempontja":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS-hez fenntartott egységek":::

Az indexelés időtartamának és az alacsony átviteli sebesség minimalizálása érdekében javasoljuk, hogy 10 S3 típusú ru-val kezdjen. Ha később több tartalom vagy magasabb szintű egyidejűség támogatására skáláz fel, és több erőforrásra van szüksége, lépjen kapcsolatba velünk a támogatási rendszerrel [(csak](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) fizetős fiókok esetén), és kérjen további ru-kiosztást.

## <a name="respect-throttling"></a>A szabályozás tiszteletben tartására

Video Indexer nagy léptékű indexelésre készült, és ha a legtöbbet szeretné kihozni belőle, akkor tisztában kell lennie a rendszer képességeivel, és ennek megfelelően kell tervezni az integrációt. Nem szeretne feltöltési kérést küldeni egy videókötetre, csak azt tapasztalni, hogy egyes filmek nemtöltődnek fel, és HTTP 429-es válaszkódot kap (túl sok kérés). Ennek az az oka, hogy több kérést küldött, mint a támogatott filmek percenkénti [korlátja.](upload-index-videos.md#uploading-considerations-and-limitations) Video Indexer egy fejlécet ad hozzá a HTTP-válaszhoz, a fejléc határozza meg, hogy mikor kísérelje meg a `retry-after` következő újrapróbálkozás. A következő kérés kipróbálása előtt ügyeljen rá, hogy tartsa tiszteletben.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Az integráció jól megtervezése, a szabályozás tiszteletben tartásával":::

## <a name="use-callback-url"></a>Visszahívási URL-cím használata

Javasoljuk, hogy ahelyett, hogy folyamatosan lekérdezi a kérés állapotát a feltöltési [](upload-index-videos.md#callbackurl)kérelem elküldéstől a másodiktól, hozzáadhat egy visszahívási URL-címet, és megvárhatja, amíg a Video Indexer frissül. Amint állapotváltozást kap a feltöltési kérelemben, egy POST-értesítést kap a megadott URL-címre.

A videófeltöltési API egyik paramétereként hozzáadhat egy visszahívási [URL-címet.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video) Tekintse meg a [GitHub-adattárban található kódmintákat.](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/) 

A visszahívási URL-címhez Azure Functions kiszolgáló nélküli eseményvezérelt platformot is használhat, amelyet HTTP aktiválhat, és a következő folyamatot valósíthatja meg.

### <a name="callback-url-definition"></a>visszahívási URL-cím definíciója

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>A megfelelő indexelési paraméterek használata

A nagy léptékű használattal Video Indexer döntések meghozatalakor nézze meg, hogyan hozhatja ki a legtöbbet az igényeihez megfelelő paraméterekkel. Gondolja át a saját esetét, és definiálja a különböző paramétereket, amelyek segítségével pénzt takaríthat meg, és gyorsabban indexelheti a videók indexelési folyamatát.

A videó feltöltése és indexelése előtt olvassa el ezt a rövid dokumentációt, és tekintse meg az [indexingPreset](upload-index-videos.md#indexingpreset) és [a streamingPreset](upload-index-videos.md#streamingpreset) fájlt, hogy jobban átolvassa a lehetőségeket. [](upload-index-videos.md)

Ha például nem tervezi megnézni a videót, ne indexelze a videóelemzéseket, ha csak hangelemzésre van szüksége, ne állítsa streamelésre az előzetes beállítást.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Indexelés optimális felbontásban, nem a legmagasabb felbontásban

Előfordulhat, hogy azt kérdezi, milyen videóminőségre van szüksége a videók indexelésében? 

Az indexelési teljesítmény sok esetben szinte semmilyen különbséggel nem rendelkezik a HD-videók (720P) és a 4K-s videók között. Végül szinte azonos megbízhatósággal kaphatja meg szinte ugyanazt az információt. Minél jobb a feltöltött film minősége, annál nagyobb a fájlméret, ami nagyobb számítási teljesítményt és időt biztosít a videó feltöltésére.

Az arcészlelési funkció esetében például egy magasabb szintű megoldás segíthet abban a forgatókönyvben, amelyben sok kicsi, de környezetfüggően fontos arc van. Ez azonban a futásidő másodfokú növekedésében és a téves riasztások megnövekedett kockázatában fog növekedni.

Ezért javasoljuk, hogy ellenőrizze, hogy a megfelelő eredményeket érte-e el a saját esetében, és először helyileg tesztelje. Töltse fel ugyanezt a videót 720P-ben és 4K-ban, és hasonlítsa össze a betekintő adatokat.

## <a name="next-steps"></a>Következő lépések

[Az API által előállított Azure Video Indexer-kimenet vizsgálata](video-indexer-output-json-v2.md)