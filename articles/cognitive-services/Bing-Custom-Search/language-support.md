---
title: Nyelvi támogatás – Bing Custom Search API
titleSuffix: Azure Cognitive Services
description: A Bing Custom Search API támogatott nyelveinek és régióinak listája.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 73b25ce1093ce697aa0937161d2af49526c231b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352354"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>A Bing Custom Search API nyelvi és területi támogatása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Az Bing Custom Search API több mint három tucat országot/régiót támogat, több mint egy nyelvet.

Bár ez nem kötelező, a kérésnek meg kell adnia a [MKT](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) lekérdezési paramétert, amely azonosítja azt a piacot, ahol az eredményeket el szeretné jutni. A választható lekérdezési paraméterek listáját lásd: [lekérdezési paraméterek](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

A lekérdezési paraméter segítségével megadhat egy országot/régiót `cc` . Ha országot vagy régiót ad meg, meg kell adnia egy vagy több nyelvi kódot is a `Accept-Language` fejléc használatával. A támogatott nyelvek ország/régió szerint változnak; a **piacok** táblában minden országhoz/régióhoz meg vannak adva.

A `Accept-Language` fejléc és a `setLang` lekérdezési paraméter kölcsönösen kizárható – ne határozza meg mindkettőt. Részletekért lásd: [elfogadás – nyelv](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

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
|Norvégia|Norvég|nem – nem|
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