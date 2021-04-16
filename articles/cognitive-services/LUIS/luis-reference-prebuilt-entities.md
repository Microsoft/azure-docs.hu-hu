---
title: Minden előre összeállított entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a LUIS (LUIS) által tartalmazott előre összeállított entitások listáját Language Understanding tartalmazza.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/13/2021
ms.openlocfilehash: 7155a829655645e13e0485ed7d51305ec50e5b0a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502759"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entitások kultúránként a LUIS-modellben

Language Understanding (LUIS) előre összeállított entitásokat biztosít.

## <a name="entity-resolution"></a>Entitásfeloldás
Ha az alkalmazás tartalmaz egy előre összeállított entitást, a LUIS a megfelelő entitásfeloldásokat is tartalmazza a végpont válaszában. Minden kimondott példa szöveg az entitással is meg van címkézve.

Az előre összeállított entitások viselkedése nem módosítható, de javítható a megoldás, ha az előre összeállított entitást szolgáltatásként hozzáadja egy gépi tanulási entitáshoz vagy [alentitáshoz.](luis-concept-entity-types.md#prebuilt-entity)

## <a name="availability"></a>Rendelkezésre állás
Ha másként nincs jelezve, az előre összeállított entitások minden LUIS-alkalmazás területi ében (kultúrában) elérhetők. Az alábbi táblázat az egyes kulturális környezetek által támogatott előre összeállított entitásokat mutatja be.

|Kultúra|Szubkultúrák|Jegyzetek|
|--|--|--|
|Kínai|[zh-CN](#chinese-entity-support)||
|Holland|[nl-NL](#dutch-entity-support)||
|Angol|[en-US (amerikai)](#english-american-entity-support)||
|Francia|[fr-CA (Kanada),](#french-canadian-entity-support) [fr-FR (Franciaország)](#french-france-entity-support), ||
|Német|[de-DE](#german-entity-support)||
|Olasz|[it-IT](#italian-entity-support)||
|Japán|[ja-JP](#japanese-entity-support)||
|Koreai|[ko-KR](#korean-entity-support)||
|Portugál|[pt-BR (Brazília)](#portuguese-brazil-entity-support)||
|Spanyol|[es-ES (Spanyolország)](#spanish-spain-entity-support), [es-MX (Mexikó)](#spanish-mexico-entity-support)||
|Török|[török](#turkish-entity-support)||

## <a name="prediction-endpoint-runtime"></a>Előrejelzési végpont futásidejű

Egy előre összeállított entitás rendelkezésre állását egy adott nyelven az előrejelzési végpont futásidejű verziója határozza meg.

## <a name="chinese-entity-support"></a>Kínai entitások támogatása

A következő entitások támogatottak:

| Előre összeállított entitás | zh-CN |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    V2, V3   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információ (például bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Holland entitások támogatása

A következő entitások támogatottak:

| Előre összeállított entitás | nl-NL |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információ (például bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2 (Földrajzi hely) 2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Angol (amerikai) entitástámogatás

A következő entitások támogatottak:

| Előre összeállított entitás | en-US |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>törtegység (például: rendszer)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    V2, V3   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információk (például: bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Francia (Franciaország) entitástámogatás

A következő entitások támogatottak:

| Előre összeállított entitás | fr-FR |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    V2, V3   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információ (például bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2 (Földrajzi hely) 2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |   -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Francia (kanadai) entitások támogatása

A következő entitások támogatottak:

| Előre összeállított entitás | fr-CA |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    V2, V3   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információ (például bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2 (Földrajzi hely) 2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Német entitások támogatása

A következő entitások támogatottak:

|Előre összeállított entitás | de-DE |
| -------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    V2, V3   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információ (például bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2 (Földrajzi hely) 2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Olasz entitások támogatása

Az előre összeállított olasz kor, pénznem, dimenzió, szám, százalékos _feloldás_ változott a V2 és a V3 előzetes verzióról.

A következő entitások támogatottak:

| Előre összeállított entitás | it-IT |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>törtegység (például: rendszer)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információk (például: bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Japán entitások támogatása

A következő entitások támogatottak:

|Előre összeállított entitás | ja-JP |
| -------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, -   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>törtegység (például: rendszer)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információ (például bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2 (Földrajzi hely) 2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, -   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine (rangsor)<br>delisle<br>celsius (Celsius)   |    V2, -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Koreai entitások támogatása

A következő entitások támogatottak:

| Előre összeállított entitás | ko-KR |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>törtegység (például: rendszer)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információk (például: bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    -   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine (rangsor)<br>delisle<br>celsius (Celsius)   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Portugál (brazíliai) entitások támogatása

A következő entitások támogatottak:

| Előre összeállított entitás | pt-BR |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    V2, V3   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információk (például: bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine (rangsor)<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

A KeyPhrase nem érhető el a portugál (brazíliai) összes alkultúraében – ```pt-BR``` .

## <a name="spanish-spain-entity-support"></a>Spanyol (Spanyolország) entitástámogatás

A következő entitások támogatottak:

| Előre összeállított entitás | es-ES |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    V2, V3   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    V2, V3   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információk (például: bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Spanyol (Mexikó) entitástámogatás

A következő entitások támogatottak:

| Előre összeállított entitás | es-MX |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    -   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    -   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információk (például: bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName (Személynév)](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

Az elavult, előre összeállított entitásokkal kapcsolatban [lásd a megjegyzéseket](luis-reference-prebuilt-deprecated.md)

## <a name="turkish-entity-support"></a>Török entitások támogatása

| Előre összeállított entitás | tr-tr |
| --------------- | :---: |
[Kor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz):](luis-reference-prebuilt-currency.md)<br>Dollár<br>tört egység (pl: rendszer)  |    -   |
[DatetimeV2:](luis-reference-prebuilt-datetimev2.md)<br>dátum<br>daterange (dátumtartomány)<br>time<br>időrend   |    -   |
[Dimenzió:](luis-reference-prebuilt-dimension.md)<br>Kötet<br>terület<br>tömeg<br>információ (például bit/bájt)<br>length (pl: meter)<br>sebesség (például mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Szám](luis-reference-prebuilt-number.md)   |    -   |
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Hőmérséklet:](luis-reference-prebuilt-temperature.md)<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius (Celsius)   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    -   |

<!---
See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)
KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Hozzájárulás az előre összeállított entitáskultúrákhoz
Az előre összeállított entitásokat a Recognizers-Text nyílt forráskódú projektben fejlesztették ki. [Közreműködés](https://github.com/Microsoft/Recognizers-Text) a projektben. Ez a projekt példákat tartalmaz a pénznemre kulturális környezetenként.

A GeographyV2 és a PersonName nem szerepel a Recognizers-Text projektben. Az előre összeállított entitásokkal kapcsolatos problémák esetén nyisson egy [támogatási kérést.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [szám,](luis-reference-prebuilt-number.md) [a datetimeV2 és](luis-reference-prebuilt-datetimev2.md)a [pénznem](luis-reference-prebuilt-currency.md) entitásról.
