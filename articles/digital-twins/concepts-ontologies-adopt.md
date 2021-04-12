---
title: Az iparági szabványnak megfelelő ontológiákat bevezetése
titleSuffix: Azure Digital Twins
description: Ismerkedjen meg az Azure Digital Twins szolgáltatáshoz használható meglévő iparági ontológiákat
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102124227"
---
# <a name="adopting-an-industry-ontology"></a>Iparági ontológia bevezetése

Mivel könnyebben kezdődhet nyílt forráskódú DTDL, mint egy üres lapról, a Microsoft a tartományi szakértőkkel együttműködve közzéteszi a ontológiákat, amely széles körben elfogadott iparági konvenciókat képvisel, és számos különböző ügyfél-használati esetet támogat. 

Az eredmény olyan nyílt forráskódú DTDL-alapú ontológiákat, amely az iparági szabványok megismerését, készítését, megismerését vagy közvetlen felhasználását mutatja be. A ontológiákat úgy vannak kialakítva, hogy megfeleljenek az alsóbb rétegbeli fejlesztőknek, és az iparág széles körben elfogadják és/vagy bővíthetők.

A Microsoft jelenleg a partnerekkel együttműködve fejlesztett ki egy ontológia for [Smart-épületeket](#realestatecore-smart-building-ontology) és egy ontológia-t az [intelligens városokhoz](#smart-cities-ontology), amelyek közös terepet biztosítanak az ezekben az iparágakban található szabványok alapján a modellezés elkerüléséhez. 

## <a name="realestatecore-smart-building-ontology"></a>RealEstateCore Smart Building ontológia

*Itt találja az ontológia-t: [**Digital Twins Definition Language-based RealEstateCore ontológia intelligens épületekhez**](https://github.com/Azure/opendigitaltwins-building)*.

A Microsoft a [RealEstateCore](https://www.realestatecore.io/), az ingatlan-tulajdonosok, a szoftvergyártók és a kutatási intézmények svéd konzorciumával együttműködve nyújtja be ezt a nyílt forráskódú DTDL az ingatlan-ipar számára.

Ez az intelligens épületek, az ontológia az intelligens épületek modellezését teszi lehetővé az iparági szabványok (például [tégla-séma](https://brickschema.org/ontology/) vagy a [W3C-építési topológia ontológia](https://w3c-lbd-cg.github.io/bot/index.html)) használatával, hogy elkerülje az újratalálmányt. Az ontológia az ajánlott eljárásokat is tartalmazza a használat és a megfelelő kibővítéshez. 

Ha többet szeretne megtudni az ontológia struktúrájáról és modellezési szabályairól, hogyan használhatja azt, hogyan bővítheti és Hogyan járulhat hozzá, látogasson el az ontológia adattárára a GitHubon: [Azure/opendigitaltwins-Building](https://github.com/Azure/opendigitaltwins-building). 

Ebben a blogbejegyzésben további információkat talál a RealEstateCore és céljaival kapcsolatos partneri kapcsolatokról, valamint a következő videóhoz: [RealEstateCore, a digitális Twins-hoz készült intelligens épület, amely már elérhető](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794).

## <a name="smart-cities-ontology"></a>Intelligens városok – ontológia

*Keresse meg az ontológia itt: [**Digital Twins Definition Language (DTDL) ontológia for intelligens Cities**](https://github.com/Azure/opendigitaltwins-smartcities)*.

A Microsoft a [nyílt agilis intelligens városokkal (OASC)](https://oascities.org/) és a [Szabó](https://sirus.be/) együttműködve DTDL-alapú ontológia-t biztosít intelligens városok számára, amely az [ETSI CIM NGSI-ld-](https://www.etsi.org/committee/cim)vel kezdődik. Az ETSI NGSI-LD mellett a Saref4City, a CityGML, az ISO és mások is értékeltek.

Az ontológia jelenlegi kiadása a modellek kezdeti készletére koncentrál. Az ontológia szerzője üdvözli Önt, hogy hozzájáruljon a használati esetek kibővítéséhez, valamint a meglévő modellek fejlesztéséhez. 

Ha többet szeretne megtudni az ontológia-ról, a használatáról és a közreműködés módjáról, látogasson el az ontológia adattárára a GitHubon: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

További információk az intelligens városok együttműködéséről és megközelítéséről ebben a blogbejegyzésben és a kapcsolódó videóban: az [intelligens városok, a digitális ikrek számára készült ontológia](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Következő lépések

* További információ az iparági szabványnak megfelelő ontológiákat kibővítéséről a specifikációk teljesítése érdekében: [*fogalmak: az iparági ontológiákat kiterjesztése*](concepts-ontologies-extend.md).

* Vagy folytassa a modellek fejlesztésének elérési útját a ontológiákat alapján: az [*ontológia-stratégiák használatával a modell fejlesztési útvonalán*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path).