---
title: Első lépések – Translator
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan regisztrálhat az Azure Cognitive Services Translatorre, és hogyan szerezhet be előfizetési kulcsot.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 1a26b00a95b1b71993385e816d06d6686f89a5a1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599545"
---
# <a name="how-to-sign-up-for-translator"></a>Regisztráció a Translatorre

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

- Nincs fiókja? A költségvonzat nélküli kísérletezéshez létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
- Már van fiókja? [Bejelentkezés](https://ms.portal.azure.com/)

## <a name="create-a-subscription-for-translator"></a>Előfizetés létrehozása Translatorhez

Miután bejelentkezett a portálra, a következő módon hozhat létre előfizetést a fordítónak:

1. Válassza a **+ Erőforrás létrehozása** lehetőséget.
1. A **Keresés a piactéren** mezőbe írja be a **Translator** kifejezést, majd válassza ki az eredmények közül.
1. Válassza a **Létrehozás** lehetőséget az előfizetés részleteinek megadásához.
1. A **díjszabási** csomag listából válassza ki az igényeinek leginkább megfelelő árképzési szintet.
    1. Az egyes előfizetések ingyenes szintűek. Az ingyenes csomag ugyanazokkal a szolgáltatásokkal és funkciókkal rendelkezik, mint a fizetős csomagok, és nem jár le.
    1. A fiókjához csak egy ingyenes előfizetés tartozhat.
1. Válassza a **Létrehozás** lehetőséget az előfizetés létrehozásának befejezéséhez.

## <a name="authentication-key"></a>Hitelesítési kulcs

A fordítóra való feliratkozáskor egy személyre szabott hozzáférési kulcsot kap, amely egyedi az előfizetésében. Erre a kulcsra a fordító minden egyes hívásakor szükség van.

1. Kérje le a hitelesítési kulcsot úgy, hogy először kiválasztja a megfelelő előfizetést.
1. Az előfizetés részleteinek **Erőforrás-kezelés** szakaszában válassza a **kulcsok** lehetőséget.
1. Másolja a felsorolt kulcsok valamelyikét az előfizetéséhez.

## <a name="learn-test-and-get-support"></a>Tanulás, tesztelés és támogatás kérése

- [Példák a GitHubra](https://github.com/MicrosoftTranslator)
- [Microsoft Translator támogatási fórum](https://www.aka.ms/TranslatorForum)

A Microsoft Translator általában lehetővé teszi az első néhány kérelem továbbítását, mielőtt ellenőrizte az előfizetési fiók állapotát. Ha az első néhány fordítói kérelem sikeres, akkor a hívások sikertelenek lesznek, a hibaüzenet a problémát jelzi. Jelentkezzen be az API-válaszba, hogy láthassa az okát.

## <a name="pricing-options"></a>Árképzési lehetőségek

- [Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)

## <a name="customization"></a>Testreszabás

Az egyéni fordító segítségével testre szabhatja a fordításokat, és létrehozhatja a saját terminológiája és stílusa alapján létrehozott fordítási rendszert, amely az általános Microsoft Translator neurális gépi fordítási rendszerekből indul. [További információ](customization.md)

## <a name="additional-resources"></a>További források

- [Ismerkedés az Azure-ban (3 perces videó)](https://azure.microsoft.com/get-started/?b=16.24)
- [Fizetés számlával](https://azure.microsoft.com/pricing/invoicing/)
