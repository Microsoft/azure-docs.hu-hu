---
title: Mi az az ontológia?
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan DTDL iparági ontológiákat egy bizonyos tartomány modellezéséhez
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3393856b25040cff603ea2ef51e8adbcba78dc26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102034693"
---
# <a name="what-is-an-ontology"></a>Mi az az ontológia? 

Az Azure Digital Twins megoldás szókincse [modellekkel](concepts-models.md)van definiálva, amelyek leírják a környezetben létező entitások típusait.

Időnként, ha a megoldás egy adott iparághoz van kötve, akkor egyszerűbb és hatékonyabb lehet, ha az adott iparág számára már létező modelleket szeretne kezdeni, ahelyett, hogy saját modellt kellene létrehoznia a semmiből. Ezeket az előre meglévő modelleket **ontológiákat** nevezzük. 

Általánosságban elmondható, hogy az ontológia egy adott tartományhoz tartozó modellek összessége, például egy építési struktúra, a IoT rendszer, az intelligens város, az energia-rács, a webes tartalom stb. A ontológiákat gyakran használják az ismeretek diagramjainak sémái, mivel ezek a következők lehetnek:
* A szoftver-összetevők, a dokumentáció, a lekérdezési kódtárak és egyebek harmonizációja
* Kisebb beruházások a koncepcionális modellezés és a rendszerfejlesztés terén
* Könnyebb adategyüttműködés szemantikai szinten
* Ajánlott eljárás újrafelhasználása, nem pedig teljesen új, vagy "a kerék újbóli kiépítése"

Ez a cikk azt ismerteti, hogy miért és hogyan használhatók a ontológiákat az Azure Digital Twins-modellekhez, valamint hogy milyen ontológiákat és-eszközök érhetők el napjainkban.

## <a name="using-ontologies-for-azure-digital-twins"></a>A ontológiákat használata az Azure Digital Twins-hoz

A ontológiákat kiváló kiindulási pontot biztosít a digitális kettős megoldások számára. Az entitások tartomány-specifikus modelleket és kapcsolatokat foglalnak magukban egy digitális kettős gráf tervezéséhez, létrehozásához és elemzéséhez. A ontológiákat lehetővé teszik a megoldás-fejlesztők számára, hogy egy bevált kiindulási pontról megkezdhesse a digitális ikrek megoldását, és az üzleti problémák megoldására összpontosítsanak. A Microsoft által biztosított ontológiákat is könnyen bővíthetők, így testre szabhatja őket a megoldáshoz. 

Emellett a megoldásokban ezen ontológiákat használatával a különböző partnerek és szállítók közötti zökkenőmentes integrációhoz is beállíthatja őket, mivel a ontológiákat közös szókincset biztosíthatnak a megoldások között.

Mivel az Azure Digital Twins modelljei a [digitális Twins Definition Language (DTDL) nyelven](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)jelennek meg, a ontológiákat az Azure Digital Twins használatával is DTDL. 

## <a name="strategies-for-integrating-ontologies"></a>Stratégiák a ontológiákat integrálásához

Három lehetséges stratégia létezik az iparági szabványnak megfelelő ontológiákat és a DTDL integrálására. Kiválaszthatja az igényeinek leginkább megfelelőt:

| Stratégia | Leírás | Források |
| --- | --- | --- |
| **Bevezetés** | A megoldást egy olyan nyílt forráskódú DTDL ontológia használatával indíthatja el, amely széles körben elfogadott iparági szabványokra épül. Használhatja ezeket a modelleket is, vagy kiterjesztheti azokat a testreszabott megoldásokhoz tartozó saját kiegészítésekkel. | [*Fogalmak: az &nbsp; &nbsp; iparági &nbsp; szabványnak megfelelő ontológiákat bevezetése*](concepts-ontologies-adopt.md)<br><br>[*Fogalmak: a &nbsp; &nbsp; ontológiákat kiterjesztése*](concepts-ontologies-extend.md) |
| **Átalakítása** | Ha már rendelkezik meglévő modellekkel más szabványos formátumban, a DTDL konvertálhatja őket az Azure digitális Twins használatával. | [*Fogalmak: &nbsp; az &nbsp; ontológiákat konvertálása*](concepts-ontologies-convert.md)<br><br>[*Fogalmak: a &nbsp; &nbsp; ontológiákat kiterjesztése*](concepts-ontologies-extend.md) |
| **Szerző** | Saját egyéni DTDL-modelljeit bármikor kifejlesztheti a semmiből, bármilyen alkalmazható iparági szabványt használhat. | [*Fogalmak: DTDL modellek*](concepts-models.md) |

### <a name="using-ontology-strategies-in-a-model-development-path"></a>Az ontológia-stratégiák használata a modell fejlesztési útvonalán

Függetlenül attól, hogy melyik stratégiát választja az ontológia Azure digitális Ikrekbe való integrálásához, az alábbi teljes elérési úton követheti az ontológia DTDL-modellként való létrehozását és feltöltését.

1. Kezdje a DTDL modellezés áttekintésével és megismerésével [Az Azure digitális Twins-ben](concepts-models.md).
1. Folytassa a fent kiválasztott ontológia-integrációs stratégiával: a modellek [**elfogadása**](concepts-ontologies-adopt.md), [**átalakítása**](concepts-ontologies-convert.md)vagy [**szerzője**](concepts-models.md) az ontológia alapján.
    1. Ha szükséges, [terjessze ki](concepts-ontologies-extend.md) az ontológia-t az igényeinek megfelelően.
1. [Ellenőrizze a](how-to-parse-models.md) modelleket annak ellenőrzéséhez, hogy működnek-e DTDL-dokumentumok.
1. Töltse fel a kész modelleket az Azure Digital Twinsba az [API](how-to-manage-model.md#upload-models) -k vagy egy olyan minta használatával, mint az [Azure Digital Twins Model Uploader](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader).

Ezután használhatja a modelleket az Azure Digital Twins-példányban. 

Azokat a mintákat jelenítheti meg, mint például az [Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) vagy az [Azure Digital Twins Model](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer)vizualizáció, vagy ha a használatával [digitális ikreket](concepts-twins-graph.md)szeretne létrehozni, használja őket.

## <a name="next-steps"></a>Következő lépések

További információ a ontológiákat bevezetésének, átalakításának és szerzői műveletek elfogadásával kapcsolatos stratégiákról:
* [*Fogalmak: az iparági szabványnak megfelelő ontológiákat bevezetése*](concepts-ontologies-adopt.md)
* [*Fogalmak: az ontológiákat konvertálása*](concepts-ontologies-convert.md)
* [*Útmutató: DTDL-modellek kezelése*](how-to-manage-model.md)

Vagy Ismerje meg, hogyan használják a modelleket a digitális Twins létrehozásához: [*fogalmak: digitális ikrek és a Twin Graph*](concepts-twins-graph.md).