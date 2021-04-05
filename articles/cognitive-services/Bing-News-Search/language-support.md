---
title: Nyelvi támogatás – Bing News Search API
titleSuffix: Azure Cognitive Services
description: A Bing News Search API által támogatott természetes nyelvek, országok és régiók listája.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: 9c7fd03c2239cea05dc79ad4dd1965fe253a2ce9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341597"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>A Bing News Search API nyelvi és területi támogatása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing News Search API számos országot/régiót támogat, több mint egy nyelvet. A lekérdezéssel rendelkező ország/régió megadásával elsődlegesen az adott országban vagy régióban lévő érdekeltségek alapján pontosíthatja a keresési eredményeket. Emellett az eredmények a Bingre mutató hivatkozásokat is tartalmazhatnak, és ezek a hivatkozások a megadott ország/régió vagy nyelv alapján lokalizálják a Bing felhasználói élményt.

A lekérdezési paraméter segítségével megadhat egy országot/régiót `cc` . Ha országot vagy régiót ad meg, meg kell adnia egy vagy több nyelvi kódot is a `Accept-Language` http-fejléc használatával. A támogatott nyelvek a Counter/régió szerint változnak; a piacok táblában minden országhoz/régióhoz meg vannak adva.

Azt is megteheti, hogy megadta a piacot a `mkt` lekérdezési paraméter és a **piacok** tábla kódjának használatával. A piac egyidejű megadása megadja az országot/régiót és az előnyben részesített nyelvet. `setLang`Ebben az esetben a lekérdezési paramétert lehet beállítani nyelvi kódra, ez általában ugyanaz a nyelv, `mkt` Ha a felhasználó egy másik nyelven szeretné megjeleníteni a binget.

## <a name="supported-markets-for-news-search-endpoint"></a>A Hírek keresési végpontjának támogatott piacai

A `/news/search` végpont esetében a következő táblázat a lekérdezési paraméter megadásához használható piaci kódok értékeit sorolja fel `mkt` . A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

A lekérdezési paraméterben megadható ország/régió kódok listáját itt `cc` tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|Dán|da-DK|
|Ausztria|Német|de-AT|
|Svájc|Német|de-CH|
|Németország|Német|de-DE|
|Ausztrália|Angol|EN-AU|
|Kanada|Angol|en-CA|
|Egyesült Királyság|Angol|en-GB|
|Indonézia|Angol|EN-ID|
|Írország|Angol|EN-IE|
|India|Angol|EN-IN|
|Malajzia|Angol|EN-MY|
|Új-Zéland|Angol|EN-NZ|
|Fülöp-szigeteki Köztársaság|Angol|EN-PH|
|Szingapúr|Angol|EN-SG|
|Egyesült Államok|Angol|en-US|
|Angol|általános|EN-WW|
|Angol|általános|EN-XA|
|Dél-afrikai Köztársaság|Angol|EN-ZA|
|Argentína|Spanyol|es-AR|
|Chile|Spanyol|es – CL|
|Spanyolország|Spanyol|es-ES|
|Mexikó|Spanyol|es-MX|
|Egyesült Államok|Spanyol|es – USA|
|Spanyol|általános|es-XL|
|Finnország|finn|fi-FI|  
|Franciaország|Francia|fr-BE|
|Kanada|Francia|fr – CA|
|Belgium|Holland|nl-BE|
|Svájc|Francia|fr – CH|
|Franciaország|Francia|fr-FR|  
|Olaszország|Olasz|it-IT|
|Hongkong (KKT)|Kínai (hagyományos)|ZH-HK|  
|Tajvan|Kínai (hagyományos)|zh-TW|
|Japán|Japán|ja-JP|  
|Dél-Korea|Koreai|ko-KR|  
|Hollandia|Holland|nl-NL|  
|Kínai Népköztársaság|Kínai|zh-CN|  
|Brazília|Portugál|pt-BR|
|Oroszország|Orosz|ru-RU|  
|Svédország|svéd|sv-SE|  
|Törökország|Török|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>A hírek végpontjának támogatott piacai
A `/news` végpont esetében a következő táblázat a lekérdezési paraméter megadásához használható piaci kódok értékeit sorolja fel `mkt` . A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

A lekérdezési paraméterben megadható ország/régió kódok listáját itt `cc` tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|Dán|da-DK|
|Németország|Német|de-DE|
|Ausztrália|Angol|EN-AU|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Angol|általános|EN-WW|
|Chile|Spanyol|es – CL|
|Mexikó|Spanyol|es-MX|
|Egyesült Államok|Spanyol|es – USA|
|Finnország|finn|fi-FI|  
|Kanada|Francia|fr – CA|
|Franciaország|Francia|fr-FR|  
|Olaszország|Olasz|it-IT|
|Brazília|Portugál|pt-BR|
|Kínai Népköztársaság|Kínai|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Támogatott piacok a hírek trendi végpontja számára
A `/news/trendingtopics` végpont esetében a következő táblázat a lekérdezési paraméter megadásához használható piaci kódok értékeit sorolja fel `mkt` . A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

A lekérdezési paraméterben megadható ország/régió kódok listáját itt `cc` tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Németország|Német|de-DE|
|Ausztrália|Angol|EN-AU|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Kanada|Angol|en-CA|
|India|Angol|EN-IN|
|Franciaország|Francia|fr-FR|
|Kanada|Francia|fr – CA|
|Brazília|Portugál|pt-BR|
|Kínai Népköztársaság|Kínai|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Országkódok  

A lekérdezési paraméterben megadható ország/régió kódok a következők: `cc` A lista módosítása változhat.  

|Ország/régió|Országkód|  
|---------------------|------------------|  
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
|Kínai Népköztársaság|CN|  
|Lengyelország|PL|  
|Portugália|PT|  
|Fülöp-szigeteki Köztársaság|PH|  
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

## <a name="next-steps"></a>Következő lépések
További információ az Bing News Search-végpontokról: [News Search API v7-dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).