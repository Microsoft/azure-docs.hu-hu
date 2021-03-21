---
title: Renderelés áttekintése
description: Az Azure használatának bemutatása és a Azure Batch renderelési képességeinek áttekintése
author: mscurrell
ms.author: markscu
ms.date: 01/14/2021
ms.topic: how-to
ms.openlocfilehash: 1cd07f9322837c03e15aaeabec993820deb3170a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98232114"
---
# <a name="rendering-using-azure"></a>Renderelés az Azure használatával

A renderelés a 3D modellek készítésének és a 2D-képekbe való átalakításának folyamata. a 3D-jelenet fájljai olyan alkalmazásokban vannak létrehozva, mint az Autodesk 3ds Max, a Autodesk Maya és a Blender.  Az olyan alkalmazások renderelése, mint a Autodesk Maya, a Autodesk Arnold, a Chaos Group V-Ray és a Blender ciklusok 2D-képeket készítenek.  Néha egyetlen lemezkép jön létre a jelenet fájljaiból. Azonban gyakori a több rendszerkép modellezése és renderelése, majd egy animációban való kombinálása.

A renderelési munkaterhelést nagy mértékben használják a média és a szórakoztató ipar különleges hatásaira (VFX). A renderelés számos más iparágban is használatos, például a reklámokban, a kereskedelemben, az olaj- és gáziparban és a gépiparban.

A renderelés folyamata számítási igényű; számos képkocka vagy rendszerkép hozható létre, és az egyes képek renderelése több órát is igénybe vehet.  A renderelés ezért egy tökéletes batch-feldolgozási feladat, amely az Azure-t több renderelés párhuzamos futtatására képes használni, és számos hardvert (például GPU-ket) használ.

## <a name="why-use-azure-for-rendering"></a>Miért érdemes az Azure-t renderelésre használni?

Számos ok miatt a renderelés tökéletesen illeszkedik az Azure-hoz:

* A renderelési feladatok számos darabra bonthatók, amelyek több virtuális gép használatával párhuzamosan is futtathatók:
  * Az animációk számos képkockát tartalmaznak, és az egyes keretek párhuzamosan is megjeleníthető.  Minél több virtuális gép áll rendelkezésre az egyes keretek feldolgozásához, annál gyorsabban hozhatók létre az összes keret és az animáció.
  * Egyes renderelési szoftverek lehetővé teszik, hogy az egyes keretek több darabra legyenek bontva, például csempék vagy szeletek számára.  Mindegyik darab külön jeleníthető meg, majd a végső képhez kombinálva, ha az összes darab elkészült.  Minél több virtuális gép érhető el, annál gyorsabban lehet megjeleníteni a keretet.
* A renderelési projektek nagy léptéket igényelhetnek:
  * Az egyes keretek összetettek lehetnek, és több órát is igénybe vehetnek, még a nagy teljesítményű hardveren is. az animációk több száz ezer képkockából állhatnak.  Nagy mennyiségű számításra van szükség a kiváló minőségű animációk ésszerű időtartamú megjelenítéséhez.  Néhány esetben több mint 100 000 magot használtak párhuzamosan több ezer keret megjelenítéséhez.
* A renderelési projektek projekt-alapúak, és különböző mennyiségű számítást igényelnek:
  * Szükség esetén kioszthatja a számítási és a tárolási kapacitást, felskálázást végez a projekt során betöltésnek megfelelően, és eltávolíthatja a projekt befejezésekor.
  * A kapacitás kiosztása után kell fizetnie, de nem kell fizetnie, ha nincs betöltés, például a projektek között.
  * A váratlan változások miatt gondoskodik a kitörésekről; nagyobb skálázás, ha a projektben váratlan változások történnek, és ezeket a módosításokat szűk időben kell feldolgozni.
* Az alkalmazások, a számítási feladatok és az időkeretek alapján széles választékban választhat:
  * Az Azure-ban széles körben elérhetők a rendelkezésre álló hardverek, amelyeket a Batch szolgáltatással lehet kiosztani és felügyelni.
  * A projekttől függően előfordulhat, hogy a követelmény a legjobb ár/teljesítmény vagy a legjobb általános teljesítmény.  A különböző jelenetek és/vagy megjelenítési alkalmazások eltérő memóriabeli követelményekkel rendelkeznek.  Egyes renderelési alkalmazások a legjobb teljesítményhez vagy bizonyos funkciókhoz használhatják a GPU-ket. 
* Az alacsony prioritású vagy a [direktszínű virtuális gépek](https://azure.microsoft.com/pricing/spot/) csökkentik a költségeket:
  * Az alacsony prioritású és a helyszíni virtuális gépek a standard virtuális gépekhez képest nagy kedvezményekhez érhetők el, és bizonyos feladatokhoz megfelelőek.
  
## <a name="existing-on-premises-rendering-environment"></a>Meglévő helyszíni renderelési környezet

A leggyakoribb eset az, hogy egy meglévő helyszíni Render Farm felügyelhető egy renderelési felügyeleti alkalmazással, mint például a PipelineFX Qube, a Royal Render, a Thinkbox határidő vagy egy egyéni alkalmazás.  A követelmény a helyszíni rendering Farm kapacitásának kiterjesztése Azure-beli virtuális gépek használatával.

Az Azure-infrastruktúra és-szolgáltatások olyan hibrid környezetek létrehozásához használhatók, ahol az Azure-t a helyszíni kapacitás kiegészítéseként használják. Például:

* [Virtual Network](../virtual-network/virtual-networks-overview.md) használatával helyezheti el az Azure-erőforrásokat ugyanarra a hálózatra, mint a helyszíni Render Farm.
* A [avere vFXT for Azure vagy az](../avere-vfxt/avere-vfxt-overview.md) [Azure HPC cache](../hpc-cache/hpc-cache-overview.md) használatával gyorsítótárazhatja az Azure-ban található forrásfájlokat, hogy csökkentse a sávszélesség-használatot és a késést, maximalizálja a teljesítményt.
* Győződjön meg arról, hogy a meglévő licenckiszolgáló a virtuális hálózaton található, és vásárolja meg az extra Azure-alapú kapacitás biztosításához szükséges további licenceket.

## <a name="no-existing-render-farm"></a>Nincs meglévő Render Farm

Előfordulhat, hogy az ügyfél-munkaállomások renderelést végeznek, de a renderelési terhelés egyre nő, és a munkaállomás kapacitása túl sokáig tart.

Két fő lehetőség közül választhat:

* Helyezzen üzembe egy helyszíni Render Managert, például a Royal Renderet, és állítson be hibrid környezetet az Azure használatára, ha további kapacitásra vagy teljesítményre van szükség. A render Manager kifejezetten a számítási feladatokhoz van igazítva, és a népszerű ügyfélalkalmazások beépülő moduljait is magában foglalja, ami lehetővé teszi a renderelési feladatok egyszerű beküldését.

* Egy Azure Batch használó egyéni megoldás a számítási kapacitás lefoglalásához és kezeléséhez, valamint a feladatok ütemezésének a renderelési feladatok futtatásához való biztosításához.

## <a name="next-steps"></a>Következő lépések

 Ismerje meg, hogyan [bővíthető az Azure-infrastruktúra és-szolgáltatások egy meglévő helyszíni Render Farm kibővítéséhez](https://azure.microsoft.com/solutions/high-performance-computing/rendering/).

További információ a [Azure batch renderelési képességeiről](batch-rendering-functionality.md).
