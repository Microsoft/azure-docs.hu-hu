---
title: Az iparági szabványnak megfelelő ontológiákat bevezetése
titleSuffix: Azure Digital Twins
description: Ismerkedjen meg az Azure Digital Twins szolgáltatáshoz használható meglévő iparági ontológiákat
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561464"
---
# <a name="adopting-an-industry-ontology"></a>Iparági ontológia bevezetése

Mivel könnyebben kezdődhet nyílt forráskódú DTDL, mint egy üres lapról, a Microsoft a tartományi szakértőkkel együttműködve közzéteszi a ontológiákat, amely széles körben elfogadott iparági konvenciókat képvisel, és számos különböző ügyfél-használati esetet támogat. 

Az eredmény olyan nyílt forráskódú DTDL-alapú ontológiákat, amely az iparági szabványok megismerését, készítését, megismerését vagy közvetlen felhasználását mutatja be. A ontológiákat úgy vannak kialakítva, hogy megfeleljenek az alsóbb rétegbeli fejlesztőknek, és az iparág széles körben elfogadják és/vagy bővíthetők.

A Microsoft jelenleg a Real Estate-partnerekkel együttműködve fejlesztett ki egy ontológia-t az intelligens épületekhez, amely az iparági szabványokon alapuló intelligens épületek modellezését teszi lehetővé, hogy elkerülje az újratalálmányt. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore Smart Building ontológia

A Microsoft a [RealEstateCore](https://www.realestatecore.io/), az ingatlan-tulajdonosok, a szoftvergyártók és a kutatóintézetek egyik svéd konzorciumával együttműködve nyílt forráskódú DTDL biztosít a Real Estate Industry számára: a [**DTDL-alapú RealEstateCore ontológia intelligens épületekhez**](https://github.com/Azure/opendigitaltwins-building).

Ez az intelligens épületek, az ontológia az intelligens épületek modellezését teszi lehetővé az iparági szabványok (például [tégla-séma](https://brickschema.org/ontology/) vagy a [W3C-építési topológia ontológia](https://w3c-lbd-cg.github.io/bot/index.html)) használatával, hogy elkerülje az újratalálmányt. Az ontológia az ajánlott eljárásokat is tartalmazza a használat és a megfelelő kibővítéshez. 

Ha többet szeretne megtudni az ontológia struktúrájáról és modellezési szabályairól, a használatáról, a bővítéséről és a közreműködés módjáról, látogasson el az ontológia [adattárára a githubon](https://github.com/Azure/opendigitaltwins-building). 

Ebben a blogbejegyzésben további információkat talál a RealEstateCore és céljaival kapcsolatos partneri kapcsolatokról, valamint a következő videóhoz: [*RealEstateCore, a digitális Twins-hoz készült intelligens épület, amely már elérhető*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="next-steps"></a>Következő lépések

* További információ az iparági szabványnak megfelelő ontológiákat kibővítéséről a specifikációk teljesítése érdekében: [*fogalmak: az iparági ontológiákat kiterjesztése*](concepts-ontologies-extend.md).

* Vagy folytassa a modellek fejlesztésének elérési útját a ontológiákat alapján: az [*ontológia-stratégiák használatával a modell fejlesztési útvonalán*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).