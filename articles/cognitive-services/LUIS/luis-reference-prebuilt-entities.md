---
title: Az összes előre összeépített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) részét képező, előre elkészített entitások listáját tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: cb3c74a2176ee7fcac53afb5185e8c62e66f4dfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798799"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entitások/kulturális elemek a LUIS-modellben

Language Understanding (LUIS) előre összeépített entitásokat biztosít.

## <a name="entity-resolution"></a>Entitás feloldása
Ha egy előre összeépített entitás szerepel az alkalmazásban, a LUIS a végponti válaszban tartalmazza a megfelelő entitás-feloldást. Az összes példa hosszúságú kimondott szöveg is címkével kell ellátni az entitással.

Az előre elkészített entitások működése nem módosítható, de javíthatja a felbontást úgy, [hogy hozzáadja az előre elkészített entitást funkcióként egy gépi tanulási entitáshoz vagy alentitáshoz](luis-concept-entity-types.md#effective-prebuilt-entities).

## <a name="availability"></a>Rendelkezésre állás
Ha másként nincs jelezve, az előre elkészített entitások az összes LUIS alkalmazás-területi beállításban (kulturális környezetekben) érhetők el. A következő táblázat az egyes kulturális környezetekben támogatott előre összeépített entitásokat tartalmazza.

|Kultúra|Szubkultúrák|Jegyzetek|
|--|--|--|
|Kínai|[zh-CN](#chinese-entity-support)||
|Holland|[nl-NL](#dutch-entity-support)||
|Angol|[en-US (amerikai)](#english-american-entity-support)||
|Francia|[fr-Ca (Kanada)](#french-canadian-entity-support), [fr-fr (Franciaország)](#french-france-entity-support), ||
|Német|[de-DE](#german-entity-support)||
|Olasz|[informatikai](#italian-entity-support)||
|Japán|[ja-JP](#japanese-entity-support)||
|Koreai|[ko-KR](#korean-entity-support)||
|Portugál|[PT-BR (Brazília)](#portuguese-brazil-entity-support)||
|Spanyol|[es-es (Spanyolország)](#spanish-spain-entity-support), [es-MX (Mexikó)](#spanish-mexico-entity-support)||
|Török|[török](#turkish-entity-support)||

## <a name="prediction-endpoint-runtime"></a>Előrejelzési végpont futtatókörnyezete

Az előre elkészített entitások egy adott nyelven való rendelkezésre állását az előrejelzési végpont futásidejű verziója határozza meg.

## <a name="chinese-entity-support"></a>Kínai entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | zh-CN |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Holland entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | nl-NL |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Angol (amerikai) entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | en-US |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Francia (franciaországi) entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | fr-FR |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Francia (kanadai) entitás támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | fr – CA |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Német entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás | de-DE |
| -------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Olasz entitások támogatása

Olasz előre elkészített kor, pénznem, dimenzió, szám, százalék _felbontás_ változása v2 és v3 előzetes verzióról.

A következő entitások támogatottak:

| Előre elkészített entitás | it-IT |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Japán entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás | ja-JP |
| -------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2,-   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2,-   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2,-   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2,-   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2,-   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2,-   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2,-   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Koreai entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | ko-KR |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    -   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Portugál (brazíliai) entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | pt-BR |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

A KeyPhrase nem érhető el a portugál (brazíliai) alkultúrában ```pt-BR``` .

## <a name="spanish-spain-entity-support"></a>Spanyol (spanyolországi) entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | es-ES |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Spanyol (mexikói) entitások támogatása

A következő entitások támogatottak:

| Előre elkészített entitás | es-MX |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

Tekintse meg az [elavult előre összeépített entitások](luis-reference-prebuilt-deprecated.md) megjegyzéseit

## <a name="turkish-entity-support"></a>Török entitások támogatása

| Előre elkészített entitás | tr-tr |
| --------------- | :---: |
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Szám](luis-reference-prebuilt-number.md)   |    -   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    -   |

<!---
See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)
KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Az előre elkészített entitások kultúrájának elősegítése
Az előre összeállított entitások a Recognizers-Text nyílt forráskódú projektben vannak kifejlesztve. [Hozzájárul](https://github.com/Microsoft/Recognizers-Text) a projekthez. Ez a projekt kulturális pénznemre vonatkozó példákat tartalmaz.

A GeographyV2 és a PersonName nem szerepelnek a Recognizers-Text projektben. Az előre elkészített entitásokkal kapcsolatos problémák esetén nyisson meg egy [támogatási kérést](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [számokról](luis-reference-prebuilt-number.md), a [datetimeV2](luis-reference-prebuilt-datetimev2.md)és a [Pénznem](luis-reference-prebuilt-currency.md) entitásokról.
