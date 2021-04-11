---
title: Hibakeresési hibák egyéni parancsok alkalmazásának futtatásakor
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan lehet hibakeresési hibákat felvenni egy egyéni parancs alkalmazásban.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b41bcda7381e5811ef1e1f91c102d8c85f50b3b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103196916"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Hibakeresési hibák egyéni parancsok alkalmazásának futtatásakor

Ez a cikk azt ismerteti, hogyan lehet hibakeresést végezni, amikor hibákat lát el az egyéni parancsok alkalmazásának futtatásakor. 

## <a name="connection-failed"></a>Sikertelen volt a hozzáférés

Ha a Custom commands alkalmazást [(a SPEECH SDK-val)](./how-to-custom-commands-setup-speech-sdk.md) vagy a [Windows Voice Assistant-ügyfelet](./how-to-custom-commands-developer-flow-test.md)futtatja, akkor az alább felsorolt kapcsolati hibák léphetnek fel:

| Hibakód | Részletek |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: a WebSocket frissítése hitelesítési hiba miatt meghiúsult |
| [1002](#error-1002) | A kiszolgáló a (z) 404 állapotkódot adta vissza, amikor a rendszer a következő állapotkódot várta: "101". |

### <a name="error-401"></a>401-es hiba
- Az ügyfélalkalmazás által megadott régió nem egyezik az egyéni parancs alkalmazásának régiójával.

- A beszédfelismerési erőforrás kulcsa érvénytelen
    
    Győződjön meg arról, hogy a beszédfelismerési erőforrás kulcsa helyes.

### <a name="error-1002"></a>1002-es hiba 
- Az egyéni alkalmazásproxy nincs közzétéve
    
    Tegye közzé alkalmazását a portálon.

- Az egyéni parancs applicationId érvénytelen.

    Győződjön meg arról, hogy az egyéni Command Application ID helyes.
 Egyéni parancs alkalmazása a beszédfelismerési erőforráson kívül

    Győződjön meg arról, hogy az egyéni Command alkalmazás a beszédfelismerési erőforrás alatt jön létre.

A kapcsolati problémák elhárításával kapcsolatos további információkért a [Windows hangsegéd-ügyfél hibaelhárítása](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting) című témakörben olvashat bővebben.


## <a name="dialog-is-canceled"></a>Párbeszédablak megszakítva

Az egyéni parancsok alkalmazás futtatásakor a párbeszédpanel megszakításra kerül, amikor valamilyen hiba történik.

- Ha teszteli az alkalmazást a portálon, akkor közvetlenül megjeleníti a lemondási leírást, és kipróbálhatja a hiba earcon. 

- Ha az alkalmazást a [Windows Voice Assistant-ügyféllel](./how-to-custom-commands-developer-flow-test.md)futtatja, akkor a rendszer hibát earcon. A következő esemény található **: CancelledDialog** a **tevékenység naplófájljai** alatt.

- Ha az ügyfélalkalmazás példáját [(a SPEECH SDK-val)](./how-to-custom-commands-setup-speech-sdk.md)követi, a hiba earcon. A következő esemény található **: CancelledDialog** az **állapot** alatt.

- Ha saját ügyfélalkalmazás épül fel, bármikor megtervezheti a kívánt logikát a CancelledDialog-események kezeléséhez.

A CancelledDialog esemény az alább felsorolt lemondási kódot és leírást tartalmazza:

| Lemondási kód | Lemondás leírása |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | A Turns megengedett maximális száma után nem történt előrehaladás |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | A felismerő használati kvóta túllépve |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Nem sikerült csatlakozni a felismerőhöz |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | Ez az alkalmazás nem érhető el a jelenlegi előfizetéssel |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | A bemenet meghaladja a felismerő maximális támogatott hosszát |
| [RecognizerNotFound](#recognizer-not-found) | A felismerő nem található |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Érvénytelen lekérdezés a felismerő számára |
| [RecognizerError](#recognizer-return-an-error) | A felismerő hibát ad vissza. |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>A Turns megengedett maximális száma után nem történt előrehaladás
Ha bizonyos számú fordulat után nem sikerül frissíteni a szükséges tárolóhelyet, a rendszer megszakítja a párbeszédpanelt. A Build-in Max szám 3.

### <a name="recognizer-usage-quota-exceeded"></a>A felismerő használati kvóta túllépve
Language Understanding (LUIS) korlátozza az erőforrás-használatot. Általában a "felismerő használati kvóta túllépte a hibát" oka lehet: 
- A LUIS authoring meghaladja a korlátot

    Előrejelzési erőforrás hozzáadása az egyéni parancsok alkalmazáshoz: 
    1. Ugrás a **Beállítások**, Luis erőforrás
    1. Válasszon előrejelzési erőforrást az **előrejelzési erőforrásból**, vagy kattintson az **új erőforrás létrehozása** elemre. 

- A LUIS előrejelzési erőforrás meghaladja a korlátot

    Ha F0 előrejelzési erőforrást tartalmaz, legfeljebb 10 ezer/hónap, 5 lekérdezés/másodperc értékre van korlátozva.

A LUIS erőforrás-korlátokkal kapcsolatos további részletekért lásd: [Language Understanding erőforrás-használat és-korlát](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>Nem sikerült csatlakozni a felismerőhöz
Ez általában azt jelenti, hogy az átmeneti kapcsolódási hiba Language Understanding (LUIS) felismerő. Próbálja ki újra, és a problémát fel kell oldani.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Ez az alkalmazás nem érhető el a jelenlegi előfizetéssel
Az előfizetése nem jogosult a LUIS alkalmazás elérésére. 

### <a name="input-exceeds-the-maximum-supported-length"></a>A bemenet meghaladja a maximálisan támogatott hosszt
A bemenet túllépte a 500 karaktert. A bemeneti adatokhoz legfeljebb 500 karaktert engedélyezünk.

### <a name="invalid-query-for-the-recognizer"></a>Érvénytelen lekérdezés a felismerő számára
A bemenet túllépte a 500 karaktert. A bemeneti adatokhoz legfeljebb 500 karaktert engedélyezünk.

### <a name="recognizer-return-an-error"></a>A felismerő hibát adott vissza
A LUIS-felismerő hibát adott vissza a bemenet felismerésére tett kísérlet során.

### <a name="recognizer-not-found"></a>A felismerő nem található
Nem található az egyéni parancsok párbeszédpanel-modellben megadott felismerő típusa. Jelenleg csak a [Language Understanding (Luis) felismerőt](https://www.luis.ai/)támogatjuk.

## <a name="other-common-errors"></a>Egyéb gyakori hibák
### <a name="unexpected-response"></a>Váratlan válasz
A váratlan válaszok több dolgot is okozhatnak. Néhány ellenőrzés a következővel kezdődik:
- Igen/nem szerepelnek például a mondatok

    Mivel jelenleg nem támogatottak az igen/nem a szándékok, kivéve, ha a használata megerősítő funkciót használ. Nem észlelhetők például a mondatokban definiált összes igen/nem szándék.

- Hasonló szándékok és példák a parancsok közötti mondatokra

    A LUIS-felismerés pontossága akkor is hatással lehet, ha két parancs hasonló szándékokkal és példákkal van megosztva. Megpróbálkozhat a parancsok működésével és a mondatok a lehető legeltérővé tételével.

    Az elismerési pontosság javításának ajánlott gyakorlata a [Luis ajánlott eljárása](../luis/luis-concept-best-practices.md).

- Párbeszédablak megszakítva
    
    A fenti szakaszban található törlés okait itt találja.

### <a name="error-while-rendering-the-template"></a>Hiba történt a sablon megjelenítése közben
A beszédfelismerési válaszban nem definiált paramétert használ a rendszer. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Az objektum hivatkozása nem az objektum egy példányára van beállítva
Üres paraméter szerepel a **Küldés tevékenység küldése az ügyfélnek** művelethez megadott JSON-adattartalomban.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Minták megtekintése a GitHubon](https://aka.ms/speech/cc-samples)
