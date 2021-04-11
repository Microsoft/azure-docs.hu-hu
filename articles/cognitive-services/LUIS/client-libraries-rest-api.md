---
title: 'Gyors útmutató: Language Understanding (LUIS) SDK-ügyfél kódtárai és REST API'
description: LUIS-alkalmazás létrehozása és lekérdezése a LUIS SDK-ügyfél kódtárai és REST API.
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, mesterséges intelligencia, AI, természetes nyelvi feldolgozás, NLP, LUIS, Azure Luis, természetes nyelvi megértés, AI Csevegőrobot, Csevegőrobot Maker, természetes nyelv megismerése
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278915"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Gyors útmutató: Language Understanding (LUIS) ügyféloldali kódtárak és REST API

Azure LUIS mesterséges intelligencia-(AI-) alkalmazás létrehozása és lekérdezése a LUIS SDK ügyféloldali kódtárakkal a C#, a Python vagy a JavaScript használatával. A cURL használatával a REST API segítségével küldhet kérelmeket.

Language Understanding (LUIS) lehetővé teszi természetes nyelvi feldolgozás (NLP) alkalmazását a felhasználó társalgási, természetes nyelvű szövegére az általános jelentés megbecslése érdekében, valamint a releváns, részletes információk kiolvasását.

* A **szerzői** ügyféloldali kódtár és REST API lehetővé teszik a Luis-alkalmazás létrehozását, szerkesztését, betanítását és közzétételét.
* Az **előrejelzési futtatókörnyezet** ügyféloldali kódtára és REST API lehetővé teszi a közzétett alkalmazás lekérdezését.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölheti az alkalmazást a Luis- [portálról](https://www.luis.ai) , és törölheti az Azure-erőforrásokat a [Azure Portalból](https://portal.azure.com/).

Ha a REST API használja, törölje a fájlt a fájlrendszerből, `ExampleUtterances.JSON` Ha elkészült a gyors útmutatóval.

## <a name="troubleshooting"></a>Hibaelhárítás

* Hitelesítés az ügyfél-függvénytárban – a hitelesítési hibák általában azt jelzik, hogy helytelen kulcs & végpontot használtak. Ez a rövid útmutató az előrejelzési futtatókörnyezet szerzői kulcsát és végpontját használja kényelmi feltételként, de csak akkor működik, ha még nem használta a havi kvótát. Ha nem tudja használni a szerzői kulcsot és a végpontot, használja az előrejelzési futtatókörnyezet kulcsát és a végpontot az előrejelzési futtatókörnyezet SDK-ügyfelének eléréséhez.
* Entitások létrehozása – ha hibaüzenet jelenik meg az oktatóanyagban használt beágyazott gépi tanulási entitás létrehozásakor, győződjön meg róla, hogy átmásolta a kódot, és nem változtatta meg a kódot egy másik entitás létrehozásához.
* Példa hosszúságú kimondott szöveg létrehozása – ha hibaüzenet jelenik meg, amely az oktatóanyagban használt címkézett példa kiírását hozza létre, ügyeljen arra, hogy a kódot másolja, és ne változtassa meg a kódot egy másik címkézett példa létrehozásához.
* Képzés – ha betanítási hibaüzenetet kap, ez általában üres alkalmazást (például hosszúságú kimondott szöveg), vagy helytelenül formázott, szándékosan vagy entitásokat tartalmazó alkalmazást jelez.
* Egyéb hibák – mivel a kód szöveg-és JSON-objektumokkal meghívja az ügyféloldali kódtárakat, ügyeljen arra, hogy ne változzon meg a kód.

Egyéb hibák – ha az előző listában nem szereplő hibaüzenetet kap, tudassa velünk az oldal alján található visszajelzések megadásával. Adja meg a telepített ügyféloldali kódtárak programozási nyelvét és verzióját.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A LUIS-hez készült iterációs alkalmazások fejlesztése](./luis-concept-app-iteration.md)