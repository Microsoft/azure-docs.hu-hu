---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 57d14ae6e6da7cfa883f1aed74cce390c0185d98
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491677"
---
Egy adott RoboCopy futtatásának sebessége és sikerességi aránya több tényezőtől függ:

* IOPS a forrás-és cél tárolón
* a forrás és a cél között elérhető hálózati sávszélesség
* a névtérben lévő fájlok és mappák gyors feldolgozásának lehetősége
* a RoboCopy futtatása közötti változások száma


### <a name="iops-and-bandwidth-considerations"></a>IOPS és sávszélességgel kapcsolatos megfontolások

Ebben a kategóriában meg kell fontolnia a **forrás**- **és a tároló, valamint** a hozzájuk csatlakozó **hálózat** képességeit. A lehetséges maximális átviteli sebességet a három összetevő leglassabb értéke határozza meg. Győződjön meg arról, hogy a hálózati infrastruktúra úgy van konfigurálva, hogy az optimális átviteli sebességet támogassa a legjobb képességeinek megfelelően.

> [!CAUTION]
> A lehető leggyorsabban a lehető leghamarabb másolja a helyi hálózat és a NAS berendezés kihasználtságát más, gyakran üzleti szempontból kritikus fontosságú feladatokhoz.

Előfordulhat, hogy a lehető leggyorsabban másolja a másolást, ha fennáll a kockázata annak, hogy az áttelepítés kisajátíthatja a rendelkezésre álló erőforrásokat.

* Gondolja át, hogy mikor érdemes a környezetében áttelepítést futtatni: napközben, munkaidőn kívül vagy hétvégén.
* A RoboCopy sebességének szabályozása érdekében vegye fontolóra a hálózatkezelési QoS-t is a Windows Serveren.
* Kerülje az áttelepítési eszközök szükségtelen működését.

A RobCopy képes a csomagok közötti késések beszúrására a `/IPG:n` `n` Robocopy-csomagok közötti ezredmásodpercben mért kapcsoló megadásával. Ezzel a kapcsolóval elkerülhetők az erőforrások monopolizálása az IO által korlátozott eszközökön és a zsúfolt hálózati kapcsolatokon keresztül.

`/IPG:n` nem használható pontos hálózati sávszélesség-szabályozáshoz egy adott Mbps-hez. Használja helyette a Windows Server Network QoS szolgáltatást. A RoboCopy teljes mértékben az SMB protokollra támaszkodik minden hálózati igény esetén. Az SMB használatának oka, hogy a RoboCopy miért nem befolyásolja a hálózati átviteli sebességet, de lelassíthatja a használatát. 

Hasonló, a NAS-on megfigyelt IOPS is érvényes. A fürt mérete a NAS-köteten, a csomagok mérete és más tényezők tömbje befolyásolja a megfigyelt IOPS. A csomagok közötti késleltetés bevezetése gyakran a legegyszerűbb módszer a NAS terhelésének szabályozására. Több érték tesztelése, például körülbelül 20 ezredmásodperc (n = 20) a szám többszörösére. Miután bevezette a késést, kiértékelheti, hogy a többi alkalmazás a várt módon működik-e. Ez az optimalizálási stratégia lehetővé teszi az optimális RoboCopy sebesség megkeresését a környezetben.

### <a name="processing-speed"></a>Feldolgozási sebesség

A RoboCopy áthalad a névtéren, és kiértékeli az összes fájlt és mappát a másoláshoz. A rendszer minden fájlt kiértékel a kezdeti másolat és a felmerülő másolatok során. Például a RoboCopy/MIR ismételt futtatása azonos forrás-és cél-tárolási helyekre. Ezek az ismétlődő futtatások a felhasználók és alkalmazások leállásának minimalizálásához, valamint az áttelepített fájlok teljes sikerességi arányának javításához hasznosak.

Gyakran az alapértelmezett érték a sávszélesség megtervezése az áttelepítés leginkább korlátozó tényezője – és ez igaz lehet. A névtér enumerálásának lehetősége azonban befolyásolhatja, hogy a nagyobb méretű, kisebb fájlokkal rendelkező névterek esetében a teljes idő még nagyobb legyen. Vegye figyelembe, hogy a kisméretű fájlok 1 TiB-as verziójának másolása jóval hosszabb időt vesz igénybe, mint a kevesebb, de nagyobb méretű fájlok 1. Feltételezve, hogy minden más változó változatlan marad.

