---
title: Megfontolandó szempontok Video Indexer használatakor – Azure
titleSuffix: Azure Media Services
description: Ez a témakör ismerteti, hogy milyen szempontokat kell figyelembe vennie a Video Indexer méretezése során.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: how-to
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: b955c0f494b757fd29c400194ef8b11314a89a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96483610"
---
# <a name="things-to-consider-when-using-video-indexer-at-scale"></a>Megfontolandó szempontok Video Indexer méretezéskor

Ha a videók indexeléséhez Azure Media Services video Indexer-t használ, és a videók archiválása egyre nő, érdemes lehet méretezni a méretezést. 

Ez a cikk a következő kérdésekre ad választ:

* Kell-e figyelembe venni valamilyen technológiai korlátozást?
* Van intelligens és hatékony módja?
* Meg lehet akadályozni, hogy a folyamat feleslegesen költ pénzt?

A cikk hat ajánlott eljárást biztosít a Video Indexer méretezésének használatáról.

## <a name="when-uploading-videos-consider-using-a-url-over-byte-array"></a>Videók feltöltésekor érdemes egy URL-címet használni a byte Array használatával

Video Indexer lehetővé teszi a videók URL-címről való feltöltését, vagy közvetlenül a fájl bájt tömbként való elküldését, az utóbbi bizonyos korlátozásokat tartalmaz. További információkért lásd: [szempontok és korlátozások feltöltése.](upload-index-videos.md#uploading-considerations-and-limitations)

Először is a fájlméret korlátozásai vannak. A bájtos tömb mérete legfeljebb 2 GB lehet, mint az URL-cím használatakor a 30 GB-os feltöltési méretre vonatkozó korlátozás.

Másodszor, vegye figyelembe, hogy csak néhány olyan probléma van, amely hatással lehet a teljesítményre, és így képes a méretezésre:

* A több részből álló fájlok küldése nagy függőséget jelent a hálózaton, 
* szolgáltatás megbízhatósága, 
* connectivity 
* feltöltési sebesség, 
* elveszett csomagok valahol a világhálón.

:::image type="content" source="./media/considerations-when-use-at-scale/first-consideration.png" alt-text="Első megfontolás a Video Indexer használatának méretezéséhez":::

Ha URL-cím használatával tölt fel videókat, csak meg kell adnia a médiafájl helyének elérési útját, és Video Indexer gondoskodik a többiről (lásd a `videoUrl` [videó feltöltése](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload) API-ban található mezőt).

> [!TIP]
> Használja a `videoUrl` videó feltöltése API opcionális paraméterét.

Ha szeretné megtekinteni, hogyan tölthet fel videókat URL-cím használatával, tekintse meg [ezt a példát](upload-index-videos.md#code-sample). A [AzCopy](../../storage/common/storage-use-azcopy-v10.md) segítségével gyorsan és megbízhatóan is használhatja a tartalmat egy olyan Storage-fiókba, amelyről beküldheti video Indexer [sas URL-cím](../../storage/common/storage-sas-overview.md)használatával.

## <a name="increase-media-reserved-units-if-needed"></a>Szükség esetén növelje a Media szolgáltatás számára fenntartott egységeket

Általában a Video Indexer használatának megkezdése esetén a koncepció igazolása során nincs szükség sok számítási teljesítményre. Amikor elkezdi a videók nagyobb archívumának indexelését, és azt szeretné, hogy a folyamat olyan tempóban legyen, amely megfelel a használati esetnek, a Video Indexer használatának vertikális felskálázására van szükség. Ezért érdemes megnövelni a felhasznált számítási erőforrások számát, ha a számítási teljesítmény jelenlegi mennyisége nem elég.

A Azure Media Services, ha a számítási teljesítményt és a párhuzamos szeretné bővíteni, figyelmet kell fordítania a Media szolgáltatás [számára fenntartott egységekre](../latest/concept-media-reserved-units.md)(RUs). Az RUs azokat a számítási egységeket határozza meg, amelyek meghatározzák a média feldolgozási feladatainak paramétereit. Az RUs száma befolyásolja az egyes fiókokban egyidejűleg feldolgozható adathordozó-feladatok számát, és a típusuk határozza meg a feldolgozás sebességét, és egy videóhoz több RU is szükség lehet, ha az indexelése összetett. Ha az RUs foglalt, az új feladatok egy várólistán lesznek tárolva, amíg egy másik erőforrás nem érhető el.

A hatékony működéshez és az olyan erőforrások elkerüléséhez, amelyek üresjáratban maradnak, a Video Indexer egy automatikus méretezési rendszerből áll, amely a kevesebb feldolgozásra van szükség, és ha a Rush (akár teljes egészében is használja az összes RUs-t). Ezt a funkciót úgy engedélyezheti, ha [bekapcsolja az autoskálázást](manage-account-connected-to-azure.md#autoscale-reserved-units) a Fiókbeállítások vagy az [Update-Paid-Account-Azure-Media-Services API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&pattern=update)használatával.

:::image type="content" source="./media/considerations-when-use-at-scale/second-consideration.jpg" alt-text="Második megfontolás a Video Indexer használatának méretezéséhez":::

:::image type="content" source="./media/considerations-when-use-at-scale/reserved-units.jpg" alt-text="AMS számára fenntartott egységek":::

Az indexelési időtartam és az alacsony átviteli sebesség minimálisra csökkentése érdekében javasoljuk, hogy az S3 típusú 10 RUs-t kell kezdenie. Később, ha a vertikális felskálázással több tartalmat vagy nagyobb párhuzamosságot támogat, és további erőforrásokra van szüksége, akkor [a támogatási rendszer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) (csak a fizetett fiókok esetében) használatával kérheti a további RUs-foglalást.

## <a name="respect-throttling"></a>A szabályozás tiszteletben tartása

A Video Indexer úgy van felépítve, hogy az indexelést a skálán kezeljék, és ha a legtöbbet szeretné kipróbálni belőle, érdemes figyelembe vennie a rendszer képességeit, és ennek megfelelően kell megterveznie az integrációt. Ha nem szeretne feltöltési kérést küldeni a videók egy kötegére, csak hogy felfedezzék, hogy a filmek némelyike nem lett feltöltve, és a HTTP 429-hibakód (túl sok kérés) érkezik. Ez azért fordulhat elő, mert több kérést küldött, mint a [videó percenkénti korlátja](upload-index-videos.md#uploading-considerations-and-limitations). Video Indexer egy `retry-after` fejlécet ad a HTTP-válaszhoz, a fejléc azt adja meg, hogy mikor próbálkozzon újra a következő próbálkozással. Győződjön meg arról, hogy a következő kérelem kipróbálása előtt tiszteletben tartja.

:::image type="content" source="./media/considerations-when-use-at-scale/respect-throttling.jpg" alt-text="Tervezze meg az integrációt, és tartsa tiszteletben a szabályozást":::

## <a name="use-callback-url"></a>Visszahívási URL-cím használata

Azt javasoljuk, hogy a kérelem állapotának a másodpercből való folyamatos lekérdezése helyett egy [visszahívási URL-címet](upload-index-videos.md#callbackurl)adjon hozzá, és várjon, amíg a video Indexer frissül. Amint bármilyen állapot módosul a feltöltési kérelemben, a megadott URL-címre értesítést kap.

Visszahívási URL-címet a [videó feltöltése API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Upload-Video?&pattern=upload)egyik paramétereként adhat hozzá. Tekintse meg a kód mintáit a [GitHub](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/)-tárházban. 

Visszahívási URL-cím esetén használhatja a Azure Functions, egy kiszolgáló nélküli, eseményvezérelt platformot, amelyet HTTP-vel indíthat el, és végrehajthat egy következő folyamatot.

### <a name="callback-url-definition"></a>visszahívási URL-definíció

[!INCLUDE [callback url](./includes/callback-url.md)]

## <a name="use-the-right-indexing-parameters-for-you"></a>Használja a megfelelő indexelési paramétereket

Ha Video Indexer használatával kapcsolatos döntéseket szeretne hozni, tekintse meg, hogyan hozhatja ki a legtöbbet az igényeinek megfelelő paraméterekkel. Gondoljon a használati esetre, ha különböző paramétereket határoz meg, így pénzt takaríthat meg, és gyorsabbá teheti a videók indexelését.

A videó feltöltése és indexelése előtt olvassa el ezt a rövid [dokumentációt](upload-index-videos.md), és tekintse meg a [IndexingPreset](upload-index-videos.md#indexingpreset) és a [streamingPreset](upload-index-videos.md#streamingpreset) , hogy jobb képet kapjon a lehetőségeiről.

Ha például nem szeretné megtekinteni a videó megtekintését, ne állítsa be az előre beállított értéket adatfolyamként, ne indexelje a videók elemzését, ha csak hangelemzésre van szüksége.

## <a name="index-in-optimal-resolution-not-highest-resolution"></a>Index az optimális felbontásban, nem a legmagasabb megoldás

Lehet, hogy a videók indexeléséhez milyen minőségi videóra van szüksége? 

Sok esetben az indexelési teljesítmény csaknem nem különbözik a HD (720P) videók és a 4K-videók között. Végül szinte ugyanazokat az ismereteket fogja kapni ugyanazzal a megbízhatósággal. Minél magasabb a feltöltött film minősége, annál nagyobb lesz a fájlméret, és ez nagyobb számítási teljesítményt és időt eredményez a videó feltöltéséhez.

Például a Arcfelismerés funkció esetében a magasabb felbontás segíthet a forgatókönyvben, ahol sok kicsi, de kontextusban fontos arc van. Ez azonban egy másodfokú növekedést jelent a futtatókörnyezetben, és növeli a téves pozitív állapotot.

Ezért javasoljuk, hogy ellenőrizze, hogy a használati esethez megfelelő eredményeket kap-e, és hogy először tesztelje azt helyileg. Töltse fel ugyanazt a videót a 720P-ban és a 4K-ban, és hasonlítsa össze az észlelt eredményeket.

## <a name="next-steps"></a>Következő lépések

[Az API által létrehozott Azure Video Indexer-kimenet vizsgálata](video-indexer-output-json-v2.md)