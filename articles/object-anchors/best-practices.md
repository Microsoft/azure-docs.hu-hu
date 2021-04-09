---
title: Ajánlott eljárások
description: Ajánlott gyakorlati tanácsok a jobb eredmények eléréséhez
author: ariye
ms.author: crtreasu
ms.date: 03/12/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: e287d8305b3fd85fc992417e1563b1e58e6f8424
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463000"
---
# <a name="best-practices"></a>Ajánlott eljárások

Javasoljuk, hogy a lehető legtöbbet próbálja ki a legjobb eredmények elérése érdekében.

## <a name="ingestion"></a>Betöltés

- Vizsgálja meg a fizikai objektumok dimenzióit. Az Azure Object-horgonyok olyan objektumok esetében működnek, amelyek legkisebb dimenziója az ajánlott 1M – 10M tartományba esik.
- A következő részletekért tekintse meg a 3D modellt a szoftverekben, például a [**MeshLab**](https://www.meshlab.net/) .
  - Győződjön meg arról, hogy a 3D-modell háromszög rácsvonalat tartalmaz, és a külső felületen lévő háromszögek kifelé. Ez azt eredményezi, hogy a csúcspontoknak igazodnia kell ahhoz, hogy a normál szabályok a saját orientációjuk szerint kövessék a jobb oldali szabályt.
  - Győződjön meg arról, hogy a 3D modell a megfelelő skálázási egységekkel van megadva a fizikai objektumokra vonatkozóan. Az egységnek a következőket kell tartalmaznia: ***centiméter, Decimeters, láb, hüvelyk, kilométer, méter, milliméter, Yard***.
  - Erősítse meg az objektum valós függőleges tájolásának megfelelő névleges gravitációs irányt. Ha az objektum lefelé irányuló függőleges/gravitációs értéke-Y, használja ***(0,-1, 0)** _ vagy _*_ (0, 0,-1) _ * * a-Z, és hasonlóan bármilyen más irányhoz.
  - Győződjön meg arról, hogy a 3D modell a támogatott formátumok egyikében van kódolva:,,, `.glb` `.gltf` `.ply` `.fbx` , `.obj` .
- A modell átalakítási szolgáltatása hosszú időt is igénybe vehet egy nagyméretű, magas LOD (részletességi szintű) modell feldolgozásához. A hatékonyság érdekében elődolgozhatja a 3D-modellt a belső arcok eltávolításához.

## <a name="detection"></a>Észlelés

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- A megadott futtatókörnyezeti SDK-nak egy felhasználó által megadott keresési régióra van szüksége a fizikai objektum (ok) kereséséhez és észleléséhez. A keresési régió lehet egy határoló mező, egy gömb, egy nézet csonkakúpot vagy bármely kombinációja. A hamis észlelés elkerülése érdekében érdemes egy olyan keresési régiót beállítani, amely elég nagy ahhoz, hogy az objektumra kiterjedjen. A megadott minta alkalmazások használatakor az objektum egyik oldalán a legközelebbi felületről 2 méter távolságra állhat, és elindíthatja az alkalmazást.
- Mielőtt elkezdené az Azure Object Anchors alkalmazást egy HoloLens 2 eszközön, távolítsa el a hologramokat a munkahely környékén az eszközök főbb beállításain keresztül a ***Beállítások – >rendszer->Hologramok*** használatával

  Ez a lépés biztosítja, hogy ha egy új objektum, például egy autó ugyanabban a térben található, mint egy másik korábban, vagy az objektum a célhelyről lett áthelyezve, a régi és a lényegtelen Hologramok nem maradnak meg, és a jelenleg megtekinthető objektumra vonatkozóan zavaró vizualizációt hoz létre.
- A Hologramok eltávolítása és az alkalmazás elindítása előtt vizsgálja meg az objektumot, például egy autót az objektum megtekintésével, miközben az eszközt körülbelül 1-2 méterre, az objektum körül pedig lassan, egy vagy két alkalommal kell kinéznie.

  Ezzel a lépéssel biztosíthatja, hogy a korábbi objektumok által a térben létrehozott fennmaradó felszíni becslések és a vizsgálatok az aktuális célobjektum azon felületével frissüljenek, amelyet használni fog. Ellenkező esetben az alkalmazás a 3D-modell és a hozzá tartozó Hologramok pontatlan igazítását eredményező dupla szellemkép-felületet láthat. Az objektum előzetes vizsgálata nagy mértékben csökkenti az Azure-objektumok észlelési késleltetését, azaz 30 másodperctől 5 másodpercre.
- A sötét és a jól tükröző objektumok esetében előfordulhat, hogy az objektumnak szorosabban kell beolvasnia az objektumot, és a fej felfelé és lefelé mozgatásával, valamint jobbra és balra haladva kell megnéznie, hogy az eszköz több nézőpontból és több távolságból is lásson több felületet.
- Ha nem megfelelő objektum-észlelést lát, például a tükrözött tájolást, vagy helytelenül állítja be a problémát, például egy megdöntött modell, a térbeli leképezést kell megjelenítenie. A helytelen eredmények gyakran rosszak vagy hiányosak a felület újraépítése miatt. Távolítsa el a hologramokat, vizsgálja meg az objektumot, és futtassa újból az objektum-észlelést az alkalmazásban.
- A megadott futtatókörnyezeti SDK néhány paramétert biztosít a felhasználók számára az észlelés finomhangolásához, ahogyan azt a minta alkalmazásaiban mutatjuk be. Az alapértelmezett paraméterek a legtöbb objektum esetében jól működnek. Ha úgy találja, hogy bizonyos objektumokhoz módosítania kell őket, néhány javaslat:
  - Ha a fizikai objektum nagy, sötét vagy fényes, akkor használjon alacsonyabb felszíni lefedettségi küszöbértéket.
  - Kis léptékű módosítás engedélyezése (például 0,1) nagyméretű objektumhoz, például autóhoz.
  - Az objektum helyi függőleges irányának és súlyossági foka közötti eltérések megadása, ha az objektum egy lejtőn van.