Ennek a különbségnek az az oka, hogy a feldolgozási teljesítmény szükséges a névtér átjárásához. A RoboCopy támogatja a többszálas másolást a `/MT:n` (z) paraméterrel, ahol n a processzor-szálak számát jelenti. Tehát amikor kifejezetten a RoboCopy számára helyez üzembe egy gépet, vegye figyelembe a processzor-magok számát és az általa megadott szálak számával létesített kapcsolatukat. A leggyakoribb két szál egy mag esetében. A gép alapvető és szálak száma fontos adatpontnak számít, hogy eldöntse, milyen többszálas értékeket `/MT:n` kell megadnia. Azt is vegye figyelembe, hogy hány RoboCopy-feladat fut párhuzamosan egy adott gépen.

A több szálon átmásoljuk a kis méretű fájlok 1 – TiB-példáját, mint a kevesebb szálat. Ugyanakkor előfordulhat, hogy a további erőforrás-befektetés az 1 TiB-beli nagyobb fájlok esetében nem eredményez arányos előnyöket. A nagy szálak száma a hálózaton egyszerre több nagyméretű fájl másolását is megkísérli. Ez az extra hálózati tevékenység növeli annak valószínűségét, hogy az átviteli sebesség vagy a tárolás IOPS.

### <a name="avoid-unnecessary-work"></a>A szükségtelen munka elkerülése

Kerülje el a névtér nagy léptékű módosításait. Például áthelyezheti a fájlokat a címtárak között, nagy léptékben módosíthatja a tulajdonságokat, vagy módosíthatja az engedélyeket (NTFS ACL-ek). Az ACL-módosítások különösen nagy hatással lehetnek, mert gyakran a mappa-hierarchiában alacsonyabb fájlokra érvényesek a kaszkádolt változások. A következmények a következőket okozhatják:

* kibővített RoboCopy-feladatok futási ideje, mert az ACL-változás által érintett összes fájl és mappa frissítése szükséges
* Előfordulhat, hogy újra kell másolni a korábban áthelyezett adatcserét. Előfordulhat például, hogy a rendszer a mappák struktúráinak változása után több adatfájlt is át kell másolni, miután korábban már másolta a fájlokat. Egy RoboCopy-feladatot nem lehet "lejátszani" egy névtérbeli változást. A következő feladatnak el kell törölni a korábban a régi mappastruktúrát, és újra fel kell töltenie a fájlokat az új mappa struktúrájába.

Egy másik fontos szempont a RoboCopy eszköz hatékony használata. Az ajánlott RoboCopy parancsfájllal a hibákhoz naplófájlokat hozhat létre és menthet. A másolási hibák előfordulhatnak – ez normális. Ezek a hibák gyakran szükségessé teszik, hogy egy másolási eszköz (például a RoboCopy) több fordulóját futtassák. Egy kezdeti Futtatás, azaz egy NAS kiszolgálóról a DataBox vagy egy kiszolgáló egy Azure-fájlmegosztást. És egy vagy több további Futtatás a/MIR kapcsolóval, és a nem másolt fájlok kifogására és újrapróbálkozására használható.

Fel kell készülnie arra, hogy a RoboCopy több fordulóját futtassuk egy adott névtér-hatókörön. Az egymást követő futtatások gyorsabban futnak, ahogy a másolásuk kevesebb, de a névtér feldolgozásának gyorsasága egyre nagyobb mértékben korlátozott. Ha több kört futtat, felgyorsíthatja az egyes körök felgyorsítását, mivel a RoboCopy nem megfelelő módon próbálkozik az adott futtatásban lévő összes adat másolásával. Ezek a RoboCopy-kapcsolók jelentős különbséget tehetnek:

* `/R:n` n = milyen gyakran próbálkozzon újra egy hibás fájl másolásával és 
* `/W:n` n = hány másodpercig kell várni az újrapróbálkozások között

`/R:5 /W:5` egy ésszerű beállítás, amelyet tetszés szerint módosíthat. Ebben a példában a rendszer ötször újrapróbálkozik egy hibás fájllal, és az újrapróbálkozások közötti öt másodperces várakozási idővel. Ha a fájl még nem másolható, a következő RoboCopy-feladattal próbálkozik újra. Az olyan fájlok, amelyek nem sikerült, mert használatban vannak, vagy az időtúllépési problémák miatt előfordulhat, hogy a rendszer végül sikeresen átmásolja a fájlokat.
   
