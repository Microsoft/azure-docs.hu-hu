---
title: 'Rövid útmutató: Form Recognizer kódtár vagy REST API'
titleSuffix: Azure Cognitive Services
description: A Form Recognizer ügyféloldali kódtár vagy REST API hozhat létre olyan űrlapfeldolgozó alkalmazást, amely kulcs/érték párokat és táblaadatokat von ki az egyéni dokumentumokból.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: űrlapfeldolgozás, automatizált adatfeldolgozás
ms.openlocfilehash: b4631892f1c35c665c4468a6e0b3ad481a19e8df
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516399"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Rövid útmutató: A Form Recognizer ügyféloldali kódtár vagy REST API

A Form Recognizer a választott fejlesztési nyelvvel. Az Azure Form Recognizer egy kognitív szolgáltatás, amellyel automatizált adatfeldolgozási szoftvereket építhet gépi tanulási technológiával. Azonosíthatja és kinyerhet szövegeket, kulcs/érték párokat, kijelölési jeleket, táblaadatokat és egyéb adatokat az űrlapdokumentumból. A szolgáltatás strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban található &mdash; kapcsolatokat. Az alkalmazásokat Form Recognizer az REST API SDK-n keresztül használhatja. Kövesse az alábbi lépéseket az SDK-csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz tartozó példakódot.

A Form Recognizer a következőre:

* [Elrendezés elemzése](#analyze-layout)
* [Nyugták elemzése](#analyze-receipts)
* [Névjegykártyák elemzése](#analyze-business-cards)
* [Számlák elemzése](#analyze-invoices)
* [Identitásdokumentumok elemzése](#analyze-identity-documents)
* [Egyéni modell betanítása](#train-a-custom-model)
* [Űrlapok elemzése egyéni modellel](#analyze-forms-with-a-custom-model)
* [Egyéni modellek kezelése](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
