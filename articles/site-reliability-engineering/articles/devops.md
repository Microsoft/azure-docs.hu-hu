---
title: 'Gyakori kérdések: SRE és DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'Gyakori kérdések: a SRE és a DevOps közötti kapcsolat megismerése'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 62872443c386c45d9821053dc901ad8e0055b74f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674276"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Gyakori kérdések: mi a kapcsolat a SRE és a DevOps között?

A site megbízhatósági mérnöki és DevOps közötti kapcsolat a következő gyakori kérdésekre épül fel, többek között a "Miben azonosak? Mi a különbség közöttük? Mi is a szervezetünk? ". Ez a cikk megpróbál megosztani egy olyan választ, amelyet a SRE és a DevOps-Közösségek is felkínáltak, hogy megismerje a kapcsolat megértését.

## <a name="how-are-they-the-same"></a>Miben egyeznek?

A SRE és a DevOps egyaránt modern operatív eljárások, amelyeket a rendszer a következő kihívásokra reagálva hozott létre és fejlesztett ki:

- az éles környezetek és a fejlesztési folyamatok egyre összetettebbek
- ezen környezetek üzleti függőségének növekedése
- a munkaerő lineáris méretezésének nehézsége a környezetek méretével együtt
- a működési stabilitás megőrzése mellett még gyorsabban kell mozogni

Mindkét működési gyakorlat olyan témákat érint, amelyek kritikus fontosságúak ezeknek a kihívásoknak a kezeléséhez, például a monitorozáshoz, a megfigyeléshez, az automatizáláshoz, a dokumentációhoz és a kollaboratív szoftverfejlesztés eszközeihez.

A SRE és a DevOps között jelentős átfedés mutatkozik az eszközök és a munkaterületek között. Mivel _a hely megbízhatósági munkafüzete_ ezt teszi, "a sre ugyanúgy érzi magát, mint a DevOps, de némileg eltérő okokból."

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>A két működési gyakorlat összehasonlításának három különböző módja

A SRE és a DevOps közötti hasonlóságok egyértelműek. Ahol nagyon érdekes, hogy miben különbözik a két különbség. Itt háromféle módon gondolkodhat a kapcsolaton, és így némi árnyalatot is megadhat a kérdéshez. Előfordulhat, hogy nem fogadja el ezeket a válaszokat, de ezek mindegyike jó kiindulási helyet biztosít a megbeszélésekhez.

### <a name="class-sre-implements-interface-devops"></a>"a Class SRE megvalósítja a DevOps felületet"

_A hely megbízhatósági munkafüzete_ (az [erőforrás-könyv listában](../resources/books.md)szerepel) a sre és a DevOps az első fejezetében tárgyalja. Ez a fejezet a "class SRE implementálja az interface DevOps" kifejezést használja alcímként. Ez azt jelenti, hogy (a fejlesztőknek szánt kifejezés használatával) a SRE a DevOps filozófia adott implementációjának tekinthető. Ahogy a fejezet kimutatta, "a DevOps viszonylag csendesen mutatja, hogyan futtathat műveleteket részletes szinten", míg a SRE lényegesen részletesebben szemlélteti a gyakorlatát. Így az egyik lehetséges válasz arra a kérdésre, hogy a két kapcsolat SRE lehet a DevOps számos lehetséges implementációjának.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>A SRE a megbízhatóság, mivel a DevOps kézbesítés

Ez az összehasonlítás egy kis muddied, mert több definíció is létezik a SRE és a DevOps, de ez továbbra is hasznos lehet. A következő kérdéssel kezdődik: "Ha az egyes üzemeltetési gyakorlatokat egy vagy két olyan kifejezésre kellett volna felszűrni, amely a fő aggodalmat tükrözi, mi lenne?"

Ha ezt a SRE-definíciót használjuk a [site megbízhatósági központ](../index.yml):

> A Site Reliability Engineering olyan szoftvermérnöki szemlélet, amelynek a célja, hogy fenntarthatóan biztosítsa egy szervezet rendszereinek, szolgáltatásainak és termékeinek megfelelő szintű megbízhatóságát.

ezt követően könnyű lenne kimondani, hogy a SRE szó "megbízhatóság". Ha a név közepén van, akkor is remek bizonyítékot nyújt erre a jogcímre.

Ha ezt a DevOps-definíciót használjuk az [Azure DevOps Resource Centerből](/azure/devops/learn/):

> A DevOps emberek, folyamatok és termékek egyesülése a végfelhasználók számára történő folyamatos értékközvetítésért.

Ezután a DevOps hasonló lepárlása lehet "kézbesítés".

Ezért a "SRE a megbízhatóság, mivel a DevOps a kézbesítés".

### <a name="direction-of-attention"></a>A figyelem iránya

Ez a válasz egy Thomas Limoncelli által az [erőforrás-könyv listájában](../resources/books.md)említett _sre_ -könyvbe való hozzájárulás alapján vagy némileg körülírva van. Tudomásul veszi, hogy a DevOps-mérnökök nagy mértékben a szoftverfejlesztői életciklus-folyamatra összpontosítanak, és alkalmanként üzemi műveleti feladatokat végeznek, miközben a SREs az alkalmi SDLC-feldolgozási feladatokra összpontosítanak.

De még ennél is fontosabb, hogy egy olyan diagramot rajzol, amely az egyik oldalon elindítja a szoftverfejlesztés folyamatát, és a termelési műveletek a másikon működnek. A kettő összekapcsolja a szokásos folyamattal, amely a kód egy fejlesztőtől való elvégzésére szolgál, a kívánt számú teszten és fázison keresztül, majd a kódot éles környezetbe helyezi.

A Limoncelli megállapítja, hogy a DevOps-mérnökök elkezdik a fejlesztési környezetet, és automatizálják a lépéseket a gyártás irányába. Ha elkészült, térjen vissza a szűk keresztmetszetek optimalizálásához.

Ezzel szemben a SREs koncentrálhat a termelési műveletekre, és a végső eredmény javítása érdekében a folyamat mélyét is elérheti (ami alapvetően az ellenkező irányba működik).

Ez a különbség a SRE és a DevOps fókusz irányában, ami segíthet a különbségtételben.

## <a name="coexistence-in-the-same-organization"></a>Párhuzamos létezés ugyanabban a szervezetben

Az utolsó kérdés, amit érdemes megválaszolni: "a SRE és a DevOps is ugyanabban a szervezetben?"

A kérdésre adott válasz egy hangsúlyos "igen!".

Reméljük, hogy az előző válaszok a két működési gyakorlat átfedését és, ha nem átfedésben vannak, hogyan lehet őket kiegészíteni a fókuszban. A létrehozott DevOps gyakorlattal rendelkező szervezetek kis léptékben kísérletezhet a SRE gyakorlattal (például a SLIs és a slo kipróbálásával), anélkül, hogy el kellene vállalniuk a SRE pozíciók vagy csapatok létrehozásához. Ez egy viszonylag gyakori SRE-bevezetési minta.

## <a name="next-steps"></a>Következő lépések

Szeretne többet megtudni a site megbízhatóság mérnöki vagy DevOps? Tekintse meg a [site megbízhatósági központ](../index.yml) és az [Azure DevOps Resource centert](/azure/devops/learn/).