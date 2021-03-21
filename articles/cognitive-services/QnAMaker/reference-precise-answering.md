---
title: Pontos hívásfogadás a válasz-span észlelés használatával – QnA Maker
description: A QnA Maker felügyelt szolgáltatásban elérhető pontos hívásfogadás funkció megismerése.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 9c7b265118e395dde2b4b5f3959ba397d75ddac5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103232188"
---
# <a name="precise-answering"></a>Pontos válaszadás

A QnA Maker felügyelt (előzetes verzió) szolgáltatásban bevezetett pontos hívásfogadás funkció lehetővé teszi, hogy pontosan rövid választ kapjon a Tudásbázisban a felhasználói lekérdezésekhez elérhető legjobb válasz-átlépésből. Ez a funkció egy olyan mély tanulási modellt használ, amely a Futtatás során megérti a felhasználói lekérdezés célját, és a válasz áthaladásának pontos rövid válaszát észleli, ha egy rövid válasz van jelen a válasz-átjáróban. 

Ez a szolgáltatás alapértelmezés szerint be van kapcsolva a teszt ablaktáblán, így tesztelheti a forgatókönyvhöz kapcsolódó funkciókat. Ez a funkció rendkívül hasznos a fejlesztők és a végfelhasználók számára egyaránt. Mostantól a tartalmi fejlesztőknek nem kell manuálisan megadniuk a konkrét QnA párokat a tudásbázisban található minden egyes tényhez, és a végfelhasználónak nem kell megvizsgálnia a szolgáltatás által visszaadott teljes válasz-áthaladást, hogy megkeresse a felhasználó lekérdezésére adott tényleges tényt. [A pontos válaszokat a válaszok létrehozása API-n keresztül](How-To/metadata-generateanswer-usage.md#get-precise-answers-with-generateanswer-api)olvashatja be.

## <a name="precise-answering-on-qna-maker-portal"></a>Pontos válaszadás QnA Maker portálon

A QnA Maker-portálon a test-panel megnyitásakor megjelenik egy lehetőség a **rövid válasz megjelenítéséhez** a tetején. Ez a beállítás alapértelmezés szerint ki lesz választva. Amikor lekérdezést ad meg a teszt ablaktáblán, egy rövid válasz jelenik meg a válasz áthaladásával együtt, ha a válaszban van egy rövid válasz.
 
![Felügyelt engedélyezett teszt panel](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Ha csak a teszt ablaktáblában szeretné megjeleníteni a válasz áthaladását, törölje a **rövid válasz megjelenítése** lehetőséget. 

A szolgáltatás a pontos válasz megbízhatósági pontszámát is visszaadja egy **Válasz-span pontszámként** , amelyet a teszt ablaktáblán a lekérdezés alatt található **vizsgálat** lehetőségre kattintva adhat meg.

![Felügyelt válaszok span pontszáma](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>QnA Maker robot közzététele

Amikor közzétesz egy robotot, az alkalmazásban alapértelmezés szerint a pontos válasz engedélyezett élmény jelenik meg, ahol rövid választ fog látni a válasz áthaladásával együtt. A válasz létrehozásához tekintse meg az API-referenciát [, amelyből](/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) megtudhatja, hogyan használhatja a pontos választ (a AnswerSpan néven) a válaszban. A felhasználónak rugalmasan kell kiválasztania más élményeket a sablonnak a bot app Service-ben való frissítésével. 

## <a name="language-support"></a>Nyelvi támogatás

Jelenleg a pontos válasz funkció csak angol nyelven támogatott.
