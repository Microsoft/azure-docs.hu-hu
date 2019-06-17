---
title: Mi az a BLEU pontszám? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: BLEU egy mérték automatikus fordítását és a egy vagy több ember által létrehozott referencia fordítását egy forrás mondatban közötti különbségeket. A BLEU algoritmus az egymást követő kifejezések, a hivatkozás fordítás talál, és egyezést, súlyozott módon megszámolja a automatikus fordítás egymást követő kifejezések hasonlítja össze.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-pawal
ms.openlocfilehash: 08be79dde62a1d624fa59816cf855e702245b622
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66385167"
---
# <a name="what-is-a-bleu-score"></a>Mi az a BLEU pontszám?

[(Kétnyelvű értékelési Understudy) BLEU](https://en.wikipedia.org/wiki/BLEU) mérésére szolgál egy automatikus fordítás a és a egy vagy több ember által létrehozott referencia fordításának forrás egy mondatban közötti különbségeket.

## <a name="scoring-process"></a>Kiértékelés folyamatban

A BLEU algoritmus az egymást követő kifejezések, a hivatkozás fordítás talál, és egyezést, súlyozott módon megszámolja a automatikus fordítás egymást követő kifejezések hasonlítja össze. Az egyezések elhelyezése független. Egyezés nagyobb mértékben azt jelzi, hogy a referencia-fordítási és a magasabb pontszámot hasonlóság nagyobb fokú. Képminőséget és nyelvtani helyességét nem veszik figyelembe.

## <a name="how-bleu-works"></a>BLEU működését?

A BLEU erőssége, hogy azt utal. jól emberi ítélet keresztül egy teszt forrásgyűjteményébe ki egyes mondathoz ítélet hibák átlagolás, helyett próbál megtervezi a pontos emberi határozat minden mondat helyett szerepel.

A pontszámok BLEU szélesebb körű vita van [Itt](https://youtu.be/-UqDljMymMg).

BLEU eredmények erősen függ, a tartomány, a képzés és finomhangolása az adatokat, a Tesztadatok konzisztenciájának technológiai spektrumunk kihasználtságának növelését, és hogy mennyi adatot elérhető betanításához. A modellek rendelkezik betanítva keskeny tartományban, és a betanítási adatok konzisztensek legyenek a tesztadatok, ha várhatóan magas BLEU pontszámot.

>[!NOTE]
>BLEU pontszámok összehasonlítását csak akkor indokolható BLEU eredmények teszt ugyanezek, ugyanarra a nyelv két és ugyanazt a fő Célkiszolgáló motort képest. Egy másik teszt készletből BLEU pontszámot kötött kell lennie.
