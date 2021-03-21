---
title: Előre elkészített modellek – LUIS
titleSuffix: Azure Cognitive Services
description: Az előre elkészített modellek tartományok, leképezések, hosszúságú kimondott szöveg és entitások biztosítására szolgálnak. Elindíthatja az alkalmazást egy előre elkészített tartománnyal, vagy hozzáadhat egy kapcsolódó tartományt az alkalmazáshoz később.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 6642e59c2957b298d54bc587853752b9fce74686
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95019143"
---
# <a name="prebuilt-models"></a>Előre összeállított modellek

Az előre elkészített modellek tartományok, leképezések, hosszúságú kimondott szöveg és entitások biztosítására szolgálnak. Elindíthatja az alkalmazást egy előre elkészített modellel, vagy hozzáadhat egy megfelelő modellt az alkalmazáshoz később. 

## <a name="types-of-prebuilt-models"></a>Az előre elkészített modellek típusai

A LUIS három típusú előre elkészített modellt biztosít. Minden modell bármikor hozzáadható az alkalmazáshoz. 

|Modell típusa|Beágyazások|
|--|--|
|[Tartomány](luis-reference-prebuilt-domains.md)|Szándékok, hosszúságú kimondott szöveg, entitások|
|Leképezések|Szándékok, hosszúságú kimondott szöveg|
|[Entitások](luis-reference-prebuilt-entities.md)|Csak entitások| 

## <a name="prebuilt-domains"></a>Előre összeállított tartományok

Language Understanding (LUIS) előre *elkészített tartományokat* biztosít, amelyek előre betanított [minták és](luis-how-to-add-intents.md) [entitások](luis-concept-entity-types.md) , amelyek együttesen működnek a tartományokban vagy az ügyfélalkalmazások általános csoportjaiban. 

Az előre elkészített tartományokat betanítják, és készen állnak a LUIS-alkalmazásba való felvételre. Az előre elkészített tartományhoz tartozó szándékok és entitások teljes mértékben testreszabhatók az alkalmazáshoz való hozzáadásuk után. 

> [!TIP]
> Az előre elkészített tartományban található szándékok és entitások együtt működnek a legjobban. Jobb megoldás, ha lehetséges, az azonos tartományhoz tartozó leképezéseket és entitásokat érdemes kombinálni.
> A segédprogramok előre elkészített tartománya olyan leképezéseket tartalmaz, amelyek bármely tartományban testreszabhatók. Hozzáadhatja például az alkalmazáshoz `Utilities.Repeat` , és betaníthatja, hogy a felhasználók milyen műveleteket végezhetnek el az alkalmazásban. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Előre elkészített tartomány leképezése viselkedésének módosítása

Előfordulhat, hogy egy előre elkészített tartomány olyan szándékot tartalmaz, amely hasonló a LUIS-alkalmazásban használni kívánt szándékhoz, de azt szeretné, hogy másképp viselkedjen. A **helyek** előre összeépített tartománya például arra `MakeReservation` törekszik, hogy egy éttermi foglalást biztosítson, de azt szeretné, hogy az alkalmazás ezt a szándékot használja a szállodai foglalások elvégzésére. Ebben az esetben módosíthatja a szándék viselkedését úgy, hogy hozzáad egy példát a hosszúságú kimondott szöveg a Szállásfoglaláshoz, majd újratanítja az alkalmazást. 

Az előre elkészített tartományokról az [előre elkészített tartományok című témakörben](./luis-reference-prebuilt-domains.md)talál teljes listát.

## <a name="prebuilt-intents"></a>Előre elkészített leképezések

A LUIS előre összeépített leképezéseket és azok hosszúságú kimondott szöveg biztosítja az előre elkészített tartományokhoz. A leképezések a teljes tartomány hozzáadása nélkül is felvehetők. A szándék hozzáadásával egy szándékot és annak hosszúságú kimondott szöveg adhat hozzá az alkalmazáshoz. A leképezés neve és a teljes lista is módosítható.  

## <a name="prebuilt-entities"></a>Előre összeállított entitások

A LUIS előre összeállított entitásokat tartalmaz az általános típusú információk, például a dátumok, az időpontok, a számok, a mérések és a pénznemek felismeréséhez. Az előre elkészített entitások támogatása a LUIS-alkalmazás kulturális környezetében változik. A LUIS által támogatott előre elkészített entitások teljes listáját, beleértve a kulturális környezet támogatását, az [előre elkészített entitások referenciája](./luis-reference-prebuilt-entities.md)című témakörben talál.

Ha egy előre összeépített entitás szerepel az alkalmazásban, az előrejelzések a közzétett alkalmazásban is szerepelnek. Az előre elkészített entitások viselkedése előre betanítva, és **nem** módosítható. 

> [!NOTE]
> a **beépített. datetime** elavult. A rendszer helyébe a [**beépített. datetimeV2**](luis-reference-prebuilt-datetimev2.md), amely a dátum-és időtartományok felismerését, valamint a kétértelmű dátumok és időpontok jobb felismerését is lehetővé teszi.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [adhat hozzá előre elkészített entitásokat](./howto-add-prebuilt-models.md) az alkalmazáshoz.