---
title: Nyelvi támogatás – Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: A Bing Visual Search API által támogatott természetes nyelvek, országok és régiók listája. Az Bing Visual Search API több mint három tucat országot/régiót támogat, több mint egy nyelvet.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 6d93b6d024fc966f3b260602f4798a2d77669a35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96501158"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>A Bing Visual Search API nyelvi és területi támogatása

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Visual Search API több mint három tucat országot/régiót támogat, több mint egy nyelvet. Minden kérelemnek tartalmaznia kell a felhasználó országát/régióját és a választott nyelvet. A felhasználó piacának ismerete segít a Bingnek a megfelelő eredmények visszaküldésében. Ha nem ad meg országot/régiót és nyelvet, a Bing a legmegfelelőbb módon határozza meg a felhasználó országát/régióját és nyelvét. Mivel az eredmények a Bingre mutató hivatkozásokat is tartalmazhatnak, az ország/régió és a nyelv ismerete előnyben részesített honosított Bing felhasználói élményt biztosíthat, ha a felhasználó a Bing hivatkozásokra kattint.

Az ország/régió és a nyelv megadásához állítsa be a `mkt` (piaci) lekérdezési paramétert az alábbi **piacok** táblából származó kódra. A piac egy országot/régiót és nyelvet is meghatároz. Ha a felhasználó egy másik nyelven szeretné megtekinteni a megjelenítendő szöveget, állítsa `setLang` a lekérdezési paramétert a megfelelő nyelvi kódra.

Azt is megteheti, hogy a lekérdezési paraméterrel megadhatja az országot/régiót `cc` . Ha országot vagy régiót ad meg, meg kell adnia egy vagy több nyelvi kódot is a `Accept-Language` http-fejléc használatával. A támogatott nyelvek ország/régió szerint változnak; Ezek az egyes országok számára a piacok táblában vannak megadva.



> [!NOTE]
> A következő piaci korlátozások érvényesek:
>
> - A képfelismerési jegyzetek csak angol nyelven érhetők el.
> - A recept, a vásárlás és a lapok – beleértve az bepillantást is – csak az Egyesült államokbeli piacon érhetők el.


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