---
title: Az Azure Service Fabric Mesh-alkalmazások méretezhetősége
description: Az alkalmazások Service Fabric Meshba való üzembe helyezésének egyik előnye, hogy könnyedén, manuálisan vagy automatikus skálázási szabályzatokkal egyszerűen méretezheti a szolgáltatásokat.
author: dkkapur
ms.author: dekapur
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 474eda904df653d514fd2ee59fa046f1f87a66aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84712241"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Service Fabric Mesh-alkalmazások méretezése

Az alkalmazások Service Fabric Meshba való üzembe helyezésének egyik fő előnye, hogy könnyedén méretezheti vagy kibővítheti szolgáltatásait. Ezt a szolgáltatások különböző terhelésének kezelésére, illetve a rendelkezésre állás javítására kell használni. A szolgáltatásokat manuálisan vagy akár ki is méretezheti, vagy beállíthatja az automatikus skálázási házirendeket.

## <a name="manual-scaling-instances"></a>Manuális skálázási példányok

Az alkalmazás erőforrásának üzembehelyezési sablonjában mindkét szolgáltatás rendelkezik egy *replicaCount* (replikaszám) tulajdonsággal, amellyel megadhatja, hány példányban szeretné a szolgáltatást üzembe helyezni. Egy alkalmazás több szolgáltatásból is állhat, és minden szolgáltatás egyedi *replicaCount* értékkel rendelkezik. A replikákat a rendszer együtt kezeli és helyezi üzembe. A szolgáltatásreplikák számának módosításához módosítsa minden olyan szolgáltatás *replicaCount* értékét az üzembehelyezési sablonban vagy a paraméterfájlban, amelyet horizontálisan le szeretne skálázni. Ezt követően frissítse az alkalmazást.

A szolgáltatási példányok manuális skálázására példákat a [szolgáltatások manuális méretezése a vagy a szolgáltatásban](service-fabric-mesh-tutorial-template-scale-services.md)című témakörben talál.

## <a name="autoscaling-service-instances"></a>Szolgáltatás-példányok automatikus skálázása
Az automatikus skálázás a Service Fabric egy további funkciója, amellyel dinamikusan méretezheti a szolgáltatási példányok számát (horizontális skálázás). Az automatikus skálázás nagy rugalmasságot biztosít, és lehetővé teszi a szolgáltatási példányok kiépítési vagy eltávolítását a CPU vagy a memória kihasználtsága alapján.  Az automatikus skálázás lehetővé teszi, hogy a számítási feladatok megfelelő számú szolgáltatási példányát futtassa, és optimalizálja a költségeket.

Egy automatikus skálázási házirend van definiálva a szolgáltatás-erőforrás fájlban. Az egyes skálázási házirendek két részből állnak:

- Skálázási trigger, amely leírja, hogy a szolgáltatás méretezése mikor történjen. A szolgáltatás skálázásakor három tényezőt kell meghatározni. Az *alacsonyabb terhelési küszöbérték* olyan érték, amely meghatározza, hogy a szolgáltatás hogyan méretezhető. Ha a partíciók összes példányának átlagos terhelése ennél az értéknél kisebb, akkor a szolgáltatás skálázása megtörténik. A *felső terhelés küszöbértéke* egy olyan érték, amely meghatározza, hogy a szolgáltatás Mikor lesz felskálázásra. Ha a partíció összes példányának átlagos terhelése meghaladja ezt az értéket, a rendszer kibővíti a szolgáltatást. A *skálázási időköz* határozza meg, hogy a trigger milyen gyakran (másodpercben) lesz bejelölve. Ha a trigger be van jelölve, a skálázásra van szükség, a rendszer alkalmazza azt. Ha nincs szükség skálázásra, a rendszer nem végez műveletet. A skálázási intervallum lejárata előtt mindkét esetben a trigger nem lesz újra bejelölve.

- Skálázási mechanizmus, amely leírja, hogy a rendszer mikor hajtja végre a skálázást az aktiváláskor. A *skálázási növekmény* meghatározza, hogy a rendszer hány példányt fog hozzáadni vagy eltávolítani a mechanizmus indításakor. A *példányok maximális száma* határozza meg a skálázás felső korlátját. Ha a példányok száma eléri ezt a korlátot, a rendszer a terheléstől függetlenül nem fogja méretezni a szolgáltatást. A *példányok minimális száma* határozza meg a skálázás legalacsonyabb korlátját. Ha a partíció példányainak száma eléri ezt a korlátot, a rendszer a terheléstől függetlenül nem fogja méretezni a szolgáltatást.

Ha szeretné megtudni, hogyan állíthatja be a szolgáltatásra vonatkozó autoskálázási szabályzatot, olvassa el az [autoscale Services](service-fabric-mesh-howto-auto-scale-services.md)című témakört.

## <a name="next-steps"></a>További lépések

Az alkalmazás modelljével kapcsolatos információkért lásd: [Service Fabric erőforrások](service-fabric-mesh-service-fabric-resources.md)
