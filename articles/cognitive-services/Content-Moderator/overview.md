---
title: Mi az az Azure Content Moderator?
titleSuffix: Azure Cognitive Services
description: Megismerheti, hogyan használható a Content Moderator a felhasználók által létrehozott tartalmakban található nem megfelelő anyagok nyomon követésére, megjelölésére, értékelésére és szűrésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: Content moderátor, Azure Content moderátor, online moderátor, Content Filtering szoftver, Content moderációs szolgáltatás, tartalom moderálása
ms.openlocfilehash: a53611fdad84f06661f3b8928296b6a45851cea4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867270"
---
# <a name="what-is-azure-content-moderator"></a>Mi az az Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Content Moderator egy AI-szolgáltatás, amely potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos tartalmak kezelésére szolgál. Magában foglalja az AI-alapú Content moderációs szolgáltatást, amely szöveget, képet és videót vizsgál, és automatikusan alkalmazza a tartalmi jelzőket, valamint a felülvizsgálati eszközt, amely egy, az emberi felülvizsgálók csapatának online moderátori környezete.

Előfordulhat, hogy az alkalmazásba kívánja felépíteni a Content Filtering szoftvert, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.

Ez a dokumentáció a következő cikk-típusokat tartalmazza:  

* [**A gyors**](client-libraries.md) üzembe helyezési útmutató végigvezeti Önt a szolgáltatásra irányuló kérések lépésein.  
* A [**útmutatók**](try-text-api.md) útmutatói a szolgáltatás részletesebb vagy testreszabott módokon történő használatára vonatkozó utasításokat tartalmaznak.  
* A [**fogalmak**](text-moderation-api.md) részletesen ismertetik a szolgáltatás funkcióit és funkcióit.  
* Az [**oktatóanyagok**](ecommerce-retail-catalog-moderation.md) már olyan útmutatók, amelyek bemutatják, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  

## <a name="where-its-used"></a>Hol használják

Az alábbiakban néhány olyan forgatókönyvet ismertetünk, amelyekben a szoftverfejlesztő vagy a csapat egy Content moderációs szolgáltatást igényel:

- Online piactér, amely mérsékelt termékkatalógusokat és más, felhasználó által létrehozott tartalmat mutat be.
- A nagyvállalatok által generált játék-összetevőkkel és csevegési szobákkal rendelkező vállalatok.
- A közösségi üzenetkezelési platformok, amelyek a felhasználók által hozzáadott képeket, szövegeket és videókat vesznek igénybe.
- Vállalati média-vállalatok, amelyek központosított moderálást valósítanak meg a tartalmukhoz.
- A K-12 oktatási megoldás-szolgáltatók olyan tartalmat szűrnek ki, amely nem felel meg a diákoknak és a pedagógusoknak.

> [!IMPORTANT]
> Nem használhatja a Content Moderatort a gyermekek illegális kiaknázására szolgáló rendszerképek észlelésére. A minősített szervezetek azonban használhatják a [PhotoDNA Cloud Service](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") -t az ilyen típusú tartalmak megjelenítésére.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Content Moderator szolgáltatás számos webszolgáltatás API-t tartalmaz, amelyek REST-hívásokkal és .NET SDK-val is elérhetőek. Emellett magában foglalja a felülvizsgálati eszközt is, amely lehetővé teszi, hogy az emberi felülvizsgálók segítsek a szolgáltatást, és javítják vagy tökéletesítsék a moderálási funkcióját.

## <a name="moderation-apis"></a>Moderálási API-k

A Content Moderator szolgáltatás olyan moderálási API-kat tartalmaz, amelyek potenciálisan nem megfelelő vagy kifogásolt anyagok tartalmának ellenőrzését is tartalmazzák.

![Content Moderator moderálási API-k diagramjának letiltása](images/content-moderator-mod-api.png)

Az alábbi táblázat a moderálási API-k különböző típusait ismerteti.

| API-csoport | Leírás |
| ------ | ----------- |
|[**Szövegmoderálás**](text-moderation-api.md)| Megvizsgálja a sértő tartalom, a szexuálisan explicit vagy szuggesztív tartalom, a káromkodás és a személyes adatokat szövegét.|
|[**Egyéni kifejezéslista**](try-terms-list-api.md)| A szöveget a beépített kifejezésekkel együtt a kifejezések egyéni listájában vizsgálja. Az egyéni listákkal a saját tartalomházirendjének megfelelően tilthatja le vagy engedélyezheti a tartalmakat.|  
|[**Képmoderálás**](image-moderation-api.md)| Kiszűri a felnőtteknek szóló és kényes tartalmakat ábrázoló képeket, észleli a képeken található szövegeket az optikai karakterfelismerés (OCR) segítségével, valamint arcokat ismer fel.|
|[**Egyéni képlisták**](try-image-list-api.md)| Egyéni képlista alapján szűri a képeket. Egyéni képlistákkal szűrheti ki az olyan gyakran ismétlődő tartalmakat, amelyeket nem szeretne ismét besorolni.|
|[**Videomoderálás**](video-moderation-api.md)| Felnőtteknek szóló és kényes tartalmakat keres a videókban, és az ilyen tartalmakhoz időjelölőket ad vissza.|

## <a name="review-apis"></a>API-k áttekintése

A felülvizsgálati API-k lehetővé teszik a moderálási folyamat és az emberi felülvizsgálók integrálását. A [feladatok](review-api.md#jobs) [, a felülvizsgálatok](review-api.md#reviews)és a [munkafolyamat](review-api.md#workflows) -műveletek használatával létrehozhat és automatizálhat humán-in-the-loop-munkafolyamatokat a [felülvizsgálati eszközzel](#review-tool) (alább).

> [!NOTE]
> A munkafolyamat API még nem érhető el a .NET SDK-ban, de a REST-végponttal is használható.

![Content Moderator felülvizsgálati API-k diagramjának letiltása](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Felülvizsgálati eszköz

A Content Moderator szolgáltatás magában foglalja a webalapú [felülvizsgálati eszközt](Review-Tool-User-Guide/human-in-the-loop.md)is, amely az emberi Moderátorok által feldolgozható tartalmi felülvizsgálatokat üzemelteti. Az emberi bevitel nem a szolgáltatás betanítása, hanem a szolgáltatás és az emberi felülvizsgálati csapatok együttes munkája lehetővé teszi a fejlesztők számára, hogy a hatékonyság és a pontosság közötti megfelelő egyensúlyt megtalálják. A felülvizsgálati eszköz egy felhasználóbarát kezelőfelületet is biztosít számos Content Moderator erőforráshoz.

![Content Moderator felülvizsgálati eszköz kezdőlapja](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes Cognitive Services esetében, a Content Moderator szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Az Content Moderator a webes portálon való használatának megkezdéséhez kövesse [az interneten található Content moderator kipróbálása](quick-start.md)című témakört. Vagy végezzen el egy [ügyféloldali kódtárat vagy REST API](client-libraries.md) rövid útmutatót a kódban található alapszintű forgatókönyvek megvalósításához.