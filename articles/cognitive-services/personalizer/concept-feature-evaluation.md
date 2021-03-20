---
title: Szolgáltatások kiértékelése – személyre szabás
titleSuffix: Azure Cognitive Services
description: Ha kiértékelést futtat a Azure Portal személyre szabott erőforrásában, a személyre szabott információval megtudhatja, hogy a rendszer milyen funkciókat és műveleteket befolyásol a modellben.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: c0e47a2943cf8c934d201f76aefc41868adf0b25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87127723"
---
# <a name="feature-evaluation"></a>Szolgáltatások értékelése

Ha kiértékelést futtat a [Azure Portal](https://portal.azure.com)személyre szabott erőforrásában, a személyre szabott információval megtudhatja, hogy a rendszer milyen funkciókat és műveleteket befolyásol a modellben. 

Ez a következő esetekben hasznos:

* Képzelje el, hogy további funkciókat is használhat, és megismerheti, hogy milyen funkciók fontosabbak a modellben.
* Tekintse meg, hogy mely funkciók nem fontosak, és esetleg távolítsa el őket, vagy elemezze, hogy mi is befolyásolja a használatot.
* Útmutatást nyújthat a szerkesztőknek és a kurátori csapatoknak a katalógusba bekerülő új tartalommal vagy termékekkel kapcsolatban.
* A funkciók személyre szabásakor előforduló gyakori problémák és hibák elhárítása.

A fontosabb funkciók nagyobb súlyokkal rendelkeznek a modellben. Mivel ezek a funkciók erősebb súlyt jelentenek, általában jelen lesznek, ha a személyre szabott előnyökkel rendelkezik.

## <a name="getting-feature-importance-evaluation"></a>A funkciók fontossági kiértékelésének beolvasása

A funkciók fontossági eredményeinek megjelenítéséhez próbaverziót kell futtatnia. A kiértékelés során a rendszer a próbaidőszak alatt megfigyelt szolgáltatások nevei alapján létrehoz egy emberi olvasási funkciót.

A funkció fontosságával kapcsolatos információk a jelenlegi személyre szabott online modellt jelölik. A kiértékelés a kiértékelési időszak végén elmentett modell fontosságát elemzi, miután az értékelés során végzett összes betanítást elvégezte a jelenlegi online tanulási szabályzattal. 

A funkció fontossági eredményei nem jelentenek más, a kiértékelés során tesztelt vagy létrehozott szabályzatokat és modelleket.  A kiértékelés nem tartalmaz olyan szolgáltatásokat, amelyeket a rendszer a próbaidőszak lejárta után személyre Szabottnak küld.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>A funkció fontossági értékelésének értelmezése

A személyre szabás kiértékeli a funkciókat úgy, hogy "csoportokat" hoz létre a hasonló fontosságú funkciókhoz. Az egyik csoport azt is elmondhatja, hogy az általános nagyobb fontossággal bír, mint mások, de a csoporton belül a funkciók sorrendje betűrendben van.

Az egyes szolgáltatásokra vonatkozó információk a következők:

* Azt határozza meg, hogy a szolgáltatás környezetből vagy műveletekből származik-e.
* A szolgáltatás kulcsa és értéke.

Egy Ice Cream Shop-rendezési alkalmazás például a "Context. Weather: Hot" kifejezést tekintheti meg nagyon fontos szolgáltatásként.

A személyre szabott funkció olyan funkciók korrelációit jeleníti meg, amelyek, ha együttesen vesznek figyelembe, magasabb szintű jutalmakat eredményeznek.

Láthatja például, hogy "Context. Weather: Hot *with* Action. MENUITEM: jégkrém", valamint "Context. Weather: hideg *a* Action. MENUITEM: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>A funkciók kiértékelése alapján elvégezhető műveletek

### <a name="imagine-additional-features-you-could-use"></a>Képzelje el, hogy milyen további funkciókat használhat

Ismerkedjen meg a modell fontosabb szolgáltatásaival. Ha például a "Context. MobileBattery: Low" szöveg jelenik meg egy videós mobil alkalmazásban, akkor előfordulhat, hogy a kapcsolattípus azt is megteheti, hogy az ügyfelek egy videoklipet látnak egy másikhoz, majd a kapcsolat típusát és a sávszélességet az alkalmazásba is felvehetik.

### <a name="see-what-features-are-not-important"></a>Ismerje meg, hogy mely funkciók nem fontosak

Esetleg távolítsa el a nem fontos funkciókat, vagy elemezze, hogy mi befolyásolhatja a használatot. A funkciók számos okból is alacsonyak lehetnek. Az egyik lehetséges, hogy a funkció nem befolyásolja a felhasználói viselkedést. Azt is jelentheti, hogy a funkció nem látható a felhasználó számára. 

Egy videós webhely például láthatja, hogy a "Action. VideoResolution = 4k" egy kis jelentőségű funkció, amely ellentmond a felhasználói kutatásoknak. Ennek oka az lehet, hogy az alkalmazás még nem említi meg vagy nem jeleníti meg a videó felbontását, így a felhasználók nem változtatják meg a viselkedésük alapján.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Útmutatás nyújtása a szerkesztői vagy kurátori csapatoknak

Adja meg a katalógusba bekerülő új tartalommal vagy termékekkel kapcsolatos útmutatást. A személyre szabott megoldás olyan eszköz, amely kibővíti az emberi betekintést és a csapatokat. Ennek egyik módja az, ha információt nyújt a szerkesztői csoportoknak arról, hogy mi a helyzet a termékekkel, cikkekkel vagy tartalommal. Előfordulhat például, hogy a videó alkalmazás forgatókönyve azt mutatja, hogy a "Action. VideoEntities. Cat: true" nevű fontos funkció, amely felszólítja a szerkesztői csapatot, hogy több Cat-videót is bevigyen.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Gyakori problémák és hibák elhárítása

A gyakori problémákat és hibákat az alkalmazás kódjának módosításával lehet megjavítani, így nem küldi el a nem megfelelő vagy helytelenül formázott szolgáltatásokat a személyre szabáshoz. 

A funkciók küldésével kapcsolatos gyakori hibák a következők:

* Személyes azonosításra alkalmas adatok küldése. A személyre szabott személyes adatok (például a név, a telefonszám, a hitelkártya-számok, az IP-címek) nem használhatók a megszemélyesítő használatával. Ha az alkalmazásnak nyomon kell követnie a felhasználókat, használjon nem azonosító UUID-t vagy más felhasználóazonosító-számot. A legtöbb esetben ez problematikus is.
* Nagy számú felhasználó esetén nem valószínű, hogy az egyes felhasználók interakciója a népesség összes interakcióján kívül esik, így a felhasználói azonosítók (még akkor is, ha nem személyes adatok) elküldése valószínűleg nagyobb zajt eredményez, mint az érték a modellnél.
* A dátum-idő mezők pontos időbélyegként való küldése a featurized időértékek helyett. Olyan funkciókkal rendelkezik, mint a Context. TimeStamp. Day = hétfő vagy a "Context. TimeStamp. Hour" = "13". Mindegyikhez legfeljebb 7 vagy 24 jellemző érték lesz. De a "Context. TimeStamp": "1985-04-12T23:20:50.52 Z" olyan pontos, hogy nem fog tudni tanulni belőle, mert soha nem fog megtörténni.

## <a name="next-steps"></a>Következő lépések

A [méretezhetőség és a teljesítmény](concepts-scalability-performance.md) megértése a személyre szabással.

