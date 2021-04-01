---
title: V3 API lekérdezési karakterlánc paraméterei
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "91309428"
---
A V3 API lekérdezési karakterlánc paraméterei a következők:

|Lekérdezési paraméter|LUIS-portál neve|Típus|Verzió|Alapértelmezett|Cél|
|--|--|--|--|--|--|
|`log`|Naplók mentése|boolean|V2 & V3|hamis|A lekérdezés tárolása a naplófájlban. Az alapértelmezett érték false (hamis).|
|`query`|-|sztring|Csak v3|Nincs alapértelmezett – a GET kérelemben szükséges|A **v2-ben** az előre jelzett érték a `q` paraméterben szerepel. <br><br>A **v3**-as verzióban a funkció a `query` paraméterben lesz átadva.|
|`show-all-intents`|Az összes cél pontszámának belefoglalása|boolean|Csak v3|hamis|Az összes leképezés visszaküldése a megfelelő pontszámmal a **jóslat. szándékok** objektumban. A rendszer a fölérendelt objektumban lévő objektumokként adja vissza a leképezéseket `intents` . Ez lehetővé teszi a programozott hozzáférést anélkül, hogy meg kellene találni a szándékot egy tömbben: `prediction.intents.give` . A v2-ben ezek egy tömbben voltak visszaadva. |
|`verbose`|További entitások részleteinek belefoglalása|boolean|V2 & V3|hamis|Ha **a v2** értéke TRUE (igaz) értékre van állítva, az összes előre jelzett leképezést visszaadja. Ha minden előre jelzett leképezésre van szüksége, használja a v3 paraméterét `show-all-intents` .<br><br>**A v3-** as verzióban ez a paraméter csak az entitások előrejelzését biztosító entitás-metaadatokat tartalmazza.  |
|`timezoneOffset`|-|sztring|2. verzió|-|A datetimeV2 entitásokra alkalmazott időzóna.|
|`datetimeReference`|-|sztring|V3|-|A datetimeV2 entitásokra alkalmazott [időzóna](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) . `timezoneOffset`A rendszer a v2-ből cseréli le.|