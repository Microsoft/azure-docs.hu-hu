---
title: Címkék hozzáadása a digital twinshez
titleSuffix: Azure Digital Twins
description: Címkék implementálja a digitális ikereket
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d45ef8c32e61b5567798b7c42af28badb222601
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376715"
---
# <a name="add-tags-to-digital-twins"></a>Címkék hozzáadása a digital twinshez 

A címkék fogalmával tovább azonosíthatja és kategorizálhatja a digitális ikereket. A felhasználók például replikálni szeretnék a címkéket a meglévő rendszerekből, például a [Haystack-címkékből](https://project-haystack.org/doc/TagModel)a saját Azure Digital Twins belül. 

Ez a dokumentum olyan mintákat ismertet, amelyek címkék digitális ikereszközökben való megvalósításához használhatók.

A címkék először tulajdonságokként lesznek hozzáadva a [modellben,](concepts-models.md) amelyek egy digitális ikereszközt írnak le. Ez a tulajdonság akkor lesz beállítva az ikereszközre, amikor a modell alapján létrejön. Ezt követően a címkék a [](concepts-query-language.md) lekérdezésekben az ikereket azonosítják és szűrik.

## <a name="marker-tags"></a>Jelölőcímkék 

A **jelölőcímke** egy egyszerű sztring, amely egy digitális iker megjelölése vagy kategorizálása, például "kék" vagy "vörös". Ez a sztring a címke neve, és a jelölőcímkéknek nincs kifejező értéke – a címke csak a jelenléte (vagy hiánya) miatt jelentős. 

### <a name="add-marker-tags-to-model"></a>Jelölőcímkék hozzáadása a modellhez 

A jelölőcímkék [DTDL-leképezésként](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) vannak modelle a és a `string` `boolean` között. A rendszer figyelmen kívül hagyja a logikai jelölőt, mivel csak a címke jelenléte `mapValue` fontos. 

Lássunk egy jelölőcímkét tulajdonságként végrehajtó ikermodell kivonatát:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Jelölőcímkék hozzáadása digitális ikerhez

Ha a tulajdonság egy digitális ikermodell része, a jelölőcímkét a tulajdonság értékének beállításával állíthatja be `tags` a digitális ikereszközben. 

Az alábbi példa három ikereszköz `tags` jelölőit tölti fel:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

Az alábbi példakód bemutatja, hogyan állíthatja be egy iker `tags` jelölőét a [.NET SDK használatával:](/dotnet/api/overview/azure/digitaltwins/client)

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

### <a name="query-with-marker-tags"></a>Lekérdezés jelölőcímkékkel

Miután hozzáadta a címkéket a digital twinshez, a címkékkel szűrheti az ikereket a lekérdezésekben. 

Itt van egy lekérdezés, amely a "red" címkével ellátott összes ikereszközt lekérdezi: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Címkéket összetettebb lekérdezésekhez is kombinálhat. Íme egy lekérdezés, amely lekérdezi az összes kerek ikereszközt, és nem pirosat: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Értékcímkék 

Az **értékcímke** egy kulcs-érték pár, amely minden címke számára értéket ad, például vagy `"color": "blue"` `"color": "red"` . Az értékcímkék létrehozása után jelölőcímkeként is használhatók a címke értékének figyelmen kívül hagyása által. 

### <a name="add-value-tags-to-model"></a>Értékcímkék hozzáadása a modellhez 

Az értékcímkék [DTDL-leképezésként](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) vannak modelle a és a `string` `string` között. A és `mapKey` a is `mapValue` jelentős. 

Lássunk egy részletet egy tulajdonságként egy értékcímkét végrehajtó ikermodellből:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Értékcímkék hozzáadása a digital twinshez

Ahogy a jelölőcímkék esetében, a digitális ikereszközben is beállíthatja az értékcímkét, ha beállítja ennek a tulajdonságnak az `tags` értékét a modellből. Ha értékcímkét használ jelölőcímkeként, a mezőt az üres `tagValue` sztringértékre () állíthatja `""` be. 

Az alábbi példa három ikereszköz értékét `tags` tölti fel:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Vegye figyelembe, hogy ebben a példában a és `red` a `purple` jelölőcímkékként használatos.

### <a name="query-with-value-tags"></a>Lekérdezés értékcímkével

Ahogy a jelölőcímkéknél, itt is használhatók értékcímkék az ikereszköz szűréséhez a lekérdezésekben. Értékcímkéket és jelölőcímkéket is használhat együtt.

A fenti példában `red` a jelölőcímkeként van használva. Ne feledje, hogy ez egy olyan lekérdezés, amely a "red" címkével ellátott összes ikereszközt lekérdezi: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Íme egy lekérdezés, amely az összes kis (érték címke) és nem piros entitást lekérdezi: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Következő lépések

További információ a digitális ikermodellek tervezéséről és kezeléséről:
* [*How-to: Manage DTDL models (DTDL-modellek kezelése)*](how-to-manage-model.md)

További információ az ikergráf lekérdezésével kapcsolatban:
* [*Hogyan lehet lekérdezni az ikergráfot?*](how-to-query-graph.md)
