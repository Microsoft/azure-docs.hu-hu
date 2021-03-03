---
title: Névjegy
description: Bevezetés az Azure távoli Renderelésbe
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: 6001fff54b86b7c18684092aad6c7fdf64f81990
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729946"
---
# <a name="about-azure-remote-rendering"></a>Az Azure Remote Rendering ismertetése

Az *Azure távoli renderelés* (ARR) egy olyan szolgáltatás, amely lehetővé teszi a magas színvonalú, interaktív 3D-tartalmak megjelenítését a felhőben, és valós időben továbbítja azokat az eszközökre, például a 2. HoloLens.

![Diagram, amely egy példát mutat be a kiváló minőségű, interaktív 3D-s Automobil motorra.](../media/arr-engine.png)

A nem lekötött eszközök korlátozott számítási teljesítménnyel rendelkeznek a komplex modellek rendereléséhez. Számos alkalmazás esetében elfogadhatatlan lenne, azonban a vizualizációs hűség bármilyen módon történő csökkentése. A következő képernyőkép összehasonlítja a modellt teljes részletességgel egy olyan modellel, amelyet a közös tartalom-létrehozási eszköz használatával megtizedeltek:

![Mintamodell](./media/engine-model-decimated.png)

A csökkentett modell körülbelül 200 000 háromszögből áll (beleértve a részletes belső részeket is), és az eredeti modellben több mint 18 000 000 háromszöget tartalmaz.

A *távoli renderelés* megoldja ezt a problémát azáltal, hogy áthelyezi a renderelési munkafolyamatot a felhőben lévő magas szintű GPU-k felé. A felhőben üzemeltetett grafikus motor rendereli a képet, kódolja a videót, és továbbítja azokat a célként megadott eszköznek.

## <a name="hybrid-rendering"></a>Hibrid renderelés

A legtöbb alkalmazásban nem elegendő egyetlen összetett modell megjelenítésére. Emellett egyéni felhasználói felületre is szüksége lesz a felhasználónak nyújtott funkciók biztosításához. Az Azure távoli renderelés nem kényszeríti a dedikált felhasználói felületi keretrendszer használatát, hanem támogatja a *hibrid renderelést*. Ez azt jelenti, hogy az eszközön elemeket lehet megjeleníteni az előnyben részesített módszer, például a [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html)használatával.

A keret végén az Azure távoli renderelés automatikusan egyesíti a helyileg renderelt tartalmat a távoli képpel. Azt is megteheti, hogy a megfelelő elzáródással rendelkezik.

## <a name="multi-gpu-rendering"></a>Több GPU renderelése

Néhány modell túl összetett ahhoz, hogy interaktív kereteket lehessen megjeleníteni, még egy magas szintű GPU esetében is. Különösen az ipari vizualizációkban ez egy gyakori probléma. A korlátok további leküldéséhez az Azure távoli renderelés több GPU-ra is terjesztheti a munkaterhelést. Az eredményeket egyetlen rendszerképbe egyesítjük, így a folyamat teljes mértékben átlátszóvá válik a felhasználó számára.

## <a name="high-level-architecture"></a>Magas szintű architektúra

Ez az ábra a távoli renderelési architektúrát szemlélteti:

![Architektúra](./media/arr-high-level-architecture.png)

A képek generálásának teljes ciklusa a következő lépésekből áll:

1. Ügyféloldali: keret beállítása
    1. A kód: felhasználói bevitel feldolgozva, a Scene Graph frissül
    1. ARR-kód: a Scene Graph frissítései és az előre jelzett Head póz a kiszolgálóra kerül.
1. Kiszolgálóoldali: távoli renderelés
    1. A renderelési motor a rendelkezésre álló GPU-k között terjeszti a renderelést
    1. Több GPU-ból származó kimenet egyetlen rendszerképbe kerül
    1. A képet Video streamként kódolja a rendszer, és visszaküldi az ügyfélnek
1. Ügyféloldali: véglegesítés
    1. A kód: nem kötelező helyi tartalom (UI, jelölők,...) megjelenítése
    1. ARR-kód: a "jelen", a helyileg megjelenített tartalom automatikusan egyesítve lesz a videó streamtel

A hálózati késés a fő probléma. A kérések küldése és az eredmény fogadása között elvárható idő általában túl hosszú az interaktív keret díjszabásához képest. Ezért a több keret is lehet egy időben.

## <a name="next-steps"></a>Következő lépések

* [Rendszerkövetelmények](system-requirements.md)
* [Gyors útmutató: modell megjelenítése egységgel](../quickstarts/render-model.md)
