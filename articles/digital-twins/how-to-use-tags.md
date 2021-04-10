---
title: Címkék hozzáadása a digitális ikrekhez
titleSuffix: Azure Digital Twins
description: 'Lásd: címkék implementálása a digitális ikrekben'
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 70bf46de072a97eca810dda60a5331df14172ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100555162"
---
# <a name="add-tags-to-digital-twins"></a>Címkék hozzáadása a digitális ikrekhez 

A címkék fogalmát a digitális ikrek további azonosítására és kategorizálására használhatja. Bizonyos esetekben előfordulhat, hogy a felhasználók a meglévő rendszerekről, például a szénakazalban- [címkékről](https://project-haystack.org/doc/TagModel)kívánják replikálni a címkéket az Azure digitális Twins példányain belül. 

Ez a dokumentum a címkék digitális ikreken való megvalósításához használható mintákat ismerteti.

A rendszer először hozzáadja a címkéket a [modellen](concepts-models.md) belül, amely a digitális Twin-et ismerteti. Ezt a tulajdonságot ezután a Twin értékre állítja be, amikor a modell alapján jön létre. Ezt követően a címkék használhatók a [lekérdezésekben](concepts-query-language.md) az ikrek azonosítására és szűrésére.

## <a name="marker-tags"></a>Jelölő címkék 

A **jelölő címke** egy egyszerű karakterlánc, amely egy digitális iker jelölésére vagy kategorizálására szolgál, például "Blue" vagy "Red". Ez a karakterlánc a címke neve, és a jelölő címkék nem rendelkeznek értelmes értékkel – a címke csak a jelenléte (vagy a távollét) alapján jelentős. 

### <a name="add-marker-tags-to-model"></a>Jelölő címkék hozzáadása a modellhez 

A jelölő címkék [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -leképezésként vannak modellezve `string` `boolean` . A logikai értéket a `mapValue` rendszer figyelmen kívül hagyja, mivel a címke jelenléte minden fontos. 

Íme egy részlet egy olyan kettős modellből, amely a jelölő címkét tulajdonságként implementálja:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Jelölő címkék hozzáadása a digitális ikrekhez

Ha a `tags` tulajdonság egy digitális Twin modell részét képezi, beállíthatja a jelölő címkét a digitális Twin-ben a tulajdonság értékének megadásával. 

Íme egy példa, amely `tags` három ikrek számára tölti fel a jelölőt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>Lekérdezés jelölő címkékkel

Miután hozzáadta a címkéket a digitális ikrekhez, a címkék használatával szűrheti az ikreket a lekérdezésekben. 

Itt látható egy lekérdezés, amely a "Red" címkével jelölt összes ikreket lekérdezi: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Az összetettebb lekérdezésekhez címkék is kombinálhatók. Itt látható egy lekérdezés, amely az összes olyan ikreket lekérdezi, amely nem piros: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Érték Címkék 

Az **érték címke** egy kulcs-érték pár, amely az egyes címkék értékének megadására szolgál (például `"color": "blue"` vagy `"color": "red"` ). Miután létrehozta az érték címkét, azt jelölő címkeként is használhatja a címke értékének figyelmen kívül hagyásával. 

### <a name="add-value-tags-to-model"></a>Érték Címkék hozzáadása a modellhez 

Az érték címkék [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -leképezésként vannak modellezve `string` `string` . A `mapKey` és a `mapValue` is jelentős. 

Itt látható egy olyan kettős modellből származó részlet, amely egy érték címkét valósít meg tulajdonságként:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Érték Címkék hozzáadása a digitális ikrekhez

Ahogy a jelölő címkék esetében is, a Value (érték) címkét egy digitális ikeren állíthatja be úgy, hogy a `tags` tulajdonság értékét a modellből állítja be. Ha az érték címkét jelölő címkeként szeretné használni, beállíthatja a `tagValue` mezőt az üres sztring () értékre `""` . 

Íme egy példa, amely három ikrek értékét tölti `tags` fel:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Vegye figyelembe, hogy a `red` `purple` példában jelölő címkékként használjuk.

### <a name="query-with-value-tags"></a>Lekérdezés érték címkékkel

Mint a jelölő címkék esetében, az érték címkék használatával szűrheti az ikreket a lekérdezésekben. Az érték címkék és a jelölő címkék együtt is használhatók.

A fenti példában `red` jelölő címkeként használjuk. Ne feledje, hogy ez egy lekérdezés a "Red" címkével jelölt összes ikrek beszerzéséhez: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Itt látható egy lekérdezés, amely az összes olyan entitást beolvassa, amely kis (érték címkével), nem piros: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Következő lépések

További információ a digitális kettős modellek tervezéséről és kezeléséről:
* [*Útmutató: DTDL-modellek kezelése*](how-to-manage-model.md)

További információ a Twin Graph lekérdezéséről:
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)