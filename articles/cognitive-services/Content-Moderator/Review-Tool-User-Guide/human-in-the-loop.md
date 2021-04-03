---
title: További tudnivalók az eszközök áttekintése – Content Moderator
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Content Moderator felülvizsgálati eszközt, amely egy olyan webhely, amely összehangolja a kombinált AI-és emberi felülvizsgálati moderálási erőfeszítéseket.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93146619"
---
# <a name="content-moderator-review-tool"></a>Content Moderator felülvizsgálati eszköz

Az Azure Content Moderator olyan szolgáltatásokat biztosít, amelyekkel kombinálhatja a gépi tanulási tartalmak moderálását emberi felülvizsgálatokkal. A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) webhelye egy felhasználóbarát kezelőfelület, amely részletes hozzáférést biztosít ezekhez a szolgáltatásokhoz.

## <a name="what-it-does"></a>Művelet

A [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com)a géppel támogatott moderálási API-kkal együtt használva a következő feladatokat hajthatja végre a tartalom-moderálási folyamat során:

- Egyetlen eszközkészletet használhat a tartalom moderálására több formátumban (szöveg, rendszerkép és videó).
- Automatizálhatja az emberi [felülvizsgálatok](../review-api.md#reviews) létrehozását, ha a moderálási API eredményei beérkeznek.
- Több felülvizsgálati csapatnak is hozzárendelhet vagy kiterjesztheti a tartalmi értékeléseket, amelyek a tartalom kategóriája vagy a felhasználói élmény szintjén vannak rendezve.
- Az alapértelmezett vagy az egyéni logikai szűrők ([munkafolyamatok](../review-api.md#workflows)) használatával rendezheti és követheti a tartalmakat anélkül, hogy kódot kellene írnia.
- Az [Összekötők](./configure.md#connectors) segítségével a Content moderator API-k mellett a Microsoft PhotoDNA, a Text Analytics és a Face Services használatával is feldolgozhatja a tartalmakat.
- A tartalom moderálási folyamataihoz tartozó fő teljesítmény-mérőszámok beolvasása.

## <a name="review-tool-dashboard"></a>Felülvizsgálati eszköz irányítópultja

Az **irányítópult** lapon megtekintheti az eszközön végzett tartalmi felülvizsgálatok fő mérőszámait. Tekintse meg a képek, szövegek és videók összesített, teljes és függőben lévő felülvizsgálatait. 

A **függőben lévő értékelések** tábla megjeleníti a függőben lévő vagy befejezett felülvizsgálatokkal rendelkező felhasználók és alcsoportok, valamint a fennmaradó SLA-idő részletezését. A táblázatban szereplő elemek kiválasztásával megtekintheti az értékeléseket. A tábla feletti keresőmező lehetővé teszi, hogy a csoport neve szerint szűrje az eredményeket, és a **szűrő** ikon lehetővé teszi más mérőszámok alapján történő szűrést.

A **befejezett értékelések** lapon való váltáskor a felhasználók és alcsoportok által feldolgozott vagy befejezett elemek teljes száma látható. A függőben lévő felülvizsgálatokkal megegyező módon szűrheti ezeket az adattípusokat.

Az irányítópult jobb felső sarkában lévő szövegre kattintva megjelennek a napi személyes metrikák, amelyek az egyes tartalomtípusok esetében befejezett értékelések számát jelentik.

> [!div class="mx-imgBorder"]
> ![A felülvizsgálati eszköz irányítópultja egy böngészőben](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Eszköz hitelesítő adatainak áttekintése

Ha regisztrál a [felülvizsgálati eszközre](https://contentmoderator.cognitive.microsoft.com), a rendszer kérni fogja, hogy válasszon ki egy Azure-régiót a fiókhoz. Ennek az az oka, hogy a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) létrehoz egy ingyenes próbaverziót az Azure Content moderator-szolgáltatásokhoz. Erre a kulcsra szüksége lesz a REST-hívásokból vagy az ügyféloldali SDK-ból származó szolgáltatások eléréséhez. A kulcs-és API-végpont URL-címét a **rendszergazdai**  >  **hitelesítő adatok** lehetőség kiválasztásával tekintheti meg.

> [!div class="mx-imgBorder"]
> ![Hitelesítő adatok Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Következő lépések

Lásd: [a felülvizsgálati eszköz konfigurálása](./configure.md) , amelyből megtudhatja, hogyan érheti el a felülvizsgálati eszköz erőforrásait és módosíthatja a beállításokat.