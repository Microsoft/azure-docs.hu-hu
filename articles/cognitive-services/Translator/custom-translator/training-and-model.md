---
title: Mi az a képzés és a modell? -Egyéni fordító
titleSuffix: Azure Cognitive Services
description: A modell a rendszer, amely egy adott nyelvi pár fordítását biztosítja. A sikeres képzés eredménye egy modell. A modellek betanításakor a három egymást kölcsönösen kizáró adatkészlet kötelező betanítási adatkészletet, hangolási adatkészletet és tesztelési adatkészletet.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: lajanuar
ms.openlocfilehash: 62a444b66d50579c601077751b0700aa954b34c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897934"
---
# <a name="what-are-trainings-and-models"></a>Mik azok a tréningek és modellek?

A modell a rendszer, amely egy adott nyelvi pár fordítását biztosítja.
A sikeres képzés eredménye egy modell. A modellek betanításakor három kölcsönösen kizárható dokumentum típusa szükséges: képzés, hangolás és tesztelés. A szótári dokumentum típusa is megadható. Tekintse meg a [mondatok igazítását](./sentence-alignment.md#suggested-minimum-number-of-sentences).

Ha a képzések kiszolgálása során csak képzési információk szerepelnek, akkor az egyéni fordító automatikusan összeállítja a hangolási és tesztelési adatfeldolgozást. A mondatok véletlenszerű részhalmazát fogja használni a betanítási dokumentumokból, és kizárja ezeket a mondatokat a betanítási adatokból.

## <a name="training-document-type-for-custom-translator"></a>Az egyéni fordító betanítási dokumentumának típusa

A betanítási csoportba tartozó dokumentumokat az egyéni fordító használja a modell kialakításának alapjául. A betanítás végrehajtása során a dokumentumokban található mondatok vannak igazítva (vagy párosítva). A betanítási dokumentumok összeállítása során a szabadságjogokat is elvégezheti. Belefoglalhatja, hogy az Ön által elkövetett dokumentumok egy modellben legyenek érintő jelentőséggel bírnak. Ismét zárja ki őket egy másikban, és tekintse meg a [Bleu (kétnyelvű kiértékelési tanulmány) pontszámának](what-is-bleu-score.md)hatását. Ha megtartja a hangolási készletet és a test set Constant, nyugodtan kísérletezhet a betanítási csoport összeállításával. Ez a megközelítés hatékony módszer a fordítási rendszer minőségének módosítására.

Több betanítást is futtathat egy projekten belül, és összehasonlíthatja a [Bleu pontszámait](what-is-bleu-score.md) az összes betanítási futtatás során. Ha több tanítást is futtat az összehasonlításhoz, győződjön meg arról, hogy az egyes időpontokban ugyanaz a hangolási/tesztelési érték van megadva. Győződjön meg arról is, hogy az eredményeket manuálisan is megvizsgálja a ["tesztelés"](how-to-view-system-test-results.md) lapon.

## <a name="tuning-document-type-for-custom-translator"></a>A dokumentum típusának finomhangolása egyéni fordítóhoz

A készletben található párhuzamos dokumentumokat az egyéni fordító használja az optimális eredmények érdekében a fordítási rendszer finomhangolásához.

A hangolási adatok a betanítás során használatosak a fordítási rendszer összes paraméterének és súlyozásának optimális értékre való beállításához. Válassza ki gondosan az adatokat: a hangolási adatokat a jövőben lefordítani kívánt dokumentumok tartalmára kell képviselnie. A hangolási adatmennyiség jelentős hatással van a létrehozott fordítások minőségére. A finomhangolás lehetővé teszi, hogy a fordítási rendszer olyan fordításokat biztosítson, amelyek a legközelebb találhatók a hangolási adathalmazban megadott mintákhoz. A hangolási adataiban legfeljebb 2500 mondatnak kell lennie. Az optimális fordítási minőség érdekében javasoljuk, hogy a mondatok legjellemzőbb kiválasztásának kiválasztásával manuálisan válassza ki a hangolási készletet.

A hangolási készlet létrehozásakor válassza a mondatokat, amelyek a lefordítani kívánt jövőbeli mondatok kifejező és reprezentatív hossza. Olyan mondatokat is érdemes kiválasztania, amelyek olyan szavakat és kifejezéseket tartalmaz, amelyeket a jövőbeli fordításokban várható becsült eloszlásban szeretne lefordítani. A gyakorlatban a 7 és 10 közötti mondat a legjobb eredményeket fogja eredményezni, mivel ezek a mondatok elég kontextust tartalmaznak az inflexiós megjelenítéséhez, és olyan kifejezési hosszúságot biztosítanak, amely jelentős, anélkül, hogy túlságosan összetett lenne.

A hangolási készletben használni kívánt mondatok típusának jó leírása a próza: tényleges, Fluent mondatok. Nem táblázatos cellák, nem pedig versek, nem csak a mondatok és a számok a normál nyelven.

Ha manuálisan kiválasztja a hangolási adatait, akkor nem rendelkezhet ugyanazzal a mondattal, mint a képzési és tesztelési adatai. A hangolási adatmennyiség jelentős hatással van a fordítások minőségére – a mondatokat körültekintően válassza ki.

Ha nem biztos abban, hogy mit kell választania a hangolási adataihoz, csak válassza ki a betanítási adatait, és hagyja, hogy az egyéni fordító válassza ki a hangolási adatait. Ha az egyéni fordító automatikusan kiválasztja az adatok finomhangolását, a mondatok véletlenszerű részhalmazát fogja használni a kétnyelvű betanítási dokumentumokból, és kizárja ezeket a mondatokat a betanítási anyagból.

## <a name="testing-dataset-for-custom-translator"></a>Egyéni fordítói adatkészlet tesztelése

A tesztelési csoportba tartozó párhuzamos dokumentumok a BLEU (kétnyelvű kiértékelési tanulmány) pontszámának kiszámítására szolgálnak. Ez a pontszám a fordítási rendszerek minőségét jelzi. Ez a pontszám azt mutatja meg, hogy az ebből a képzésből származó fordítási rendszer által végzett fordítások milyen mértékben egyeznek meg a tesztelési adatkészletben szereplő hivatkozási mondatokkal.

A BLEU pontszám az automatikus fordítás és a hivatkozás fordítása közötti különbözet mérése. Az értéke 0 és 100 között van. A 0 pontszám azt jelzi, hogy a hivatkozás egyetlen szava sem jelenik meg a fordításban. A 100-es pontszám azt jelzi, hogy az automatikus fordítás pontosan megfelel a hivatkozásnak: ugyanaz a szó pontosan ugyanazon a helyen van. A kapott pontszám a test összes mondatának átlaga.

A tesztelési adatként olyan párhuzamos dokumentumoknak kell szerepelniük, amelyekben a célként megadott nyelvi mondatok a forrás-cél párokban a megfelelő nyelvi mondatok legkívánatosabb fordításai. Érdemes lehet ugyanazokat a feltételeket használni, amelyeket a hangolási adatösszeállításhoz használt. A tesztelési adat azonban nem befolyásolja a fordítási rendszerek minőségét. Kizárólag a BLEU pontszám előállítására szolgál.

Tesztelési adatként legfeljebb 2 500 mondatnak kell lennie. Ha a rendszer automatikusan kiválasztja a tesztelési készletet, akkor a mondatok véletlenszerű részhalmazát fogja használni a kétnyelvű betanítási dokumentumokból, és kizárja ezeket a mondatokat a betanítási anyagból.

Megtekintheti a tesztelési csoport egyéni fordításait, és összehasonlíthatja azokat a tesztelési készletben található fordításokkal, ha a modellen belül a teszt lapra navigál.