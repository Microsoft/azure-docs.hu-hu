---
title: Azure virtuálisgép-méretezési csoportok – áttekintés
description: Tudnivalók az Azure virtuálisgép-méretezési csoportokról és az alkalmazások automatikus méretezéséről
author: mimckitt
ms.author: mimckitt
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 76d3bc1e1736e648316bcd81bf8897d1d2f272a2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672579"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Mik azok a virtuálisgép-méretezési csoportok?
Az Azure virtuálisgép-méretezési csoportok lehetővé teszik elosztott terhelésű virtuális gépek csoportjának létrehozását és kezelését. A virtuálisgép-példányok száma automatikusan növelhető vagy csökkenthető a pillanatnyi igényeknek megfelelően vagy egy meghatározott ütemezés szerint. A méretezési csoportok biztosítják az alkalmazások magas rendelkezésre állását, és lehetővé teszik nagy számú virtuális gép központi felügyeletét, konfigurálását és frissítését. A virtuálisgép-méretezési csoportokkal nagyobb léptékű szolgáltatások építhetők ki a nagy számítási igényű, „big data” típusú és tárolóalapú számítási feladatokhoz.


## <a name="why-use-virtual-machine-scale-sets"></a>Miért érdemes virtuálisgép-méretezési csoportokat használni?
A redundancia és a jobb teljesítmény biztosítása érdekében az alkalmazások általában több példány között vannak szétosztva. Az ügyfelek az alkalmazást egy terheléselosztón keresztül érhetik el, amely elosztja a kérelmeket az alkalmazás példányai között. Ha egy alkalmazáspéldány karbantartásra vagy frissítésre szorul, az ügyfelek egy másik elérhető példányhoz irányíthatók. A megnövekedett ügyféligények kielégítéséhez szükség lehet az alkalmazást futtató alkalmazáspéldányok számának növelésére.

Az Azure-beli virtuálisgép-méretezési csoportok biztosítják a szükséges felügyeleti képességeket a több virtuális gépen futó alkalmazásokhoz, az [erőforrások automatikus méretezéséhez](virtual-machine-scale-sets-autoscale-overview.md) és a forgalom terheléselosztásához. A méretezési csoportok legfontosabb előnyei a következők:

- **Több virtuális gép egyszerű létrehozása és kezelése**
    - Ha egy alkalmazás sok virtuális gépen fut, fontos fenntartani egy állandó, a környezet egészében érvényesülő konfigurációt. Az alkalmazás megbízható teljesítménye érdekében fontos, hogy minden virtuális gépen ugyanaz legyen a gép mérete, a lemezkonfiguráció és az alkalmazás telepített verziója.
    - A méretezési csoportok használatakor a rendszer az összes virtuálisgép-példányt egyazon alapul szolgáló operációsrendszer-képből és konfigurációból hozza létre. Ez a megközelítés lehetővé teszi több száz virtuális gép egyszerű kezelését további konfigurációs feladatok és hálózatkezelés nélkül.
    - A méretezési csoportok az [Azure Load Balancer](../load-balancer/load-balancer-overview.md) használatát támogatják az alapszintű 4. rétegbeli forgalom elosztásához, az [Azure Application Gateway](../application-gateway/overview.md) pedig fejlettebb, 7. rétegbeli forgalom elosztását és TLS-lezárást biztosít.

- **Magas rendelkezésre állás és rugalmas alkalmazások**
    - A méretezési csoportok segítségével az alkalmazások több példányban futtathatók. Ha valamelyik virtuálisgép-példány problémába ütközik, az ügyfelek továbbra is elérhetik az alkalmazást valamelyik másik példányon keresztül, minimális megszakítással.
    - A rendelkezésre állás fokozása érdekében a [rendelkezésre állási zónák](../availability-zones/az-overview.md) használatával a méretezési csoportokhoz tartozó virtuálisgép-példányok automatikusan eloszthatók egyetlen adatközponton belül, vagy több adatközpont között.

- **Az alkalmazások automatikus, az erőforrásigényekhez illeszkedő méretezhetősége**
    - Az alkalmazásokkal szemben támasztott ügyféligények a nap vagy a hét folyamán megváltozhatnak. A méretezési csoportok képesek automatikusan növelni a virtuálisgép-példányok számát, ha az igény nő, illetve csökkenteni, ha visszaesik.
    - Az automatikus méretezés segít minimális szinten tartani az alkalmazást futtató virtuálisgép-példányok számát alacsony igényszint esetén, és elfogadható teljesítményt biztosít az ügyfeleknek további VM-példányok hozzáadásával, amikor igénynövekedés tapasztalható. Ez a képesség segít a költségek csökkentésében, és hatékony módon, csakis szükség esetén hoz létre Azure-erőforrásokat.

