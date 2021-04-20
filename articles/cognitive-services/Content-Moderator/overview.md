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
ms.date: 04/16/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, azure content moderator, online moderator, tartalomszűrési szoftver, tartalommoderálási szolgáltatás, tartalommoderálás
ms.openlocfilehash: b0ff5a241a76cd49d104e4145df37515b43c4e27
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726195"
---
# <a name="what-is-azure-content-moderator"></a>Mi az az Azure Content Moderator?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Content Moderator egy olyan AI-szolgáltatás, amely lehetővé teszi a potenciálisan sértő, kockázatos vagy egyéb nem kívánt tartalmak kezelését. Tartalmazza az AI-alapú tartalommoderálási szolgáltatást, amely szövegeket, képeket és videókat vizsgál, és automatikusan alkalmazza a tartalomjelölőket, valamint a felülvizsgálati eszközt, amely egy online moderator-környezet egy emberi felülvizsgáló csapat számára.

Előfordulhat, hogy tartalomszűrő szoftvert szeretne az alkalmazásba építeni, hogy megfeleljen a szabályozásnak, vagy fenntartsa a kívánt környezetet a felhasználók számára.

Ez a dokumentáció a következő cikktípusokat tartalmazza:  

* [**A rövid útmutatók**](client-libraries.md) olyan első lépések, amelyek végigvezetik a szolgáltatásnak indított kérések folyamatán.  
* [**Az útmutatók a**](try-text-api.md) szolgáltatás pontosabb vagy testreszabottabb módon való használatával kapcsolatos utasításokat tartalmaznak.  
* [**A fogalmak**](text-moderation-api.md) részletes magyarázatot nyújtanak a szolgáltatás funkcióiról és funkcióiról.  
* [**Az oktatóanyagok**](ecommerce-retail-catalog-moderation.md) hosszabb útmutatók, amelyekből megtudhatja, hogyan használhatja a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.  

## <a name="where-its-used"></a>A használata hely

Az alábbi forgatókönyvekben egy szoftverfejlesztőnek vagy csapatnak tartalommoderálási szolgáltatásra lenne szüksége:

- Online piacterek, amelyek moderálják a termékkatalógusokat és a felhasználók által létrehozott egyéb tartalmakat.
- Játékcégek, amelyek moderálják a felhasználók által létrehozott játék-műtermékeket és csevegőszobákat.
- Közösségi üzenetkezelési platformok, amelyek moderálják a felhasználók által hozzáadott képeket, szöveget és videókat.
- Vállalati médiavállalatok, amelyek központosított moderálást alkalmaznak a tartalmaikhoz.
- A K-12 oktatási megoldások szolgáltatói kiszűrik a diákok és oktatók számára nem megfelelő tartalmakat.

> [!IMPORTANT]
> Nem használhatja a Content Moderator a gyermek által kihasznált képek észlelésére. A minősített szervezetek azonban a [PhotoDNA Felhőszolgáltatással](https://www.microsoft.com/photodna "Microsoft PhotoDNA Cloud Service") is megszibadultatják az ilyen típusú tartalmakat.

## <a name="what-it-includes"></a>Mit tartalmaz?

A Content Moderator szolgáltatás számos webszolgáltatás API-t tartalmaz, amelyek REST-hívásokkal és .NET SDK-val is elérhetőek. Emellett tartalmazza a felülvizsgálati eszközt is, amely lehetővé teszi az emberi felülvizsgálók számára a szolgáltatás segítését, valamint a moderálási funkció továbbítását vagy finomhangolását.

## <a name="moderation-apis"></a>Moderálási API-k

Az Content Moderator szolgáltatás tartalmaz moderálási API-kat, amelyek a potenciálisan nem megfelelő vagy kifogásolható tartalmakat ellenőrzik.

![blokkdiagram a Content Moderator MODErálási API-khoz](images/content-moderator-mod-api.png)

Az alábbi táblázat a moderálási API-k különböző típusait ismerteti.

| API-csoport | Description |
| ------ | ----------- |
|[**Szövegmoderálás**](text-moderation-api.md)| Sértő tartalmakat, szexuálisan explicit vagy javaslattó tartalmakat, trágár kifejezéseket és személyes adatokat keres a szövegben.|
|[**Egyéni kifejezéslista**](try-terms-list-api.md)| A beépített kifejezésekkel együtt egy egyéni kifejezéslistán is átvizsgálja a szöveget. Az egyéni listákkal a saját tartalomházirendjének megfelelően tilthatja le vagy engedélyezheti a tartalmakat.|  
|[**Képmoderálás**](image-moderation-api.md)| Kiszűri a felnőtteknek szóló és kényes tartalmakat ábrázoló képeket, észleli a képeken található szövegeket az optikai karakterfelismerés (OCR) segítségével, valamint arcokat ismer fel.|
|[**Egyéni képlisták**](try-image-list-api.md)| Egyéni képlista alapján szűri a képeket. Egyéni képlistákkal szűrheti ki az olyan gyakran ismétlődő tartalmakat, amelyeket nem szeretne ismét besorolni.|
|[**Videomoderálás**](video-moderation-api.md)| Felnőtteknek szóló és kényes tartalmakat keres a videókban, és az ilyen tartalmakhoz időjelölőket ad vissza.|

## <a name="review-apis"></a>API-k áttekintése

A felülvizsgálati API-kkal emberi felülvizsgálókkal integrálhatja a moderálási folyamatot. A [Feladatok,](review-api.md#jobs) [a Felülvizsgálatok](review-api.md#reviews)és a Munkafolyamat műveletekkel hozhat létre és automatizálhat emberi munkafolyamatokat a felülvizsgálati eszközzel [(alább).](#review-tool) [](review-api.md#workflows)

> [!NOTE]
> A Workflow API még nem érhető el a .NET SDK-ban, de használható a REST-végponttal.

![blokkdiagram az Content Moderator API-khoz](images/content-moderator-rev-api.png)

## <a name="review-tool"></a>Felülvizsgálati eszköz

A Content Moderator szolgáltatás tartalmazza a webalapú felülvizsgálati eszközt [is,](Review-Tool-User-Guide/human-in-the-loop.md)amely az emberi moderátorok által feldolgozni használható tartalom-felülvizsgálatokat tartalmazza. Az emberi bemenet nem betanítja a szolgáltatást, de a szolgáltatás és az emberi felülvizsgálati csapatok együttes munkája lehetővé teszi a fejlesztők számára, hogy megfelelő egyensúlyt teremtsen a hatékonyság és a pontosság között. A felülvizsgálati eszköz felhasználóbarát kezelőfelületet biztosít számos Content Moderator számára.

![Content Moderator felülvizsgálati eszköz kezdőlapja](images/homepage.PNG)

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ahogy az összes Cognitive Services, a Content Moderator szolgáltatást használó fejlesztőknek is tisztában kell lenniük a Microsoft ügyféladatokkal kapcsolatos szabályzatával. További Cognitive Services [a](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) Microsoft Biztonsági és biztonsági központ Cognitive Services oldalon.

## <a name="next-steps"></a>Következő lépések

A webes portálon Content Moderator használatának első lépésekhez kövesse a Content Moderator [a weben való használatának első lépésekhez.](quick-start.md) Vagy töltse ki az [ügyfélkódtárat, vagy REST API rövid útmutatót](client-libraries.md) az alapszintű forgatókönyvek kódban való megvalósításához.