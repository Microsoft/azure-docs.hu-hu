---
title: Lekérdezési egységek az Azure Digital Twinsban
titleSuffix: Azure Digital Twins
description: A lekérdezési egységek számlázási koncepciójának megismerése az Azure Digital Twinsban
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0e1c5f08c4292e4f3dfec448d8bf54d5d5601840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "99050498"
---
# <a name="query-units-in-azure-digital-twins"></a>Lekérdezési egységek az Azure Digital Twinsban 

Az Azure Digital Twins **lekérdezési egység (Qu)** egy igény szerinti számítási egység, amely az [Azure Digital Twins-lekérdezések](how-to-query-graph.md) futtatására szolgál a [lekérdezési API](/rest/api/digital-twins/dataplane/query)használatával. 

Elvonta a rendszererőforrásokat, például a PROCESSZORt, a IOPS és a memóriát, amely az Azure Digital Twins által támogatott lekérdezési műveletek elvégzéséhez szükséges, így a használat nyomon követhető a lekérdezési egységekben.

A lekérdezés végrehajtásához felhasznált lekérdezési egységek mennyiségét a következő érinti:...

* a lekérdezés bonyolultsága
* az eredményhalmaz mérete (így a 10 találatokat visszaadó lekérdezés több QUs fog használni, mint a hasonló összetettségű lekérdezés, amely csak egyetlen eredményt ad vissza)

Ez a cikk a lekérdezési egységek értelmezését és a lekérdezési egység felhasználásának nyomon követését ismerteti.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>A lekérdezési egység felhasználásának megkeresése az Azure Digital Ikrekben

Ha az Azure Digital Twins [lekérdezési API](/rest/api/digital-twins/dataplane/query)használatával futtat lekérdezést, a válasz fejlécében ellenőrizheti a lekérdezés által felhasznált QUs számát. Keresse meg a "lekérdezési díj" kifejezést az Azure digitális ikrektől visszaküldött válaszban.

Az Azure Digital Twins [SDK](how-to-use-apis-sdks.md) -k lehetővé teszik a lekérdezési díj fejlécének kinyerését a lapozható válaszból. Ez a szakasz bemutatja, hogyan lehet lekérdezni a digitális ikreket, és hogyan lehet megismételni a lapozható választ a lekérdezési díj fejlécének kinyeréséhez. 

A következő kódrészlet azt mutatja be, hogyan lehet kibontani a lekérdezési API meghívásakor felmerülő lekérdezési díjakat. Első lépésként megismétli a lekérdezési fejlécet, majd megismétli a digitális dupla eredményeket az egyes lapokon belül. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure digitális Twins lekérdezéséről, látogasson el ide:

* [*Fogalmak: lekérdezési nyelv*](concepts-query-language.md)
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)
* [A lekérdezési API-referenciák dokumentációja](/rest/api/digital-twins/dataplane/query/querytwins)

Az Azure Digital Twins-lekérdezésekkel kapcsolatos korlátokat az [*Azure Digital Twins szolgáltatás korlátaiban*](reference-service-limits.md)találja.