---
title: Alkalmazás közzététele – LUIS
titleSuffix: Azure Cognitive Services
description: Ha befejezte a elkészítését és tesztelését a LUIS alkalmazás aktív, tegye elérhetővé számára az ügyfélalkalmazás közzétesszük azt a végpontot.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: diberry
ms.openlocfilehash: fdbdb2cd13d131019c4230aa369af31240d25f0d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563650"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Aktív, betanított alkalmazás közzététele átmeneti vagy éles végponton

Ha befejezte a elkészítését és tesztelését a LUIS alkalmazás aktív, tegye elérhetővé számára az ügyfélalkalmazás közzétesszük azt a végpontot. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Közzététel

A végponthoz való közzétételéhez válassza **közzététel** a képernyő felső részén kattintson a panel jobb. 

![Felső, jobb oldali navigációs sávban](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

Válassza ki a megfelelő tárhely, amikor a felugró ablakban jelenik meg: átmeneti és éles környezetben. Két közzétételi tárolóhely használatával Ez lehetővé teszi, hogy két különböző végpontok közzétett végpontokkal rendelkező két különböző verziója vagy verziójával rendelkezik. 

Az alkalmazás közzé van téve a LUIS erőforrások a LUIS portálon hozzá társított összes régióba. Például a létrehozott alkalmazás [www.luis.ai](https://www.luis.ai), ha a LUIS erőforrás létrehozása **westus** , és adja hozzá az alkalmazást egy erőforrást, az alkalmazás közzé van téve az adott régióban. A LUIS régiók kapcsolatos további információkért lásd: [régiók](luis-reference-regions.md).
 
![Közzétételi előugró ablak](./media/luis-how-to-publish-app/publish-pop-up.png)

Ha az alkalmazás sikeresen közzé lett téve, egy zöld, sikeres értesítés jelenik meg, a böngésző tetején. A zöld értesítési sáv a végpontok egy hivatkozást is tartalmaz. 

![Előugró ablak közzététele a végpontra mutató hivatkozással](./media/luis-how-to-publish-app/publish-success.png)

Ha a végpont URL-címe van szüksége, válassza ki a hivatkozást. Is érheti el a végponti URL-címek kiválasztásával **kezelés** a felső menüben, majd válassza ki **kulcsokat és a végpontok** a bal oldali menüben. 

## <a name="configuring-publish-settings"></a>Nastavení publikování konfigurálása

Konfigurálása közzétételi beállítások kiválasztásával **kezelés** tetején, a jobb gombbal a navigáció, majd kiválasztja a **közzétételi beállítások**. 

![Közzétételi beállítások](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>Miután engedélyezte a hangulatelemzés közzététele

<a name="enable-sentiment-analysis"></a>

Hangulatelemzés lehetővé teszi, hogy a LUIS integrálása [Szövegelemzés](https://azure.microsoft.com/services/cognitive-services/text-analytics/) a vélemény és a kulcs kifejezés elemzést. 

Nem kell adnia a Text Analytics kulcsot, és nem jár költségekkel számlázási ezt a szolgáltatást az Azure-fiókjába. Ha bejelöli ezt a beállítást, az állandó. 

Véleményadatok egy 1 és 0 a pozitív jelző közötti pontszámot (közelebb 1) vagy negatív (0 közelebb) az adatok a róluk szóló véleményeket. `positive`A, `neutral`a, a és `negative` a a által támogatott kulturális környezet. Jelenleg csak az angol támogatja a hangulati címkéket. 

A JSON-végpont választ véleményelemzéssel kapcsolatos további információkért lásd: [hangulatelemzés](luis-concept-data-extraction.md#sentiment-analysis)

## <a name="next-steps"></a>További lépések

* Lásd: [kulcsok kezelése](./luis-how-to-azure-subscription.md) kulcsok hozzáadása az Azure-előfizetés LUIS kulcsot, és hogyan állíthatja be, a Bing Spell Check kulcs és a keresési eredmények között szerepeljen az összes szándék fog vonatkozni.
* Lásd: [Train és tesztelje alkalmazását](luis-interactive-test.md) vonatkozó utasításokat a közzétett alkalmazás tesztelése a test-konzolon.

