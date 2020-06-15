---
title: Az összes előre összeépített entitás – LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) részét képező, előre elkészített entitások listáját tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686630"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entitások/kulturális elemek a LUIS-modellben

Language Understanding (LUIS) előre összeépített entitásokat biztosít. Ha egy előre összeépített entitás szerepel az alkalmazásban, a LUIS tartalmazza a megfelelő entitás-előrejelzést a végpont válaszában. Az összes példa hosszúságú kimondott szöveg is címkével kell ellátni az entitással. Az előre elkészített entitások viselkedése **nem** módosítható. Ha másként nincs jelezve, az előre elkészített entitások az összes LUIS alkalmazás-területi beállításban (kulturális környezetekben) érhetők el. A következő táblázat az egyes kulturális környezetekben támogatott előre összeépített entitásokat tartalmazza.

|Kulturális környezet|Szubkultúrák|Megjegyzések|
|--|--|--|
|Kínai|[zh-CN](#chinese-entity-support)||
|Holland|[nl-NL](#dutch-entity-support)||
|Angol|[en-US (amerikai)](#english-american-entity-support)||
|Francia|[fr-Ca (Kanada)](#french-canadian-entity-support), [fr-fr (Franciaország)](#french-france-entity-support), ||
|Német|[de-DE](#german-entity-support)||
|Olasz|[it-IT](#italian-entity-support)||
|Japán|[ja-JP](#japanese-entity-support)||
|Koreai|[ko-KR](#korean-entity-support)||
|Portugál|[PT-BR (Brazília)](#portuguese-brazil-entity-support)||
|Spanyol|[es-es (Spanyolország)](#spanish-spain-entity-support), [es-MX (Mexikó)](#spanish-mexico-entity-support)||
|Török|[török](#turkish-entity-support)|Nem támogatott előre elkészített entitások a török nyelven|

## <a name="prediction-endpoint-runtime"></a>Előrejelzési végpont futtatókörnyezete

Az előre elkészített entitások egy adott nyelven való rendelkezésre állását az előrejelzési végpont futásidejű verziója határozza meg.

## <a name="chinese-entity-support"></a>Kínai entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```zh-CN``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Holland entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```nl-NL``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Angol (amerikai) entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```en-US``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Francia (franciaországi) entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```fr-FR``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |   -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Francia (kanadai) entitás támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```fr-CA``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Német entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```de-DE``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Olasz entitások támogatása

Olasz előre elkészített kor, pénznem, dimenzió, szám, százalék _felbontás_ változása v2 és v3 előzetes verzióról.

A következő entitások támogatottak:

|Előre elkészített entitás|```it-IT``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Japán entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```ja-JP``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2,-   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2,-   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2,-   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2,-   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2,-   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2,-   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2,-   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Koreai entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```ko-KR``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    -   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Portugál (brazíliai) entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```pt-BR``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Spanyol (spanyolországi) entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```es-ES``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    V2, V3   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Spanyol (mexikói) entitások támogatása

A következő entitások támogatottak:

|Előre elkészített entitás|```es-MX``` |
------|:------:|
[Életkor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem (pénz)](luis-reference-prebuilt-currency.md):<br>dollár<br>tört egység (pl.: Penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>daterange<br>time<br>timerange   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>tömeg<br>információ (pl. bit/bájt)<br>Hossz (pl. Meter)<br>sebesség (pl.: mérföld/óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Sorszámok](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>DeLisle<br>Celsius   |    -   |
[URL-cím](luis-reference-prebuilt-url.md)   |    V2, V3   |

Tekintse meg az [elavult előre összeépített entitások](luis-reference-prebuilt-deprecated.md) megjegyzéseit

A KeyPhrase nem érhető el a portugál (brazíliai) alkultúrában ```pt-BR``` .

## <a name="turkish-entity-support"></a>Török entitások támogatása

**A török rendszer nem támogatja az előre elkészített entitásokat.**

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Az előre elkészített entitások kultúrájának elősegítése
Az előre összeállított entitások a felismerők – szöveg nyílt forráskódú projektben vannak kifejlesztve. [Hozzájárul](https://github.com/Microsoft/Recognizers-Text) a projekthez. Ez a projekt kulturális pénznemre vonatkozó példákat tartalmaz.

A GeographyV2 és a PersonName nem szerepelnek a felismerők – Text projektben. Az előre elkészített entitásokkal kapcsolatos problémák esetén nyisson meg egy [támogatási kérést](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>További lépések

Tudnivalók a [számokról](luis-reference-prebuilt-number.md), a [datetimeV2](luis-reference-prebuilt-datetimev2.md)és a [Pénznem](luis-reference-prebuilt-currency.md) entitásokról.