- **Működtetés nagy léptékben**
    - A méretezési csoportok akár 1000 virtuálisgép-példányt is tartalmazhatnak. Ha saját virtuálisgép-rendszerképeit hozza létre és tölti fel, a korlát 600 virtuálisgép-példány.
    - Az éles munkaterhelésekkel kapcsolatos legjobb teljesítmény érdekében használja az [Azure Managed Disks](../virtual-machines/managed-disks-overview.md)-t.


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Mi a különbség a virtuális gépek és a méretezési csoportok között?
A méretezési csoportokat virtuális gépek alkotják. A méretezési csoportok biztosítják az alkalmazások futtatásához és méretezéséhez szükséges felügyeleti és automatizálási rétegeket. Helyettük manuálisan is létrehozhat és felügyelhet önálló virtuális gépeket, illetve meglévő eszközök integrálásával is kialakíthat egy hasonló szintű automatizációt. A következő táblázat összefoglalja a méretezési csoportok előnyeit több virtuálisgép-példány manuális felügyeletével szemben.

| Eset                           | Virtuális gépek manuális csoportja                                                                    | Virtuálisgép-méretezési csoport |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| További virtuálisgép-példányok hozzáadása        | A létrehozás, a konfigurálás és a megfelelőség biztosítása manuális folyamatokkal történik                             | Automatikus létrehozás központi konfigurációból |
| Forgalom kiegyensúlyozása és elosztása | Manuálisan kell létrehozni és konfigurálni egy Azure-terheléselosztót vagy alkalmazásátjárót      | Automatikus létrehozás és integrálás egy Azure-terheléselosztóval vagy alkalmazásátjáróval |
| Magas rendelkezésre állás és redundancia   | Manuálisan kell létrehozni a rendelkezésre állási csoportot, illetve elosztani és nyomon követni a virtuális gépeket a rendelkezésre állási zónákban | A virtuálisgép-példányok elosztása a rendelkezésre állási zónák vagy rendelkezésre állási készletek között automatikusan történik |
| Virtuális gépek méretezése                     | Manuális monitorozás és Azure Automation                                                 | Automatikus méretezés a gazdagép metrikái, a vendég metrikái, az Application Insights vagy ütemezés alapján |

A méretezési csoportok nem járnak többletköltségekkel. Csak a mögöttes számítási erőforrások, például a virtuálisgép-példányok, a terheléselosztó vagy a felügyelt lemezes tárolók használatáért kell fizetni. A felügyeleti és automatizálási szolgáltatások, például az automatikus méretezés és a redundancia, nem járnak többletköltségekkel a virtuális gépek használatán túl.

## <a name="how-to-monitor-your-scale-sets"></a>A méretezési csoportok figyelése

Használja a [Azure monitor for VMst](../azure-monitor/vm/vminsights-overview.md), amely egy egyszerű előkészítési folyamattal rendelkezik, és automatizálja a méretezési csoportba tartozó virtuális gépekről származó fontos CPU-, memória-, lemez-és hálózati teljesítményszámlálók gyűjteményét. Emellett további figyelési képességeket és előre definiált vizualizációkat is tartalmaz, amelyek segítenek a méretezési csoportok rendelkezésre állásának és teljesítményének a kiépítésében.

Engedélyezheti a figyelést a [virtuálisgép-méretezési csoport alkalmazásához](../azure-monitor/app/azure-vm-vmss-apps.md) a Application Insights segítségével részletes információkat gyűjthet az alkalmazásról, többek között a lapok nézeteiről, az alkalmazások kéréseiről és a kivételekről. Ellenőrizze az alkalmazás rendelkezésre állását a [rendelkezésre állási teszt](../azure-monitor/app/monitor-web-app-availability.md) konfigurálásával a felhasználói forgalom szimulálása érdekében.

## <a name="data-residency"></a>Adattárolási hely

Az Azure-ban az ügyféladatok egyetlen régióban való tárolását lehetővé tevő funkció jelenleg csak az Ázsia és a Csendes-óceáni térség geo és Dél-Brazília (Sao Paulo állam) régiójában, a Geo régióban található Délkelet-ázsiai régióban (Szingapúr) érhető el. Az összes többi régió esetében az ügyféladatokat a Geo tárolja. További információ: [megbízhatósági központ](https://azure.microsoft.com/global-infrastructure/data-residency/).

## <a name="next-steps"></a>Következő lépések
Első lépésként hozza létre első virtuálisgép-méretezési csoportját az Azure Portalon.

> [!div class="nextstepaction"]
> [Méretezési csoport létrehozása az Azure Portalon](quick-create-portal.md)
