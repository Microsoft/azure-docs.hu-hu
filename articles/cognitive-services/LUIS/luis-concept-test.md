---
title: A LUIS-alkalmazás tesztelése
titleSuffix: Azure Cognitive Services
description: A tesztelés a hosszúságú kimondott szöveg és a LUIS által elismert szándékok és entitások válaszának beszerzésének folyamata.
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: b33f765e936d7c0db301a5b2fcf38ccaf137f771
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787553"
---
# <a name="testing-example-utterances-in-luis"></a>Tesztelési példa a LUIS hosszúságú kimondott szöveg

A tesztelés a hosszúságú kimondott szöveg és a LUIS által elismert szándékok és entitások válaszának beszerzésének folyamata. 

A LUIS-t interaktívan, egy Kimondás útján, vagy hosszúságú kimondott szöveg-készletet is megadhat. A tesztelés során összehasonlíthatja az aktuális aktív modell előrejelzési válaszát a közzétett modell előrejelzési válaszával. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Mi a tesztelési pontszám?
Az előrejelzési [pontszámok](luis-concept-prediction-score.md) részletes ismertetését lásd: az előrejelzési pontszám fogalmai.

## <a name="interactive-testing"></a>Interaktív tesztelés
Az interaktív tesztelés a LUIS-portál **tesztelési** paneljén végezhető el. Megadhat egy teljes értéket, amelyből megtudhatja, hogyan azonosíthatók és hogyan lesznek azonosítva a szándékok és az entitások. Ha a LUIS nem Jósolja meg a szándékokat és az entitásokat a tesztelési panelen való Kimondás során, a rendszer új kifejezésként másolja azt a **leképezési** oldalra. Ezután címkézze fel az entitások részeit, és végezze el a LUIS betanítását. 

## <a name="batch-testing"></a>Kötegelt tesztelés
Ha egyszerre több Kimondás van tesztelve, tekintse meg a [kötegelt tesztelést](./luis-how-to-batch-test.md) .

## <a name="endpoint-testing"></a>Végpont tesztelése
A [végpontot](luis-glossary.md#endpoint) az alkalmazás legfeljebb két verzióját használva tesztelheti. Ha az alkalmazás fő vagy élő verzióját **éles** végpontként állítja be, adjon hozzá egy második verziót az **átmeneti** végponthoz. Ez a megközelítés három változatot biztosít: a jelenlegi modellt a [Luis](luis-reference-regions.md) webhely teszt paneljén, valamint a két különböző végponton található két verziót. 

Az összes végponti teszt a használati kvóta felé mutat. 

## <a name="do-not-log-tests"></a>Tesztek naplózása
Ha tesztet végez egy végponton, és nem szeretné, hogy a rendszer naplózza a teljes naplózást, ne felejtse el használni a `logging=false` lekérdezési karakterlánc konfigurációját.

## <a name="where-to-find-utterances"></a>Hol található a hosszúságú kimondott szöveg
LUIS az összes naplózott hosszúságú kimondott szöveg tárolja a lekérdezési naplóban, letölthető a LUIS portálon az **alkalmazások** listája lapról, valamint a Luis [authoring API](https://go.microsoft.com/fwlink/?linkid=2092087)-kkal. 

Az hosszúságú kimondott szöveg LUIS nem biztos, hogy szerepel a [Luis](luis-reference-regions.md) webhely **[végpont hosszúságú kimondott szöveg áttekintése](luis-how-to-review-endpoint-utterances.md)** lapján. 

## <a name="remember-to-train"></a>Ne feledje a betanítást
A modell módosítása után ne felejtse el [betanítani](luis-how-to-train.md) a Luis-t. A LUIS alkalmazás módosításait a rendszer nem vizsgálja az alkalmazás betanítása előtt. 

## <a name="best-practices"></a>Ajánlott eljárások
Ismerje meg az [ajánlott eljárásokat](luis-concept-best-practices.md).

## <a name="next-steps"></a>Következő lépések

* További információ a hosszúságú kimondott szöveg [teszteléséről](luis-interactive-test.md) .