---
title: Nyelvi támogatás – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: A Bing Web Search API által támogatott természetes nyelvek, országok és régiók listája.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 6de01d2c12454f43a2802b4a7a979b5f74b46f0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96340271"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>A Bing Web Search API nyelvi és területi támogatása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Az Bing Web Search API több mint három tucat országot vagy régiót támogat, több mint egy nyelvet. Ha egy olyan országot vagy régiót ad meg, amely egy lekérdezéssel segíti a keresési eredmények pontosítását az adott ország vagy régió érdekei alapján. Az eredmények tartalmazhatnak a Bingre mutató hivatkozásokat, és ezek a hivatkozások a megadott ország/régió vagy nyelv alapján honosítják meg a Bing felhasználói élményt.

A lekérdezési paraméter segítségével megadhat egy országot vagy régiót `cc` . Ha meg van adva egy ország vagy régió, meg kell adnia egy vagy több, a [ `Accept-Language` fejlécet](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)tartalmazó nyelvi kódot. Az egyes piacokon támogatott nyelvek listáját a [Markets (piacok) táblában](#markets) végezheti el.

Másik lehetőségként megadhatja a piacot a `mkt` lekérdezési paraméterrel, valamint egy kódot a **piacok** táblából. A piac egyidejű megadása megadja az országot vagy régiót, valamint az előnyben részesített nyelvet. Explicit módon beállíthatja a nyelvet a `setLang` lekérdezési paraméterrel.

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

## <a name="next-steps"></a>Következő lépések

* [Bing Image Search API – referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)