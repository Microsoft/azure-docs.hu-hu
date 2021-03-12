---
title: Szemantikai rangsorolás
titleSuffix: Azure Cognitive Search
description: A Cognitive Search szemantikai rangsorolási algoritmusát ismerteti.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: a008551ac6f149617feedd01e256b637f83e975d
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235014"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Szemantikai rangsorolás az Azure Cognitive Search

> [!IMPORTANT]
> A szemantikai keresési funkciók nyilvános előzetes verzióban érhetők el, csak az előzetes verziójú REST API. Az előzetes verziójú funkciók a szolgáltatásban is elérhetők, a [kiegészítő használati feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)alatt, és nem garantált, hogy az általánosan elérhető implementációja azonos. További információkért lásd a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

A szemantikai rangsor a lekérdezés végrehajtási folyamatának kiterjesztése, amely javítja a pontosságot, és visszahívja azt a kezdeti eredményhalmaz felső egyezésének átállításával. A szemantikai rangsorolást a legkorszerűbb mély gépi olvasási felolvasási modellek jelentik, amelyek természetes nyelven kifejezett lekérdezésekre vannak kialakítva, ellentétben a kulcsszavak nyelvi megfeleltetésével. Az [alapértelmezett hasonlósági rangsorolási algoritmussal](index-ranking-similarity.md)ellentétben a szemantikai rangsor a szavak kontextusát és jelentését használja a relevancia megállapításához.

## <a name="how-semantic-ranking-works"></a>A szemantikai rangsorolás működése

A szemantikai rangsorolás erőforrás-és időigényes is. A lekérdezési művelet várható késésén belüli feldolgozás befejezéséhez a modell csak az alapértelmezett [hasonlósági rangsorolási algoritmus](index-ranking-similarity.md)által visszaadott első 50-dokumentumot veszi át. A kezdeti rangsor eredményei több mint 50 egyezést tartalmazhatnak, de csak az első 50 lesz a szemantikai sorrendben. 

A szemantikai rangsoroláshoz a modell a gépi olvasást és a tanulást is használja a dokumentumok újbóli kiértékeléséhez, attól függően, hogy milyen jól illeszkedik a lekérdezés szándéka.

1. A szemantikai rangsor minden dokumentum esetében kiértékeli a mezőket a searchFields paraméterben, és összevonja a tartalmakat egy nagy sztringbe.

1. A rendszer ezt követően kivágja a karakterláncot, hogy a teljes hossz ne legyen több, mint 20 000 token. Ha nagyon nagy méretű dokumentumokkal rendelkezik, és a tartalom mező vagy merged_content mező több tartalommal rendelkezik, a rendszer csak az első 20 000 tokent fogja használni.

1. Az 50-dokumentumok mindegyikét egyetlen hosszú karakterlánc jelöli, amely legfeljebb 20 000 tokent képvisel. Ezt a karakterláncot a rendszer elküldi az összefoglaló modellnek. Az összefoglaló modell feliratokat (és válaszokat) hoz létre a gépi olvasási felolvasással, hogy azonosítsa a tartalmat összefoglaló és a kérdés megválaszolásához szükséges részeket. Az összefoglaló modell kimenete egy tovább csökkentett karakterlánc, amely legfeljebb 128 tokenből áll.

1. A kisebb sztring lesz a dokumentum felirata, és a nagyobb karakterláncban található leglényegesebb szakaszokat jelöli. Ezt követően a 50 (vagy kevesebb) feliratot rendezi a rendszer. 

Az elméleti és szemantikai jelentőséget a vektoros ábrázolási és a kifejezési fürtök alkotják. Míg a kulcsszó hasonlósági algoritmusa a lekérdezés bármely kifejezéséhez egyenlő súlyt adhat, a szemantikai modell betanítása megtörtént a egymástól és a kapcsolatok olyan szavak közötti kapcsolatának felismerésére, amelyek egyébként nem kapcsolódnak a felülethez. Ennek eredményeképpen, ha egy lekérdezési sztring azonos fürthöz tartozó kifejezéseket tartalmaz, akkor a kettőt tartalmazó dokumentum magasabb rangú, mint egy nem.

:::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="A környezet vektoros ábrázolása" border="true":::

## <a name="next-steps"></a>Következő lépések

A szemantikai rangsorolást a standard szinteken, adott régiókban kínáljuk. További információért és a regisztrációhoz tekintse meg a [rendelkezésre állást és a díjszabást](semantic-search-overview.md#availability-and-pricing).

Az új lekérdezési típus lehetővé teszi a szemantikai keresés fontossági sorrendjét és reagálási struktúráját. [Hozzon létre egy szemantikai lekérdezést](semantic-how-to-query-request.md) a kezdéshez.

Másik megoldásként tekintse át a következő cikkek valamelyikét a kapcsolódó információkhoz.

+ [Helyesírás-ellenőrzés hozzáadása a lekérdezési feltételekhez](speller-how-to-add.md)
+ [Szemantikai válasz visszaadása](semantic-answers.md)