---
title: Tekintse át a Language Understanding (LUIS) aktív tanulás használata végpont kimondott szöveg
titleSuffix: Azure Cognitive Services
description: Aktív tanulás az előrejelzési pontosság és a legegyszerűbb megvalósításához három stratégia egyik. Aktív tanulás, a felülvizsgálat végpont utterances megfelelő leképezés és entitás. A LUIS úgy dönt, hogy pontosan végpontja kimondott szöveg.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/07/2018
ms.author: diberry
ms.openlocfilehash: e65e44ec4deb07e85b9514a1685e4dbb692fd512
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48857408"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Aktív tanulás engedélyezése endpoint utterances áttekintésével
Aktív tanulás az előrejelzési pontosság és a legegyszerűbb megvalósításához három stratégia egyik. Aktív tanulás, a felülvizsgálat végpont utterances megfelelő leképezés és entitás. A LUIS úgy dönt, hogy pontosan végpontja kimondott szöveg.

## <a name="what-is-active-learning"></a>Mi az aktív tanulás
Aktív tanulás két lépésből áll. Először LUIS választ kap az alkalmazás végponti érvényesítési igénylő kimondott szöveg. A második lépést az alkalmazás tulajdonos vagy közreműködő, érvényesíteni a kiválasztott megcímkézzen [tekintse át](luis-how-to-review-endoint-utt.md), beleértve a megfelelő cél és minden olyan entitások a célt. A kimondott szöveg áttekintése, után betanítása, és tegye közzé újra az alkalmazást. 

## <a name="which-utterances-are-on-the-review-list"></a>A felülvizsgálati listán vannak melyik kimondott szöveg
A LUIS ad hozzá a kimondott szöveg felülvizsgálati szándékot kiváltó felső alacsony pontszámmal rendelkezik, vagy a felső két szándék pontszámok túl kicsi. 

## <a name="single-pool-for-utterances-per-app"></a>Egyetlen készletet az egyes alkalmazások kimondott szöveg
A **tekintse át a végpont utterances** lista alapján a verzió nem változik. A kimondott szövegek egyetlen készletét kell áttekintenie, függetlenül attól, hogy a kimondott szöveg melyik verzióját szerkeszti vagy az alkalmazás melyik verziója lett közzétéve a végponton. 

## <a name="where-are-the-utterances-from"></a>Hol találhatók a megcímkézzen
Végpont utterances végfelhasználói lekérdezések, az alkalmazás HTTP-végponton kell venni. Ha az alkalmazás nincs közzétéve, vagy nem kapott találatok még, nem rendelkezik megszólalásokat áttekintéséhez. Nincs végpont találatok érkezik egy adott szándékot vagy entitáshoz, ha nem rendelkezik a kimondott szöveg tekintse át azokat. 

## <a name="schedule-review-periodically"></a>Rendszeres időközönként ütemezés áttekintése
Tekintse át a javasolt utterances nem kell végrehajtani minden nap, de a LUIS rendszeres karbantartása részének kell lennie. 

## <a name="delete-review-items-programmatically"></a>Programozott módon felülvizsgálati elemek törlése
Használja a **[törlése a jelöletlen utterances](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API-t. Készítsen biztonsági másolatot a törlés előtt ezeket utterances  **[naplófájlok exportálása](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [tekintse át](luis-how-to-review-endoint-utt.md) végpont kimondott szöveg
