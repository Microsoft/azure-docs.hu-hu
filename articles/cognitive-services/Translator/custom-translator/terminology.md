---
title: Terminológia – egyéni fordító
titleSuffix: Azure Cognitive Services
description: Az egyéni fordító cikkeiben használt kifejezések listája.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: reference
ms.openlocfilehash: 4461f584e365a5d47e7ceee942e33bc8b101b2d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657822"
---
# <a name="custom-translator-terminology"></a>Egyéni fordítói terminológia

A következő táblázat felsorolja azokat a feltételeket, amelyeket az [Egyéni fordítóval](https://portal.customtranslator.azure.ai)való munka során talál.

| Szó vagy kifejezés|Definíció|
|------------------|-----------|
| Forrás nyelve | A forrás nyelve az a kezdő nyelv, amelyet át szeretne alakítani egy másik nyelvre (a "cél").|
| Cél nyelve| A célként megadott nyelv az a nyelv, amelyet a gépi fordításnak meg kell adnia, miután megkapta a forrás nyelvét. |
| Egynyelvű fájl | Egy egynyelvű fájl egyetlen nyelvet tartalmaz, amely nem párosítható más nyelvű fájlokkal. |
| Párhuzamos fájlok | A párhuzamos fájlok a megfelelő szöveggel rendelkező két fájl kombinációját jelentik. Az egyik fájlban a forrás nyelve szerepel. A másik nyelv is a cél.|
| Mondat igazítása| A párhuzamos adatkészlethez igazított mondatoknak kell tartoznia, amelyek mindkét nyelven azonos szövegnek felelnek meg. A forrás párhuzamos fájlban például az első mondatnak elvileg a cél párhuzamos fájl első mondatához kell rendelnie.|
| Igazított szöveg | A fájlok érvényesítésének egyik legfontosabb lépése a mondatok egymáshoz igazítása a párhuzamos dokumentumokban. A dolgok különböző nyelveken eltérően vannak kifejezve. Más nyelveken is különböző Word-utasítások szerepelnek. Ennek a lépésnek a feladata, hogy a mondatokat ugyanazzal a tartalommal igazítsa, hogy azok felhasználhatók legyenek a betanításhoz. Az alacsony mondatok igazítása azt jelzi, hogy a fájlok egyike vagy mindkét fájlja hibás lehet. |
| Szó betörése/megszüntetése | A sortörés a szavak közötti határok megjelölésének funkciója. Számos írási rendszer egy szóközzel jelöli a szavak közötti határt. A sortörések leválasztása az előző lépésben szereplő szavak között esetlegesen beszúrt látható jelölők eltávolítását jelenti. |
| Határolójelek   | Az elválasztó karakterek a mondatok szegmensekre osztására, illetve a mondatok közötti árrés kibontására szolgálnak. Az angol szóközekben például a szavak, a kettőspontok és a pontosvesszővel elválasztott mondatok és időszakok körülhatárolása kifejezéseket. |
| Fájlok betanítása | A program egy képzési fájlt használ a gépi fordítási rendszer megtanítására, hogyan képezhető le az egyik nyelvről (a forrásról) a célnyelv (a cél). Minél több adat van, annál jobb lesz a rendszer végrehajtása. |
| Fájlok hangolása | Ezeket a fájlokat gyakran véletlenszerűen származtatják a betanítási készletből (ha nem választ egy hangolási készletet). A mondatokat a rendszer kijelöli, és a rendszer finomhangolásához használja, és gondoskodik arról, hogy megfelelően működjön. Ha szeretne létrehozni egy általános célú fordítási modellt, és létre szeretné hozni a saját finomhangolási fájljait, akkor ügyeljen rá, hogy a mondatok véletlenszerű halmaza legyen a különböző tartományokban |
| Fájlok tesztelése| Ezek a fájlok gyakran származtatott fájlok, amelyek véletlenszerűen vannak kiválasztva a betanítási készletből (ha nem választ be egyetlen tesztelési készletet sem). Ezen mondatok célja, hogy kiértékelje a fordítási modell pontosságát. Mivel ezek a mondatok a rendszer pontos lefordítását kívánják biztosítani, érdemes lehet létrehozni egy tesztelési készletet, és fel kell tölteni a fordítóba. Így biztosítható, hogy ezek a mondatok a rendszer kiértékelésében (a BLEU-pontszám létrehozásakor) legyenek felhasználva.   |
| Kombinált fájl   | Egy fájltípus, amelyben a forrás-és a lefordított mondatok ugyanabban a fájlban találhatók. Támogatott fájlformátumok (. TMX,. XLIFF,. XLF,. ICI,. xlsx). |
| Archív fájl | Más fájlokat tartalmazó fájl. Támogatott fájlformátumok (zip, GZ, tgz).  |
| BLEU-pontszám   | A [Bleu](what-is-bleu-score.md) az iparági szabványnak megfelelő módszer a fordítási modell pontosságának kiértékeléséhez. Bár más kiértékelési módszerek is léteznek, a Microsoft Translator a BLEU metódust használja a projekt tulajdonosainak pontosságának jelentéséhez.
