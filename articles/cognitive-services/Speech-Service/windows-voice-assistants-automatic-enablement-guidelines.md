---
title: Adatvédelmi irányelvek a hangasszisztensekhez Windows rendszeren
titleSuffix: Azure Cognitive Services
description: A hangutasítások aktiválásának engedélyezése a hangügynök számára alapértelmezés szerint.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90987390"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Adatvédelmi irányelvek a hangasszisztensekhez Windows rendszeren

Fontos, hogy a felhasználók egyértelmű információkat kapjanak a hangadatok gyűjtéséről és használatáról, valamint arról, hogy mikor és hogyan történik a gyűjtemény ellenőrzése. Az adatvédelem alapvető aspektusait – a *nyilvánosságra hozatalt* és a betartást *– különösen* fontos a Windowsba integrált hangsegédek számára a használatuk folyamatos figyelése.

A Windowsban a hangasszisztenseket létrehozó fejlesztőknek tartalmazniuk kell a felhasználói felület azon elemeit, amelyek a Segéd figyelési képességeit tükrözik.

> [!NOTE]
> A Segéd-alkalmazás megfelelő nyilvánosságra hozatalának és beleegyezésének hiánya, beleértve az alkalmazások frissítéseinek megadását, az asszisztens nem lesz elérhető a hangalapú aktiváláshoz, amíg az adatvédelmi problémák megoldódik.

## <a name="minimum-requirements-for-feature-inclusion"></a>A funkciók felvételének minimális követelményei

A Windows-felhasználók láthatják és szabályozhatják a Segéd-alkalmazásaik rendelkezésre állását a alkalmazásban **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![A képernyőképen a Cortana availablity szabályozására szolgáló beállítások láthatók. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "Egy segéd-alkalmazás Windows Voice Activate adatvédelmi beállítási bejegyzése")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

A listában való részvételre való jogosultság kiválasztásához forduljon a Microsofthoz a winvoiceassistants@microsoft.com kezdéshez. Alapértelmezés szerint a felhasználóknak explicit módon engedélyeznie kell a hangaktiválást egy új asszisztens számára a **`Settings > Privacy > Voice Activation`** alkalmazásban `ms-settings:privacy-voiceactivation` . Az engedélyezett alkalmazások a listában jelennek meg, miután futtatták és használták az `Windows.ApplicationModel.ConversationalAgent` API-kat. A hangaktiválási beállítások módosítható, ha az alkalmazás mikrofonos beleegyezést kapott a felhasználótól.

Mivel a Windows adatvédelmi beállításai tartalmazzák a Hangaktiválás működésével kapcsolatos információkat, valamint az engedélyek vezérlésére szolgáló szabványos felhasználói felületet, a közzététel és a hozzájárulás egyaránt teljesül. A Segéd ebben az engedélyezési listán marad, amíg nem:

* A felhasználó megtévesztése vagy informálása a Segéd hang-és hangkezelési szolgáltatásával
* Nem megfelelő beavatkozás egy másik asszisztenssel
* Egyéb releváns Microsoft-szabályzatok kibontása

Ha a fentiek bármelyikét felderítik, a Microsoft eltávolíthat egy asszisztenst az engedélyezési listáról, amíg a problémák nem oldhatók fel.

> [!NOTE]
> A hangaktiválási engedélyhez minden esetben mikrofon-engedély szükséges. Ha egy segéd-alkalmazáshoz nem tartozik mikrofon-hozzáférés, nem jogosult a hangaktiválásra, és a rendszer letiltott állapotban fogja megjeleníteni a Hangaktiválás adatvédelmi beállításait.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>A mikrofon belefoglalásával kapcsolatos további követelmények

Az asszisztens szerzők, akik könnyebben és simábban szeretnék megtenni a felhasználók számára a hangaktiválást, a további követelmények betartásával is megtehetik a közzétételt és a jóváhagyást anélkül, hogy további utat kellene megadniuk a beállítások lapon. A jóváhagyást követően a Hangaktiválás azonnal elérhetővé válik, ha a felhasználó mikrofon engedélyt ad a Segéd alkalmazásnak. Ennek elvégzéséhez a Segéd-alkalmazásnak a következőket kell tennie a mikrofon jóváhagyásának megkérdezése **előtt** (például az `AppCapability.RequestAccessAsync` API használatával):

1. Egyértelmű és jól látható módon jelzi, hogy az alkalmazás szeretne-e hallgatni egy kulcsszót a felhasználó hangjára, *még akkor is, ha az alkalmazás nem fut*, és szeretné, ha a felhasználó beleegyezik
1. Az adathasználattal és az adatvédelmi szabályzatokkal kapcsolatos releváns információk, például a hivatalos adatvédelmi nyilatkozatra mutató hivatkozás
1. Kerülje az irányelvek vagy a vezető megfogalmazás elkerülését (például "kattintson az Igen elemre a következő üzenetben") az élmény folyamatában, amely kizárja a hangrögzítési viselkedést

Ha egy alkalmazás végrehajtja a fentieket, a hangaktiválási funkciót és a mikrofon belefoglalását is lehetővé teszi. További információért forduljon a szolgáltatáshoz, winvoiceassistants@microsoft.com és tekintse át az első használat élményét.

> [!NOTE]
> A zárolás feletti Hangaktiválás nem jogosult a mikrofonos hozzáférés automatikus engedélyezésére, és továbbra is szükség van a felhasználóra, hogy meglátogassa a Hangaktiválás adatvédelmi lapját, amely lehetővé teszi a fenti zárolási hozzáférés engedélyezését egy asszisztens számára.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További tudnivalók a Windows hangalapú asszisztensekkel kapcsolatos ajánlott eljárásokról](windows-voice-assistants-best-practices.md)