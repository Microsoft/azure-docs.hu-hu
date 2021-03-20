---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011720"
---
Az Azure-fájlmegosztás adatainak adatvesztéssel vagy sérüléssel szembeni védelme érdekében az összes Azure-fájlmegosztás több példányt is tárol az egyes fájlokról az írásuk során. A számítási feladatok követelményeitől függően kiválaszthatja a redundancia további mértékeit is. A Azure Files jelenleg a következő adatredundancia-beállításokat támogatja:

- **Helyileg redundáns**: a helyileg redundáns tárolás, amely gyakran LRS néven is ismert, azt jelenti, hogy minden fájl háromszor van tárolva egy Azure Storage-fürtön belül. Ez védelmet biztosít a hardveres hibák, például a hibás lemezmeghajtók miatti adatvesztés elleni védelemben.
- **Felesleges zóna**: a zóna redundáns tárolója (más néven ZRS) azt jelenti, hogy minden fájl három alkalommal van tárolva három különböző Azure Storage-fürtben. A helyileg redundáns tároláshoz hasonlóan a Zone redundancia is három példányban tárolja az egyes fájlokat, azonban ezek a másolatok fizikailag el vannak különítve három különböző Azure-beli *rendelkezésre állási zónában* található tároló fürtben. A rendelkezésre állási zónák egy Azure-régióban található egyedi fizikai helyek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. A rendszer nem fogadja el a tárhelyre való írást mindaddig, amíg az összes rendelkezésre álló zónában be nem írja a Storage-fürtöket. 
- **Geo-redundáns**: a Geo-redundáns tárolás (más néven GRS) hasonló a helyileg redundáns tároláshoz, mivel a fájlok háromszor tárolódnak az elsődleges régióban lévő Azure Storage-fürtön belül. A rendszer az összes írást aszinkron módon replikálja egy Microsoft által definiált másodlagos régióba. A Geo-redundáns tárolás 6 másolatot biztosít az adateloszlásról két Azure-régió között. Egy olyan súlyos katasztrófa esetén, mint az Azure-régió természetes katasztrófák vagy más hasonló események miatti végleges elvesztése, a Microsoft feladatátvételt hajt végre, így a másodlagos működés elsődleges, és minden műveletet kiszolgál. Mivel a replikáció az elsődleges és a másodlagos régió között aszinkron módon történik, súlyos katasztrófa esetén a másodlagos régióba még nem replikált adatmennyiség elvész. Elvégezheti a Geo-redundáns Storage-fiókok manuális feladatátvételét is.
- A **geo-zóna redundáns**: a Geo-Zone redundáns tárolás, amely gyakran GZRS néven is ismert, például a zóna redundáns tárolója, abban az esetben, ha a fájl három alkalommal van tárolva az elsődleges régió három különböző tárolási fürtjében. A rendszer az összes írást aszinkron módon replikálja egy Microsoft által definiált másodlagos régióba. A földrajzi zóna – redundáns tárolás feladatátvételi folyamata ugyanúgy működik, mint a földrajzilag redundáns tároláshoz.

A standard szintű Azure-fájlmegosztás támogatja mind a négy redundancia-típust, míg a prémium szintű Azure-fájlmegosztás csak a helyileg redundáns és a zóna redundáns tárolást támogatja.

Az általános célú 2-es verziójú (GPv2) Storage-fiókok két további, a Azure Files által nem támogatott redundancia-beállítást biztosítanak: olvassa el az elérhető geo-redundáns tárolást, amely gyakran az RA-GRS, valamint az elérhető földrajzi zóna-redundáns tárolás, más néven RA-GZRS. Ezekkel a beállításokkal kiépítheti az Azure-fájlmegosztást a Storage-fiókokban, azonban Azure Files nem támogatja a másodlagos régióból való olvasást. Az olvasási hozzáférésű geo-vagy geo-zóna redundáns tárolási fiókokban üzembe helyezett Azure-fájlmegosztás a Geo-redundáns vagy a Geo-zóna redundáns tárterületként lesz kiszámlázva.