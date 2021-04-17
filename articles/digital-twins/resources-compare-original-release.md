---
title: Eltérések az eredeti kiadáshoz
titleSuffix: Azure Digital Twins
description: A Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 2bfd36b79ad800c14a68948aa8488e842d3f4def
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481174"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Mi az új Azure Digital Twins? Miben különbözik az eredeti verziótól (2018)?

Az első nyilvános előzetes Azure Digital Twins 2018 októberében jelent meg. Bár az eredeti verzió alapfogalmai átesnek a jelenlegi szolgáltatáson, az interfészek és az implementáció számos részlete megváltozott, hogy a szolgáltatás rugalmasabb és hozzáférhetőbb legyen. Ezeket a változásokat az ügyfelek visszajelzései motiválták.

> [!IMPORTANT]
> Az új szolgáltatás kibővített képességeinek fényében az eredeti Azure Digital Twins szolgáltatás ki lett vezetve. 2021 januárjával az API-k és a kapcsolódó adatok már nem érhetők el.

Ha az első nyilvános előzetes verzióban használta a Azure Digital Twins első verzióját, a jelen cikkben található információk és ajánlott eljárások segítségével megismeri, hogyan használhatja az aktuális szolgáltatást, és hogyan használhatja annak funkcióit.

## <a name="differences-by-topic"></a>Különbségek témakör szerint

Az alábbi diagram egymás mellett mutatja be a szolgáltatás eredeti verziója és az aktuális szolgáltatás között változott fogalmakat.

| Témakör | Az eredeti verzióban | Az aktuális verzióban |
| --- | --- | --- | --- |
| **Modellezés**<br>*Rugalmasabb* | Az eredeti kiadás az intelligens terek köré lett tervezve, ezért beépített szókincset használt az épületekhez. | A Azure Digital Twins tartománytól független. Saját egyéni szókincset és egyéni modelleket definiálhat a megoldáshoz, hogy rugalmasabb módon képviselje a környezetek több fajtáját.<br><br>További információ: [*Fogalmak: Egyéni modellek.*](concepts-models.md) |
| **Topológia**<br>*Rugalmasabb*| Az eredeti kiadás egy intelligens terekre szabott fa adatstruktúrát támogatott. A digitális ikerkapcsolatok hierarchikus kapcsolatokkal voltak összekapcsolva. | A jelenlegi kiadással a digitális ikereket tetszőleges gráf topológiákhoz csatlakoztathatja, tetszés szerint rendezve. Ez nagyobb rugalmasságot biztosít a való világ összetett kapcsolatainak kifejeződését.<br><br>További [*információ: Fogalmak: Digitális ikerek és az ikergráf.*](concepts-twins-graph.md) |
| **Számítás**<br>*Gazdagabb, rugalmasabb* | Az eredeti kiadásban az események és a telemetria feldolgozásának logikáját a felhasználó által definiált JavaScript-függvények (UDF-ek) definiálták. Az UDF-ekkel való hibakeresés korlátozott volt. | A jelenlegi kiadás nyílt számítási modellel rendelkezik: egyéni logikát biztosít külső számítási erőforrások, például a [Azure Functions.](../azure-functions/functions-overview.md) Ez lehetővé teszi egy ön által választott programozási nyelv használatát, az egyéni kódtárak korlátozás nélküli használatát, valamint a külső szolgáltatásban elérhető erőforrások fejlesztésének és hibakeresésének előnyeit.<br><br>További információ: [*Útmutató: Azure-függvény beállítása adatok feldolgozásához.*](how-to-create-azure-function.md) |
| **Eszközkezelés IoT Hub**<br>*Akadálymentesebb* | Az eredeti kiadásban felügyelt [](../iot-hub/about-iot-hub.md) eszközök a IoT Hub szolgáltatás belső példányával Azure Digital Twins eszközök. Ez az integrált központ nem volt teljesen elérhető a fejlesztők számára. | A jelenlegi kiadásban "saját" IoT Hubot fog létrehozni egy függetlenül létrehozott IoT Hub-példány csatolásával (a már eddig is felügyelt eszközökkel együtt). Ez teljes hozzáférést biztosít a IoT Hub funkcióihoz, és így Ön irányíthatja az eszközkezelést.<br><br>További információ: [*Útmutató: Telemetria*](how-to-ingest-iot-hub-data.md)be IoT Hub. |
| **Biztonság**<br>*További standard* | Az eredeti kiadás előre meghatározott szerepkörökkel rendelkezik, amelyek a példányhoz való hozzáférés kezelésére használhatók. | A jelenlegi kiadás ugyanazokkal az Azure-beli szerepköralapú [hozzáférés-vezérlési (Azure RBAC)](../role-based-access-control/overview.md) háttérszolgáltatással integrálható, mint más Azure-szolgáltatások. Ez egyszerűbbé teszi a hitelesítést a megoldásában más Azure-szolgáltatások között, például IoT Hub, Azure Functions, Event Grid stb.<br>Az RBAC használatával továbbra is használhat előre definiált szerepköröket, vagy létrehozhat és konfigurálhat egyéni szerepköröket.<br><br>További információ: [*Fogalmak: Biztonság Azure Digital Twins megoldásokhoz.*](concepts-security.md) |
| **Méretezhetőség**<br>*Nagyobb* | Az eredeti kiadásban az eszközökre, üzenetekre, gráfra és skálázható egységekre vonatkozó skálázható korlátozások vonatkoznak. Előfizetésenként csak Azure Digital Twins példány volt támogatva.  | A jelenlegi kiadás egy új, továbbfejlesztett skálázható architektúrára támaszkodik, és nagyobb számítási teljesítményt biztosít. Emellett régiónként és előfizetésenként 10 példányt támogat.<br><br>Az Azure Digital Twins aktuális kiadás [*korlátaival*](reference-service-limits.md) kapcsolatos részletekért tekintse meg az Azure Digital Twins szolgáltatási korlátokat. |

## <a name="service-limits"></a>Szolgáltatási korlátozások

A korlátozásokkal Azure Digital Twins lásd: Azure Digital Twins [*szolgáltatáskorlátok.*](reference-service-limits.md)

## <a name="next-steps"></a>Következő lépések

* Ismerkedés az aktuális kiadással a rövid útmutató: Rövid [*útmutató: Mintaforgatókönyv megismerása.*](quickstart-azure-digital-twins-explorer.md)

* Vagy kezdjen el olvasni a fő fogalmakról az [*Alapfogalmak: Egyéni modellek .*](concepts-models.md)