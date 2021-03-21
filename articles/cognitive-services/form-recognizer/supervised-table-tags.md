---
title: Hogyan használhatók a táblázatos címkék az egyéni űrlapos modell-űrlap felismerésének betanításához
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja hatékonyan a felügyelt táblák címkézését.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 5422520c6a863876091d7820a5c07fa2413346c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467818"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Táblázatos címkék használata az egyéni űrlapos modell betanításához

Ebből a cikkből megtudhatja, hogyan taníthatja be az egyéni űrlap-modellt a Table Tags (labels) használatával. Bizonyos forgatókönyvek összetettebb címkézést igényelnek, mint a kulcs-érték párok igazítása. Ilyen forgatókönyvek például az űrlapokból származó adatok kinyerése összetett hierarchikus struktúrákkal vagy a szolgáltatás által automatikusan észlelt és kinyert elemek megtalálása. Ezekben az esetekben a táblázat címkével is elvégezheti az egyéni űrlapos modell betanítását.

## <a name="when-should-i-use-table-tags"></a>Mikor érdemes használni a Table tageket?

Íme néhány példa arra, hogy mikor érdemes használni a táblázat címkéit:

- A kinyerni kívánt adatok táblázatként jelennek meg az űrlapokon, és a táblázatok szerkezete is hasznos lehet. Például a táblázat minden sora egy tételt jelöl, és a sor minden oszlopa az adott elem egy adott funkcióját jelöli. Ebben az esetben olyan tábla címkét használhat, amelyben egy oszlop a funkciókat jelképezi, és egy sor az egyes funkciókról tartalmaz információkat.
- A kinyerni kívánt adatok nem adott formában jelennek meg, hanem szemantikailag az adatok egy kétdimenziós rácsban is elférnek. Például az űrlapon szerepelnek a személyek listája, valamint a következők: Utónév, vezetéknév és e-mail-cím. Ezt az információt ki szeretné bontani. Ebben az esetben használhat egy, az utónév, a vezetéknév és az e-mail-címet tartalmazó tábla címkét oszlopként, és az egyes sorok a listában szereplő személy adataival vannak feltöltve.

> [!NOTE]
> Az űrlap-felismerő automatikusan megkeresi és Kinyeri a dokumentumok összes táblázatát, függetlenül attól, hogy a táblák címkével rendelkeznek-e. Ezért nem kell minden táblát címkével ellátnia az űrlapból egy tábla címkéjével, és a táblázat címkéi nem kell replikálni az űrlapon található nagyon fontos tábla szerkezetét. Az űrlap-felismerő által automatikusan kinyert táblák a JSON-kimenet pageResults szakaszában lesznek felszámítva.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Táblázatos címke létrehozása a Form OCR test Tool eszközzel (FOTT)
<!-- markdownlint-disable MD004 -->
* Döntse el, hogy **dinamikus** vagy **rögzített méretű** Table címkét szeretne-e használni. Ha a sorok száma a dokumentumtól a dokumentumig változik, használjon dinamikus tábla címkét. Ha a sorok száma konzisztens a dokumentumok között, használjon rögzített méretű táblát.
* Ha a tábla címkéje dinamikus, adja meg az oszlopok nevét és az adattípust és a formátumot az egyes oszlopokhoz.
* Ha a tábla rögzített méretű, az egyes címkékhez adja meg az oszlop nevét, a sor nevét, az adattípust és a formátumot.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Tábla címke konfigurálása":::

## <a name="label-your-table-tag-data"></a>Tábla címkézési adatai

* Ha a projektben van egy tábla címkéje, megnyithatja a címkézés panelt, és feltöltheti a címkét úgy, ahogy a kulcs-érték mezők címkéje lenne.
:::image type="content" source="media/table-labeling.png" alt-text="Címke tábla címkékkel":::

## <a name="next-steps"></a>Következő lépések

Kövesse az egyéni űrlap-felismerő modell betanítását és használatát:

> [!div class="nextstepaction"]
> [Betanítás címkék használatával a minta feliratozási eszközzel](quickstarts/label-tool.md)

## <a name="see-also"></a>Lásd még

* [Mi a Form Recognizer?](overview.md)
>
