---
title: Tudásbázis – QnA Maker
description: QnA Maker a legjobbat tanulja meg a modell változásainak, a kitalált példáknak, a közzétételnek és az adatoknak a végponti lekérdezésekből való összegyűjtése során.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: e52e7151bc30a19bd6f6041d52effdd799a87c99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91776969"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Tudásbázis életciklusa QnA Maker
QnA Maker a legjobbat tanulja meg a modell változásainak, a kitalált példáknak, a közzétételnek és az adatoknak a végponti lekérdezésekből való összegyűjtése során.

![Tartalomkészítési ciklus](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>QnA Maker-tudásbázis létrehozása
A (z) QnA Maker Tudásbázis (KB) végpontja a TUDÁSBÁZISCIKK tartalmának megfelelő választ ad a felhasználók lekérdezéséhez. A Tudásbázis létrehozása egy egyszeri művelet, amely a kérdések, válaszok és a kapcsolódó metaadatok tartalmi tárházának beállítására szolgál. Egy KB-ot a meglévő tartalom bejárásával hozhat létre, például a következő forrásokból:

- Gyakori kérdések oldalai
- Termék-kézikönyvek
- Q-A pár

Útmutató [Tudásbázis létrehozásához](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>A Tudásbázis tesztelése és frissítése

A Tudásbázis készen áll a tesztelésre, ha a tartalom fel van töltve, vagy akár szerkesztőségi módon, akár automatikus kinyeréssel. Az interaktív tesztelés a QnA Maker portálon, a **teszt** panelen végezhető el. Általános felhasználói lekérdezéseket adhat meg. Ezt követően ellenőrizze, hogy a válaszok a helyes válasz és a megfelelő megbízhatósági pontszám alapján lettek-e visszaadva.


* **Az alacsony megbízhatósági pontszámok kijavítása**: alternatív kérdések hozzáadása.
* **Ha egy lekérdezés helytelenül adja vissza az [alapértelmezett választ](../How-to/change-default-answer.md)**: új válaszok hozzáadása a megfelelő kérdéshez.

A test-Update ezen szoros ciklusa addig folytatódik, amíg meg nem felel az eredményekkel. Ismerje meg, hogyan [tesztelheti tudásbázisát](../How-To/test-knowledge-base.md).

Nagyméretű Tudásbázis esetében használja az automatikus tesztelést a [GENERATEANSWER API](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) -val és a `isTest` Body tulajdonsággal, amely a `test` közzétett Tudásbázis helyett a tudásbázist kérdezi le.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele
Ha elkészült a Tudásbázis tesztelésével, közzéteheti. A közzététel leküldi a tesztelt Tudásbázis legújabb verzióját egy dedikált Azure Cognitive Search indexre, amely a **közzétett** tudásbázist jelképezi. Egy végpontot is létre fog hozni, amelyet az alkalmazásban vagy a csevegőrobot meg tud hívni.

A közzétételi művelet miatt a Tudásbázis tesztelési verziójában végrehajtott további módosítások nem érintik a közzétett verziót. Előfordulhat, hogy a közzétett verzió éles alkalmazásban van.

Ezen tudásbázisok mindegyike külön tesztelésre is megcélozható. Az API-k használatával megcélozhatja a Tudásbázis tesztelési verzióját a `isTest` Body tulajdonsággal a generateAnswer hívásban.

Ismerje meg, hogyan [teheti közzé a tudásbázist](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Használat monitorozása
Ahhoz, hogy naplózni tudja a szolgáltatás csevegési naplóit, engedélyeznie kell Application Insights a [QnA Maker szolgáltatás létrehozásakor](../How-To/set-up-qnamaker-service-azure.md).

A szolgáltatás használatának különböző elemzéseit is lekérheti. További információ az Application-elemzések használatáról az [QnA Maker szolgáltatás elemzési](../How-To/get-analytics-knowledge-base.md)adatainak beszerzéséhez.

Az elemzések alapján elsajátított megfelelő [frissítéseket a Tudásbázisban](../How-To/edit-knowledge-base.md)teheti meg.

## <a name="version-control-for-data-in-your-knowledge-base"></a>A tudásbázisban található adatok verziószám-vezérlése

Az adatverzió-vezérlés a QnA Maker portál **Beállítások** lapjának importálási/exportálási szolgáltatásain keresztül érhető el.

A Tudásbázisról biztonsági mentést készíthet a Tudásbázis exportálásával `.tsv` vagy `.xls` formátumban. Az exportálást követően vegye fel ezt a fájlt a normál verziókövetés ellenőrzésének részeként.

Ha egy adott verzióra vissza kell térnie, importálnia kell a fájlt a helyi rendszerből. Az exportált tudásbázist **csak importálással kell használni** a **Beállítások** lapon. Nem használható fájl-vagy URL-dokumentum típusú adatforrásként. Ezzel felülírja a Tudásbázisban az importált fájl tartalmával kapcsolatos kérdéseket és válaszokat.

## <a name="test-and-production-knowledge-base"></a>Tesztelési és üzemi Tudásbázis
A Tudásbázis a QnA Maker által létrehozott, karbantartott és használt kérdések és válaszok tárháza. Minden QnA Maker erőforrás több Tudásbázisban is tarthat.

A Tudásbázis két állapottal rendelkezik: *tesztelés* és *Közzététel*.

### <a name="test-knowledge-base"></a>Tudásbázis tesztelése

A *teszt Tudásbázis* a jelenleg szerkesztett és mentett verzió. A teszt verziója pontosságra és a válaszok teljességére lett tesztelve. A teszt Tudásbázisban végrehajtott módosítások nem érintik az alkalmazás vagy a csevegési robot végfelhasználóját. A teszt Tudásbázis a `test` http-kérelemben is ismert. Az `test` ismeret a QnA Maker portál interaktív **teszt** paneljén érhető el.

### <a name="production-knowledge-base"></a>Üzemi Tudásbázis

A *közzétett Tudásbázis* a csevegési robotban vagy alkalmazásban használt verzió. A Tudásbázis közzététele a tesztelési verziójának tartalmát a közzétett verzióba helyezi. A közzétett Tudásbázis az alkalmazás által a végponton keresztül használt verzió. Győződjön meg arról, hogy a tartalom helyes és jól tesztelt. A közzétett Tudásbázis a `prod` http-kérelemben is ismert.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Aktív tanulási javaslatok](./active-learning-suggestions.md)