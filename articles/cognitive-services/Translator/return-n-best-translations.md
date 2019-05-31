---
title: Vissza a legjobb N fordítások – Translator Text API
titlesuffix: Azure Cognitive Services
description: Legjobb N fordítás a Microsoft Translator Text API használatával adja vissza.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-pawal
ms.openlocfilehash: 27138fc82515983bb07df845e1204fe04dff915a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389677"
---
# <a name="how-to-return-n-best-translations"></a>Legjobb N fordítások visszaállítása

> [!NOTE]
> Tato metoda se zamítá. Nem érhető a 3.0-s verzió, a Translator Text API.

A Microsoft Translator API GetTranslations() és GetTranslationsArray() módszerei közé tartozik egy választható logikai jelzőt "IncludeMultipleMTAlternatives".
A metódus legfeljebb maxTranslations alternatívák ad vissza, ahol a különbözeti kerüljön a translator motor legjobb N listájából.

Az aláírás az:

**Syntax**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Szükséges** az engedélyezési fejléc használata esetén a appid mezőt üresen hagyja más esetben adja meg egy karakterlánc, amely tartalmazza a "Tulajdonos" + "" + a hozzáférési jogkivonatot.|
| text | **Szükséges** egy karakterlánc, amely a fordítandó szöveg. A szöveg mérete nem haladhatja meg a 10000 karakternél.|
| from | **Szükséges** egy karakterlánc, amely a fordítandó szöveg nyelvkódja. |
| erre: | **Szükséges** karakterláncként lefordítani a szöveget a nyelvi kódot. |
| maxTranslations | **Szükséges** egy int való visszatéréshez fordítások maximális számát jelöli. |
| Beállítások | **Nem kötelező** TranslateOptions egy objektum, amely tartalmazza az alább felsorolt értékeket. Minden nem kötelező, és alapértelmezés szerint a leggyakrabban használt beállításait.

* Kategória: Az egyetlen támogatott, és az alapértelmezett beállítás "általános".
* ContentType: Az egyetlen támogatott, és az alapértelmezett beállítás "text/plain".
* Állapot: Felhasználói állapot összevetését kérések és válaszok érdekében. Ugyanaz a tartalma visszatér a válaszban.
* IncludeMultipleMTAlternatives: Ez a jelző azt határozza meg, hogy az MT motortól alternatívák egynél több visszaadása. Alapértelmezés a False (hamis), és csak 1 alternatív tartalmazza.

## <a name="ratings"></a>Minősítések
A minősítések alkalmazza az alábbiak szerint: A legjobb automatikus fordítás, 5 besorolású.
Az automatikusan generált (N-a legjobb) fordítási lehetőségeket 0 minősítést, és egy 100 egyezés mértékét rendelkeznek.

## <a name="number-of-alternatives"></a>Alternatív megoldások száma
Visszaadott alternatív megoldások száma legfeljebb maxTranslations, de kevesebb is lehet.

## <a name="language-pairs"></a>Nyelvi párok
Ez a funkció a fordítások egyszerűsített és hagyományos kínai, mindkét irányban között nem érhető el. Minden más támogatott Microsoft Translator nyelvi párok érhető el.
