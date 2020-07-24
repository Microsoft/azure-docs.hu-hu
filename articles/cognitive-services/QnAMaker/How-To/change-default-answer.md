---
title: Alapértelmezett válasz beolvasása – QnA Maker
description: A rendszer az alapértelmezett választ adja vissza, ha nincs egyezés a kérdésben. Érdemes lehet módosítani az alapértelmezett választ a normál alapértelmezett válasz alapján.
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: d37e63d84be58e6ccd2f1e23a1344961d39ffa01
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054177"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker erőforrás alapértelmezett válaszának módosítása

A Tudásbázis alapértelmezett válaszát akkor kell visszaadnia, ha a válasz nem található. Ha ügyfélalkalmazás, például az [Azure bot Service](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)-t használja, akkor külön alapértelmezett válasz is lehet, ami azt jelzi, hogy a pontszám küszöbértéke nem felel meg a válasznak.

## <a name="types-of-default-answer"></a>Alapértelmezett válasz típusai

A Tudásbázisban kétféle alapértelmezett válasz van. Fontos megérteni, hogy a rendszer hogyan és mikor adja vissza az előrejelzési lekérdezéseket:


|Kérdés típusa|A Válasz leírása|
|--|--|
|KB-válasz, ha nem határoz meg választ|`No good match found in KB.`– Ha a [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) nem talál egyező választ a kérdésre, a `DefaultAnswer` rendszer az App Service beállításait adja vissza. Az azonos QnA Maker erőforrásban található összes Tudásbázis ugyanazzal az alapértelmezett válasz szöveggel rendelkezik.<br>A beállítást a Azure Portal az App Service-ben vagy a REST API-kon keresztül kezelheti a beállítás [beolvasásához](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) vagy [frissítéséhez](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) .|
|Követési utasítások szövege|Ha követő kérést használ egy beszélgetési folyamat során, előfordulhat, hogy nincs szükség a QnA pár válaszára, mert azt szeretné, hogy a felhasználó kiválassza a követési kéréseket. Ebben az esetben állítson be meghatározott szöveget az alapértelmezett válasz szövegének megadásával, amelyet a rendszer az összes előrejelzéssel együtt ad vissza a követő kérésekhez. A szöveg tanítási szövegként jelenik meg a követő kérések kiválasztásához. Az alapértelmezett válasz szövege például a következő: `Please select from the following choices` . Ezt a konfigurációt a dokumentum következő néhány szakaszában ismertetjük. A REST API használatával a Tudásbázis definíciójának részeként is beállítható `defaultAnswerUsedForExtraction` . [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)|

### <a name="client-application-integration"></a>Ügyfélalkalmazás-integráció

Egy ügyfélalkalmazás, például egy, az **Azure bot Service**-szel rendelkező robot esetében az alábbi gyakori forgatókönyvek közül választhat:

* A Tudásbázis beállításainak használata
* Az ügyfélalkalmazás eltérő szövegét használva megkülönböztetheti a válasz eredményét, de nem felel meg a pontszám küszöbértékének. Ez a szöveg lehet a kódban tárolt statikus szöveg, vagy az ügyfélalkalmazás beállítások listájában lehet tárolni.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Követő kérés alapértelmezett válaszának beállítása a Tudásbázis létrehozásakor

Új Tudásbázis létrehozásakor az alapértelmezett válasz szöveg az egyik beállítás. Ha úgy dönt, hogy nem állítja be azt a létrehozási folyamat során, később is megváltoztathatja a következő eljárással.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Követő kérés alapértelmezett válaszának módosítása QnA Maker portálon

A rendszer a Tudásbázis alapértelmezett válaszát adja vissza, ha a QnA Maker szolgáltatás nem ad vissza választ.

1. Jelentkezzen be a [QnA Maker portálra](https://www.qnamaker.ai/) , és válassza ki a tudásbázist a listából.
1. A navigációs sávon válassza a **Beállítások** lehetőséget.
1. Módosítsa az **alapértelmezett válasz szöveg** értékét a **Tudásbázis kezelése** szakaszban.

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Képernyőkép: QnA Maker portál, beállítások lap, alapértelmezett válasz szövegmezővel kiemelve.":::

1. A módosítás mentéséhez válassza a **Mentés és a betanítás** lehetőséget.

## <a name="next-steps"></a>További lépések

* [Tudásbázis létrehozása](../How-to/manage-knowledge-bases.md)
