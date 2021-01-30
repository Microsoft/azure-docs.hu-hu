---
title: Eltérések az eredeti kiadástól
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogy mi változott az Azure Digital Twins új verziójában
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072634"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>Mi az új Azure digitális Twins? Miben különbözik az eredeti verziótól (2018)?

Az Azure Digital Twins első nyilvános előzetes verziója 2018 októberében jelent meg. Habár az eredeti verzió alapfogalmai az aktuális szolgáltatásra kerültek át, számos interfész és megvalósítási részlet módosult, hogy a szolgáltatás rugalmasabb és hozzáférhető legyen. Ezeket a módosításokat az ügyfelek visszajelzései indokolták.

> [!IMPORTANT]
> Az új szolgáltatás kibővített képességeinek tükrében az eredeti Azure Digital Twins szolgáltatás kivonásra került. Január 2021, az API-k és a kapcsolódó adatai már nem érhetők el.

Ha az első nyilvános előzetes verzióban használta az Azure Digital Twins első verzióját, a jelen cikkben található információk és ajánlott eljárások segítségével megismerheti, hogyan használható a jelenlegi szolgáltatás, és hogyan használhatja a funkcióit.

## <a name="differences-by-topic"></a>Különbségek témakör szerint

Az alábbi diagram a szolgáltatás és az aktuális szolgáltatás eredeti verziójának változásait tartalmazó, egymás melletti áttekintést nyújt.

| Témakör | Az eredeti verzióban | A jelenlegi verzióban |
| --- | --- | --- | --- |
| **Modellezés**<br>*Rugalmasabb* | Az eredeti kiadás az intelligens tárolóhelyek köré lett tervezve, ezért beépített szókincstel rendelkezik az épületekhez. | Az aktuális Azure-beli digitális ikrek tartományi-függetlenek. Saját egyéni szókincsét és egyéni modelljét is meghatározhatja a megoldáshoz, hogy több fajta környezetet képviselni rugalmasabb módon.<br><br>További információ a [*fogalmakról: egyéni modellek*](concepts-models.md). |
| **Topológia**<br>*Rugalmasabb*| Az eredeti kiadás egy faszerkezetes adatstruktúrát támogat, amely intelligens tárolóhelyekhez van igazítva. A digitális Twins hierarchikus kapcsolatokkal kapcsolódott. | A jelenlegi kiadással a digitális ikrek tetszőleges gráf-topológiához csatlakoztathatók, azonban azt szeretné. Ez nagyobb rugalmasságot biztosít a valós világ összetett kapcsolatainak kinyilvánítása érdekében.<br><br>További információ a [*fogalmakról: digitális ikrek és a Twin Graph*](concepts-twins-graph.md). |
| **Számítás**<br>*Gazdagabb, rugalmasabb* | Az eredeti kiadásban az események és a telemetria logikája definiálva lett a JavaScript felhasználó által definiált függvényekben (UDF). A hibakeresés a UDF korlátozott volt. | Az aktuális kiadás egy nyitott számítási modellel rendelkezik: egyéni logikát biztosít külső számítási erőforrások (például [Azure functions](../azure-functions/functions-overview.md)) csatolásával. Ez lehetővé teszi a választott programozási nyelv használatát, az egyéni kódtárak korlátozás nélküli elérését, és kihasználhatja a külső szolgáltatás által esetlegesen használt fejlesztési és hibakeresési erőforrásokat.<br><br>További információ [*: Azure-függvény beállítása az adatfeldolgozáshoz*](how-to-create-azure-function.md). |
| **Eszközkezelés IoT Hub**<br>*Könnyebben elérhető* | Az eredeti kiadással felügyelt eszközök [IoT hub](../iot-hub/about-iot-hub.md) egy példányával, amely az Azure Digital Twins szolgáltatásban belső volt. Ez az integrált központ nem volt teljesen elérhető a fejlesztők számára. | A jelenlegi kiadásban a "saját" IoT hub egy egymástól független módon létrehozott IoT Hub-példány csatolásával (a már felügyelt eszközökkel együtt). Ez teljes hozzáférést biztosít a IoT Hub képességeihez, és lehetővé teszi az eszközkezelés irányítását.<br><br>További információ [*: telemetria beolvasása IoT hubból*](how-to-ingest-iot-hub-data.md). |
| **Biztonság**<br>*További szabványos* | Az eredeti kiadás előre definiált szerepkörökkel rendelkezett, amelyeket a példányhoz való hozzáférés kezeléséhez használhat. | A jelenlegi kiadás integrálható ugyanazzal az [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/overview.md) , amelyet más Azure-szolgáltatások használnak. Ez egyszerűbbé teheti a megoldásban lévő más Azure-szolgáltatások (például a IoT Hub, a Azure Functions, a Event Grid stb.) hitelesítését.<br>A RBAC-mel előre definiált szerepköröket is használhat, vagy létrehozhat és konfigurálhat egyéni szerepköröket.<br><br>További információ a [*fogalmakról: az Azure Digital Twins-megoldások biztonsága*](concepts-security.md). |
| **Méretezhetőség**<br>*Nagyobb* | Az eredeti kiadásban az eszközök, az üzenetek, a diagramok és a skálázási egységek méretezési korlátai is megvannak. Az előfizetések esetében csak egy Azure digitális Twins-példány támogatott.  | A jelenlegi kiadás egy új architektúrára támaszkodik, amely továbbfejlesztett méretezhetőséggel rendelkezik, és nagyobb számítási teljesítményt nyújt. Emellett régiónként legfeljebb 10 példányt támogat.<br><br>Az aktuális kiadás korlátainak részletes ismertetését az [*Azure digitális Twins szolgáltatás korlátozásai*](reference-service-limits.md) című témakörben tekintheti meg. |

## <a name="service-limits"></a>Szolgáltatási korlátozások

Az Azure Digital Twins-korlátok listáját az [*Azure digitális Twins szolgáltatás korlátai*](reference-service-limits.md)című részben tekintheti meg.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg a jelenlegi kiadással a gyors üzembe helyezési útmutatóban: rövid útmutató [*: példa a forgatókönyvre*](quickstart-adt-explorer.md).

* Vagy kezdjen el olvasni a főbb fogalmakkal kapcsolatban [*: egyéni modellek*](concepts-models.md).