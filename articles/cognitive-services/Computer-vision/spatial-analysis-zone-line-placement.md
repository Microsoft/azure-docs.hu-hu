---
title: Térbeli elemzési zóna és vonal elhelyezése
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan állíthat be zónákat és vonalakat térbeli elemzéssel
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936221"
---
# <a name="zone-and-line-placement-guide"></a>A zóna és a vonal elhelyezési útmutatója

Ez a cikk azt ismerteti, hogyan határozható meg a zónák és a vonalak a térbeli elemzési műveletekhez, hogy pontos elemzést lehessen elérni a népek mozgásának helyéről. Ez az összes műveletre vonatkozik. 

A zónák és a sorok a JSON SPACEANALYSIS_CONFIG paraméterrel vannak meghatározva. További információt a [térbeli elemzési műveletek](spatial-analysis-operations.md) című cikkben talál.

## <a name="guidelines-for-drawing-zones"></a>A rajzolási zónákra vonatkozó irányelvek

Ne feledje, hogy minden hely eltérő; az igényektől függően frissítenie kell a pozíciót vagy a méretet.

Ha szeretné megtekinteni a kamera nézet egy adott szakaszát, hozza létre a legnagyobb zónát, amely az Ön számára érdekli, de nem tartalmazza azokat a területeket, amelyeket Ön még nem érdekli. Ezzel növeli az összegyűjtött adatok pontosságát, és meggátolja a hamis pozitív értéket a nyomon követni kívánt területekről. Ügyeljen a sokszög sarkainak elhelyezésére, és ügyeljen rá, hogy ne a nyomon követni kívánt területen kívül legyenek.  

### <a name="example-of-a-well-shaped-zone"></a>Példa jól formázott zónára

A zónának elég nagynak kell lennie ahhoz, hogy három ember álljon rendelkezésre az egyes élek mentén, és az érdeklődési területre koncentráljon. A térbeli elemzés azonosítja azokat a személyeket, akiknek a lábát elhelyezik a zónában, így ha a 2D-s képen a zónák szerepelnek, Képzelje el a zónát a padlón.

![Jól formázott zóna](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>Példák a nem megfelelően formázott zónákra

A következő példák a rosszul formázott zónákat mutatják be. Ezekben a példákban a fontos terület az a hely, ahol a *játék ideje* látható.

**A zóna nem a padlón található.**

![Nem megfelelően formázott zóna](./media/spatial-analysis/zone-not-on-floor.png) 

**A zóna túl kicsi.**

![a zóna túl kicsi](./media/spatial-analysis/zone-too-small.png)

**A zóna nem rögzíti teljesen a kijelző körüli területet.**

![a zóna nem rögzíti teljes mértékben a végpontok körét](./media/spatial-analysis/zone-bad-capture.png)

**A zóna túl van közelebb a kamera lemezképének széléhez, és nem rögzíti a jobb oldali megjelenítést.**

![a zóna túl van közelebb a kamera lemezképének széléhez, és nem rögzíti a jobb oldali megjelenítést](./media/spatial-analysis/zone-edge.png)

**A zóna részlegesen le van tiltva a polcon, így a személyek és a padló nem teljesen látható.**

![a zóna részben blokkolva van, így az emberek nem teljesen láthatók](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>Példa jól formázott sorra

A sornak elég hosszúnak kell lennie a teljes bejárat befogadásához. A térbeli elemzés azonosítja azokat a személyeket, akiknek a lábai átlépik a vonalat, így a 2D-s ábrán lévő vonalak rajzolásakor úgy képzeli el őket, mintha a padlón fekszenek. 

Ha lehetséges, a sort a tényleges bejáratnál szélesebbre kell kiterjeszteni. Ha ez nem eredményez további átkeléseket (például az alábbi képen, amikor a sor egy falhoz esik), akkor kiterjesztheti.

![Jól formázott vonal](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>Példák a nem megfelelően formázott sorokra

Az alábbi példákban a rosszul definiált sorok láthatók.

**A sor nem fedi le a teljes belépési módot a padlón.**

![A sor nem fedi le a teljes belépési módot a padlón](./media/spatial-analysis/zone-line-bad-coverage.png)

**A sor túl magas, és nem fedi le az ajtó teljes egészét.**

![A sor túl magas, és nem fedi le az ajtó teljes egészét](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>Következő lépések

* [Felhasználók üzembe helyezése webes alkalmazásokban](spatial-analysis-web-app.md)
* [Térbeli elemzési műveletek konfigurálása](./spatial-analysis-operations.md)
* [Naplózás és hibaelhárítás](spatial-analysis-logging.md)
* [Kamera elhelyezése – útmutató](spatial-analysis-camera-placement.md)
