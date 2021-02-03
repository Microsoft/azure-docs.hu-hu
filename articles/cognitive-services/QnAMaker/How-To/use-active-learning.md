---
title: Az aktív tanulás használata a Tudásbázisban – QnA Maker
description: Ismerje meg, hogyan fejlesztheti a tudásbázist az aktív tanulással. Áttekintheti, elfogadhatja vagy elutasíthatja a meglévő kérdések eltávolítása vagy módosítása nélkül.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 396cba079baf92da1f5d14a4ecf3dfdb7de0aa2f
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509230"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Aktív tanulás használata a tudásbázis továbbfejlesztéséhez

Az [aktív tanulás](../Concepts/active-learning-suggestions.md) lehetővé teszi, hogy alternatív kérdéseket javasolva javítsa a Tudásbázis minőségét. A felhasználói beadványok figyelembe vesznek, és javaslatokként jelennek meg az alternatív kérdések listájában. Lehetősége van arra, hogy alternatív kérdésként adja hozzá ezeket a javaslatokat, vagy utasítsa el őket.

A Tudásbázis nem változik automatikusan. A módosítások érvénybe léptetéséhez el kell fogadnia a javaslatokat. Ezek a javaslatok kérdéseket tesznek fel, de nem módosítják vagy nem távolítják el a meglévő kérdéseket.

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Futtatókörnyezet verziójának frissítése az aktív tanulás használatához

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Az aktív tanulást a futtatókörnyezet 4.4.0 és újabb verziói támogatják. Ha a Tudásbázis egy korábbi verzión lett létrehozva, [frissítse a futtatókörnyezetet](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) a szolgáltatás használatára.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

QnA Maker felügyelt (előzetes verzió), mivel a futtatókörnyezetet maga a QnA Maker szolgáltatás üzemelteti, nem kell manuálisan frissíteni a futtatókörnyezetet.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Alternatív kérdések aktív tanulásának bekapcsolása

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil kiadás)](#tab/v1)

Az aktív tanulás alapértelmezés szerint ki van kapcsolva. A javasolt kérdések megtekintéséhez kapcsolja be a következőt:. Az aktív tanulás bekapcsolását követően el kell küldenie az adatokat az ügyfélalkalmazástól a QnA Maker. További információ: [építészeti folyamat a GenerateAnswer és a Train API-k egy robotból való használatához](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. A Tudásbázis közzétételéhez válassza a **Közzététel** lehetőséget. Az aktív tanulási lekérdezések csak a GenerateAnswer API-előrejelzési végpontról lesznek összegyűjtve. A QnA Maker portál teszt ablaktáblájára irányuló lekérdezések nem érintik az aktív tanulást.

1. Az aktív tanulás bekapcsolásához a QnA Maker-portálon nyissa meg a jobb felső sarokban, válassza ki a **nevét**, és lépjen a [**Szolgáltatásbeállítások**](https://www.qnamaker.ai/UserSettings)menüpontra.

    ![Kapcsolja be az aktív tanulás javasolt kérdéseit a szolgáltatás beállításai lapról. Válassza ki a felhasználónevét a jobb felső menüben, majd válassza a Szolgáltatásbeállítások elemet.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Keresse meg a QnA Maker szolgáltatást, majd állítsa be az **aktív tanulást**.

    > [!div class="mx-imgBorder"]
    > [![A szolgáltatás beállításai lapon kapcsolja be az aktív tanulási funkciót. Ha nem tudja váltani a szolgáltatást, előfordulhat, hogy frissítenie kell a szolgáltatást.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Az előző képen megadott pontos verzió csak példaként jelenik meg. A verzió eltérő lehet.

    Az **aktív tanulás** engedélyezése után a Tudásbázis rendszeres időközönként új kérdéseket javasol a felhasználó által benyújtott kérdések alapján. Az **aktív tanulást** letilthatja a beállítás újbóli bekapcsolásával.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker felügyelt (előzetes verzió)](#tab/v2)

Alapértelmezés **szerint az Active** learning QnA Maker felügyelt (előzetes verzió). A javasolt alternatív kérdések megtekintéséhez a szerkesztési lapon [használja a megtekintési beállításokat](../How-To/improve-knowledge-base.md#view-suggested-questions) .

---

## <a name="review-suggested-alternate-questions"></a>Javasolt alternatív kérdések áttekintése

[Tekintse át a további javasolt kérdéseket](improve-knowledge-base.md) az egyes tudásbázisok **szerkesztési** lapján.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](./manage-knowledge-bases.md)
