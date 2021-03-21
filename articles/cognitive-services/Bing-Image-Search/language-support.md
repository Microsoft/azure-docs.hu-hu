---
title: Nyelvi támogatás – Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, mely országokat/régiókat és nyelveket támogatja a Bing Image Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 14dacc4351af444c75517df4d4e02ecca3bbf40a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350075"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>A Bing Image Search API nyelvi és területi támogatása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Az Bing Image Search API több mint három tucat országot/régiót támogat, több mint egy nyelvet. A lekérdezéssel rendelkező ország/régió megadásával elsődlegesen az adott országban vagy régióban lévő érdekeltségek alapján pontosíthatja a keresési eredményeket. Emellett az eredmények a Bingre mutató hivatkozásokat is tartalmazhatnak, és ezek a hivatkozások a megadott ország/régiók vagy nyelv alapján lokalizálják a Bing felhasználói élményt.

Az ország/régió és a nyelv megadásához állítsa be a `mkt` (piaci) lekérdezési paramétert az alábbi **piacok** táblából származó kódra. A piac egy országot/régiót és nyelvet is meghatároz. Ha a felhasználó egy másik nyelven szeretné megtekinteni a megjelenítendő szöveget, állítsa `setLang` a lekérdezési paramétert a megfelelő nyelvi kódra.

Azt is megteheti, hogy a lekérdezési paraméterrel megadhatja az országot/régiót `cc` . Ha országot vagy régiót ad meg, meg kell adnia egy vagy több nyelvi kódot is a `Accept-Language` http-fejléc használatával. A támogatott nyelvek ország/régió szerint változnak; a piacok táblában minden országhoz/régióhoz meg vannak adva.

> [!NOTE]
> A trend images API jelenleg csak a következő piacokat támogatja:
> - en-US (angol, Egyesült Államok)
> - en-CA (angol, Kanada)
> - EN-AU (angol, Ausztrália)
> - zh-CN (kínai, Kína)

## <a name="countriesregions"></a>Országok/régiók

|Ország/régió|Code|
|-------|----|
|Argentína|AR|
|Ausztrália|AU|
|Ausztria|AT|
|Belgium|BE|
|Brazília|BR|
|Kanada|CA|
|Chile|CL|
|Dánia|DK|
|Finnország|FI|
|Franciaország|JK|
|Németország|DE|
|Hongkong (KKT)|HK|
|India|IN|
|Indonézia|ID (Azonosító)|
|Olaszország|IT|
|Japán|JP|
|Dél-Korea|KR|
|Malajzia|MY|
|Mexikó|MX|
|Hollandia|NL|
|Új-Zéland|NZ|
|Norvégia|NO|
|Kína|CN|
|Lengyelország|PL|
|Portugália|PT|
|Fülöp-szigetek|PH|
|Oroszország|RU|
|Szaúd-Arábia|SA|
|Dél-afrikai Köztársaság|ZA|
|Spanyolország|ES|
|Svédország|SE|
|Svájc|CH|
|Tajvan|TW|
|Törökország|TR|
|Egyesült Királyság|GB|
|Egyesült Államok|USA|


## <a name="markets"></a>Piacok

|Ország/régió|Nyelv|Piaci kód|
|-------|--------|-----------|
|Argentína|Spanyol|es-AR|
|Ausztrália|Angol|EN-AU|
|Ausztria|Német|de-AT|
|Belgium|Holland|nl-BE|
|Belgium|Francia|fr-BE|
|Brazília|Portugál|pt-BR|
|Kanada|Angol|en-CA|
|Kanada|Francia|fr – CA|
|Chile|Spanyol|es – CL|
|Dánia|Dán|da-DK|
|Finnország|finn|fi-FI|
|Franciaország|Francia|fr-FR|
|Németország|Német|de-DE|
|Hongkong (KKT)|Kínai (hagyományos)|ZH-HK|
|India|Angol|EN-IN|
|Indonézia|Angol|EN-ID|
|Olaszország|Olasz|it-IT|
|Japán|Japán|ja-JP|
|Dél-Korea|Koreai|ko-KR|
|Malajzia|Angol|EN-MY|
|Mexikó|Spanyol|es-MX|
|Hollandia|Holland|nl-NL|
|Új-Zéland|Angol|EN-NZ|
|Kína|Kínai|zh-CN|
|Lengyelország|Lengyel|pl-PL|
|Portugália|Portugál|pt-PT|
|Fülöp-szigetek|Angol|EN-PH|
|Oroszország|Orosz|ru-RU|
|Szaúd-Arábia|Arab|ar-SA|
|Dél-afrikai Köztársaság|Angol|EN-ZA|
|Spanyolország|Spanyol|es-ES|
|Svédország|svéd|sv-SE|
|Svájc|Francia|fr – CH|
|Svájc|Német|de-CH|
|Tajvan|Kínai (hagyományos)|zh-TW|
|Törökország|Török|tr-TR|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Egyesült Államok|Spanyol|es – USA|

## <a name="next-steps"></a>Következő lépések
További információ az Bing News Search-végpontokról: [hírek Image Search API v7-dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).