---
title: Áttelepítés Bing Speechról a Speech Service-be
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan telepíthet át egy meglévő Bing Speech-előfizetésből az Azure Cognitive Servicesból a Speech szolgáltatásba.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 81c4c26f252cdd9eb302a7f8f362c8bf52e48629
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91825590"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Áttelepítés Bing Speechról a beszédfelismerési szolgáltatásba

Ebből a cikkből megtudhatja, hogyan telepíthet alkalmazásokat a Bing Speech APIról a Speech szolgáltatásba.

Ez a cikk a Bing Speech API-k és a beszédfelismerési szolgáltatás közötti különbségeket ismerteti, és stratégiákat javasol az alkalmazások áttelepítéséhez. A Bing Speech API előfizetés kulcsa nem fog működni a Speech szolgáltatással; szüksége lesz egy új Speech Service-előfizetésre.

Egyetlen Speech Service-előfizetési kulcs biztosítja az alábbi funkciók elérését. Mindegyik funkció forgalmi díját külön állapítjuk meg, így csak a használt funkciókért kell fizetnie.

* [Diktálás](speech-to-text.md)
* [Egyéni beszédfelismerés](https://cris.ai)
* [Szövegfelolvasás](text-to-speech.md)
* [Egyéni szövegfelolvasási hangok](how-to-customize-voice-font.md)
* [Tolmácsolás](speech-translation.md) (nem tartalmaz [szövegfordítást](../translator/translator-info-overview.md))

A [SPEECH SDK](speech-sdk.md) a Bing Speech ügyféloldali kódtárak funkcionális cseréje, de más API-t használ.

## <a name="comparison-of-features"></a>Funkciók összehasonlítása

A beszédfelismerési szolgáltatás nagymértékben hasonló a Bing Speechhoz, a következő eltérésekkel.

| Szolgáltatás | Bing – Beszédfelismerés | Speech szolgáltatás | Részletek |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | A Speech Service támogatja a Windows 10, a Univerzális Windows-platform (UWP) és a .NET Standard 2,0. |
| C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | A Speech Service támogatja a Windowst és a Linuxot. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | A beszédfelismerési szolgáltatás támogatja az androidos és a beszédfelismerési eszközöket. |
| Folyamatos beszédfelismerés | 10 perc | Korlátlan | A Speech SDK támogatja a korlátlan folyamatos felismerést, és automatikusan újrakapcsolódik az időtúllépés vagy a kapcsolat bontása után. |
| Részleges vagy ideiglenes eredmények | :heavy_check_mark: | :heavy_check_mark: | Támogatott a Speech SDK-val. |
| Egyéni beszédfelismerési modellek | :heavy_check_mark: | :heavy_check_mark: | Bing Speech külön Custom Speech-előfizetést igényel. |
| Egyéni hangbetűkészletek | :heavy_check_mark: | :heavy_check_mark: | Bing Speech külön egyéni hangalapú előfizetést igényel. |
| 24 kHz-es hangok | : heavy_minus_sign: | :heavy_check_mark: |
| Beszédfelismerési szándék felismerése | Külön LUIS API-hívást igényel | Integrált (SDK-val) | A Speech Service-ben LUIS-kulcsot is használhat. |
| Egyszerű szándék felismerése | : heavy_minus_sign: | :heavy_check_mark: |
| Hosszú hangfájlok kötegelt átírása | : heavy_minus_sign: | :heavy_check_mark: |
| Felismerési mód | Manuális végponti URI-n keresztül | Automatikus | A beszédfelismerési mód nem érhető el a Speech szolgáltatásban. |
| Végpont helye | Globális | Regionális | A regionális végpontok javítják a késést. |
| REST API-k | :heavy_check_mark: | :heavy_check_mark: | A beszédfelismerési szolgáltatás REST API-jai kompatibilisek Bing Speech (eltérő végponttal). A REST API-k szöveg-beszéd és korlátozott beszéd-szöveg funkciókat támogatnak. |
| WebSocket-protokollok | :heavy_check_mark: | : heavy_minus_sign: | A Speech SDK absztrakt webes szoftvercsatorna-kapcsolatai olyan funkciókat foglalnak magukban, amelyek állandó kapcsolatot igényelnek a szolgáltatással, így a továbbiakban nem támogatott a manuális előfizetés. |
| Szolgáltatások közötti API-hívások | :heavy_check_mark: | : heavy_minus_sign: | Bing Speech biztosítva a C# szolgáltatási könyvtáron keresztül. |
| Nyílt forráskódú SDK | :heavy_check_mark: | : heavy_minus_sign: |

A beszédfelismerési szolgáltatás időalapú árképzési modellt használ (nem pedig tranzakció alapú modell). A részletekért tekintse meg a [Speech Service díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Migrálási stratégiák

Ha Ön vagy a szervezete olyan fejlesztést vagy éles környezetben futó alkalmazásokat használ, amelyek Bing Speech API használnak, a lehető leghamarabb frissítse őket a beszédfelismerési szolgáltatás használatára. Tekintse meg a [beszédfelismerési szolgáltatás dokumentációját](index.yml) , amely tartalmazza az elérhető SDK-kat, a kódot és az oktatóanyagokat.

A beszédfelismerési szolgáltatás [REST API](rest-apis.md) -jai kompatibilisek a Bing Speech API-kkal. Ha jelenleg a Bing Speech REST API-kat használja, csak a REST-végpontot kell módosítania, és váltania kell egy Speech Service-előfizetési kulcsra.

Ha egy Bing Speech ügyféloldali függvénytárat használ egy adott programozási nyelvhez, a [SPEECH SDK](speech-sdk.md) -ba való Migrálás megköveteli az alkalmazás módosítását, mivel az API különbözik. A beszédfelismerési SDK lehetővé teszi a kód egyszerűbb használatát, miközben az új funkciókhoz is hozzáférést biztosít. A Speech SDK számos programozási nyelven elérhető. Az API-k minden platformon hasonlóak, és megkönnyítik a többplatformos fejlesztést.

A beszédfelismerési szolgáltatás nem biztosít globális végpontot. Állapítsa meg, hogy az alkalmazás hatékonyan működik-e, ha egyetlen regionális végpontot használ az összes adatforgalmához. Ha nem, a leghatékonyabb végpont meghatározásához használja a térinformatikat. Minden használt régióban külön Speech Service-előfizetésre van szükség.

Ismerkedés a Speech SDK-val:

1. Töltse le a [SPEECH SDK](speech-sdk.md)-t.
1. A Speech Service rövid [útmutatók](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) és [oktatóanyagok](how-to-recognize-intents-from-speech-csharp.md)használata. Tekintse meg a [kód mintáit](samples.md) is, és ismerkedjen meg az új API-kkal.
1. Az alkalmazás frissítése a beszédfelismerési szolgáltatás használatára.

## <a name="support"></a>Támogatás

Bing Speech ügyfeleknek [támogatási jegy](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával kell felvenniük a kapcsolatot az ügyfélszolgálattal. Ha [technikai támogatási csomagra](https://azure.microsoft.com/support/plans/)van szüksége, vegye fel velünk a kapcsolatot is.

A Speech Service, az SDK és az API támogatásához látogasson el a Speech Service [támogatási oldalára](support.md).

## <a name="next-steps"></a>Következő lépések

* [Próbálja ki ingyenesen a Speech szolgáltatást](overview.md#try-the-speech-service-for-free)
* [A diktálás használatának első lépései](get-started-speech-to-text.md)
* [Ismerkedés a szövegfelolvasással](get-started-text-to-speech.md)

## <a name="see-also"></a>Lásd még

* [Beszédfelismerési szolgáltatás kibocsátási megjegyzései](releasenotes.md)
* [Mi a beszédfelismerési szolgáltatás?](overview.md)
* [A Speech Service és a Speech SDK dokumentációja](speech-sdk.md#get-the-speech-sdk)
