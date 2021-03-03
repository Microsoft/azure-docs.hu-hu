---
title: A Virtual Machines Azure dedikált gazdagépek áttekintése
description: További információ arról, hogyan használhatók az Azure dedikált gazdagépek a virtuális gépek telepítéséhez.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 46de182c7acfaf75b2e65fa318717348dd1c4b73
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667344"
---
# <a name="azure-dedicated-hosts"></a>Dedikált Azure-gazdagépek

Az Azure dedikált gazdagép olyan szolgáltatás, amely fizikai kiszolgálókat biztosít, amelyek egy vagy több virtuális gép üzemeltetésére alkalmasak egyetlen Azure-előfizetéshez. A dedikált gazdagépek ugyanazok a fizikai kiszolgálók, amelyek az adatközpontokban, erőforrásként vannak megadva. Dedikált gazdagépeket a régión, a rendelkezésre állási zónán és a tartalék tartományon belül is kiépítheti. Ezután elhelyezheti a virtuális gépeket közvetlenül a kiépített gazdagépeken, bármilyen konfigurációban, amely a legjobban megfelel az igényeinek.


## <a name="benefits"></a>Előnyök 

A teljes gazdagép megőrzése a következő előnyöket biztosítja:

-   Hardver elkülönítése a fizikai kiszolgáló szintjén. Más virtuális gépek nem lesznek elhelyezve a gazdagépeken. A dedikált gazdagépeket ugyanabban az adatközpontban helyezik üzembe, és ugyanazon a hálózaton és mögöttes tárolási infrastruktúrán osztoznak, mint más, nem elkülönített gazdagépek.
-   Az Azure platform által kezdeményezett karbantartási események szabályozása. Habár a karbantartási események többsége kevés hatással van a virtuális gépekre, vannak olyan érzékeny munkaterhelések, amelyekben a Szüneteltetés minden egyes másodperce hatással lehet. A dedikált gazdagépekkel a karbantartási időszakra is lehetőség van, hogy csökkentse a szolgáltatásra gyakorolt hatást.
-   Az Azure Hybrid benefittel saját licenceket hozhat a Windows és az SQL rendszerhez az Azure-ba. A hibrid előnyök használata további előnyöket biztosít. További információ: [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Csoportok, gazdagépek és virtuális gépek  

![A dedikált gazdagépek új erőforrásainak megtekintése.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

A **gazda-csoport** egy olyan erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Egy adott régióban és egy rendelkezésre állási zónában hozhat létre egy gazdagépet, és hozzáadhat gazdagépeket.

A **gazdagép** egy erőforrás, amely egy Azure-adatközpontban található fizikai kiszolgálóra van leképezve. A fizikai kiszolgáló a gazdagép létrehozásakor van lefoglalva. A gazdagépek egy gazdagépen belül jönnek létre. A gazdagép rendelkezik egy SKU-val, amely leírja, hogy milyen virtuálisgép-méreteket lehet létrehozni. Minden gazdagép különböző méretű virtuális gépeket képes tárolni, feltéve, hogy azok ugyanabból a méretből származnak.


## <a name="high-availability-considerations"></a>Magas rendelkezésre állási megfontolások 

A magas rendelkezésre állás érdekében több virtuális gépet kell üzembe helyeznie, több gazdagép között elosztva (legalább 2). Az Azure dedikált gazdagépekkel több lehetősége van arra, hogy kiépítse az infrastruktúrát a hibák elkülönítési határainak formálásához.

### <a name="use-availability-zones-for-fault-isolation"></a>A Availability Zones használata a hibák elkülönítéséhez

A rendelkezésre állási zónák egy Azure-régióban található egyedi fizikai helyek. Minden rendelkezésreállási zóna egy vagy több, független áramforrással, hűtéssel és hálózatkezelési megoldással ellátott adatközpontból áll. Egy gazdagép-csoport egyetlen rendelkezésre állási zónában jön létre. A létrehozást követően a rendszer az összes gazdagépet a zónán belül helyezi el. Ha magas rendelkezésre állást szeretne biztosítani a zónák között, létre kell hoznia több gazdagép-csoportot (egy zónán belül), és ennek megfelelően kell elosztania a gazdagépeket.

Ha egy rendelkezésre állási zónához rendel hozzá egy gazdagépet, akkor a gazdagépen létrehozott összes virtuális gépet ugyanabban a zónában kell létrehozni.

### <a name="use-fault-domains-for-fault-isolation"></a>Tartalék tartományok használata a hibák elkülönítéséhez

Egy gazdagép hozható létre egy adott tartalék tartományban. A méretezési csoportokban vagy a rendelkezésre állási csoportokban lévő virtuális gépekhez hasonlóan a különböző tartalék tartományokban lévő gazdagépek az adatközpont különböző fizikai állványokra kerülnek. A gazdagépek létrehozásakor meg kell adnia a tartalék tartományok darabszámát. A gazdagépen belüli gazdagépek létrehozásakor minden gazdagéphez rendeljen tartalék tartományt. A virtuális gépek nem igénylik a tartalék tartomány-hozzárendelést.

A tartalék tartományok nem egyeznek a közös elhelyezéssel. Ha a két gazdagép azonos tartalék tartománya van, nem jelenti azt, hogy egymáshoz közel vannak.

A tartalék tartományok hatóköre a gazda csoportra terjed ki. A két gazda csoport közötti affinitást nem szabad feltételezni (kivéve, ha a különböző rendelkezésre állási zónákban vannak).

A különböző tartalék tartományokkal rendelkező gazdagépekre telepített virtuális gépeken a mögöttes felügyelt lemezek több tárolási bélyegzőn lesznek, így növelhetik a hibák elkülönítésének védelmét.

### <a name="using-availability-zones-and-fault-domains"></a>Availability Zones és tartalék tartományok használata

Mindkét képességet együtt használva még több hibatűrést érhet el. Ebben az esetben meg kell adnia a rendelkezésre állási zónát és a tartalék tartományok számát az egyes gazdagépek esetében, hozzá kell rendelnie egy tartalék tartományt a csoport minden gazdagépéhez, és hozzá kell rendelnie egy rendelkezésre állási zónát az egyes virtuális gépekhez

Az [itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) található Resource Manager-minta sablon zónák és tartalék tartományok használatával terjeszti a gazdagépeket a maximális rugalmasság érdekében egy adott régióban.


## <a name="manual-vs-automatic-placement"></a>Manuális és automatikus elhelyezés 

Egy virtuális gép Azure-beli létrehozásakor kiválaszthatja, hogy melyik dedikált gazdagépet kívánja használni. Azt is megteheti, hogy automatikusan elhelyezi a virtuális gépeket a meglévő gazdagépeken egy gazdagépen belül. 

Új gazda-csoport létrehozásakor győződjön meg arról, hogy az automatikus virtuális gép elhelyezése beállítás be van jelölve. A virtuális gép létrehozásakor jelölje ki a gazdagépet, és hagyja, hogy az Azure válassza a legjobb gazdagépet a virtuális géphez. 

Az automatikus elhelyezésre engedélyezett kiszolgálócsoportok nem igénylik az összes virtuális gép automatikus elhelyezését. Továbbra is kiválaszthatja a gazdagépet, még akkor is, ha az automatikus elhelyezés ki van választva a gazdagép-csoport számára. 

### <a name="limitations"></a>Korlátozások

Ismert problémák és korlátozások az automatikus virtuális gép elhelyezésekor:

- A dedikált gazdagépeken nem alkalmazhat Azure Hybrid-előnyöket.
- Nem fogja tudni újratelepíteni a virtuális gépet. 
- Dedikált gazdagépekkel nem lehet Lsv2, NVasv4, NVsv3, Msv2 vagy M sorozatú virtuális gépeket használni 


## <a name="virtual-machine-scale-set-support"></a>Virtuálisgép-méretezési csoport támogatása

A virtuálisgép-méretezési csoportok lehetővé teszik, hogy egyetlen erőforrásként kezelje a virtuális gépek egy csoportját, és a rendelkezésre állást, a felügyeletet, a skálázási és a hangolási szabályzatokat csoportként alkalmazza. A meglévő dedikált gazdagépek is használhatók a virtuálisgép-méretezési csoportokhoz. 

Virtuálisgép-méretezési csoport létrehozásakor megadhat egy meglévő gazdagép-csoportot, amely tartalmazza a dedikált gazdagépeken létrehozott összes virtuálisgép-példányt.

A virtuálisgép-méretezési csoportok dedikált gazdagép-csoportban való létrehozásakor a következő követelmények érvényesek:

- Engedélyezni kell az automatikus virtuális gép elhelyezését.
- A gazdagép-csoport rendelkezésre állási beállításának meg kell egyeznie a méretezési csoporttal. 
    - A regionális méretezési csoportokhoz a (rendelkezésre állási zóna megadása nélkül létrehozott) regionális gazdagépeket kell használni.
    - A gazdagépnek és a méretezési csoportnak ugyanazt a rendelkezésre állási zónát kell használnia. 
    - A gazdagépek szintjének tartalék tartományának meg kell egyeznie a méretezési csoport hibatűrési számával. A Azure Portal lehetővé teszi a méretezési csoport *maximális terjesztésének* megadását, amely az 1. számú tartalék tartomány számát állítja be.
- A dedikált gazdagépeket először létre kell hozni, elegendő kapacitással, és ugyanazokkal a beállításokkal, mint a méretezési csoport zónák és a tartalék tartományok.
- A dedikált gazdagépek által támogatott virtuálisgép-méreteknek meg kell egyezniük a méretezési csoporthoz használt virtuális gépek méretétől.

A dedikált gazdagépek nem minden méretezési és optimalizálási beállítást támogatnak. Alkalmazza a következő beállításokat a méretezési csoportra: 
- A túlzott kiépítés nem ajánlott, és alapértelmezés szerint le van tiltva. Engedélyezheti a túlzott kiépítést, de a méretezési csoport lefoglalása sikertelen lesz, ha a gazda csoport nem rendelkezik az összes virtuális gép kapacitásával, beleértve a túlépített példányokat is. 
- A ScaleSetVM-előkészítési mód használata 
- Földrajzi elhelyezési csoportok használata a közös elhelyezéshez



## <a name="maintenance-control"></a>Karbantartásszabályozás

A virtuális gépeket támogató infrastruktúra esetenként frissíthető a megbízhatóság, a teljesítmény, a biztonság és az új funkciók megkezdése érdekében. Az Azure platform megpróbálja csökkenteni a platform karbantartásának hatását, amikor csak lehetséges, de a *karbantartási szempontból bizalmas* munkaterheléseket használó ügyfelek még néhány másodpercet sem tudnak elviselni, hogy a virtuális gépet be kell fagyasztani vagy le kell kapcsolni a karbantartáshoz.

A **karbantartási ellenőrzés** lehetővé teszi az ügyfeleknek, hogy kihagyhatják a dedikált gazdagépeken ütemezett rendszeres szoftverfrissítéseket, majd egy 35 napos időszakon belül alkalmazza azt a választásuk időpontjában. A karbantartási időszakon belül közvetlenül is alkalmazhatja a karbantartást a gazdagép szintjén, bármilyen sorrendben. Miután a karbantartási időszak véget ért, a Microsoft továbblép, és alkalmazza a függőben lévő karbantartást a gazdagépekre olyan sorrendben, amely nem követi a felhasználó által definiált tartalék tartományokat.

További információ: [a platform frissítéseinek kezelése a karbantartási ellenőrzéssel](./maintenance-control.md).

## <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok

A dedikált gazdagép kiosztása után az Azure hozzárendeli azt a fizikai kiszolgálóhoz. Ez garantálja a kapacitás rendelkezésre állását, ha üzembe kell helyeznie a virtuális gépet. Az Azure a régió (vagy zóna) teljes kapacitását használja a gazdagép fizikai kiszolgálójának kiválasztásához. Ez azt is jelenti, hogy az ügyfelek várhatóan növekedni tudnak a dedikált gazdagépek lábnyomán anélkül, hogy a fürtön kívülről kellene kifogyni.

## <a name="quotas"></a>Kvóták

A dedikált gazdagépek telepítésekor két típusú kvóta használható.

1. Dedikált gazdagép vCPU kvótája. Az alapértelmezett kvóta 3000 vCPU, régiónként.
1. Virtuálisgép-méret család kvótája. Az utólagos **elszámolású előfizetések** esetében például az USA keleti régiójában csak 10 vCPU lehet elérhető az Dsv3-méretekhez. Dsv3 dedikált gazdagép üzembe helyezéséhez a dedikált gazdagép üzembe helyezése előtt a kvóta növelését legalább 64 vCPU kell megadnia. 

A kvóta növeléséhez hozzon létre egy támogatási kérelmet a [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

A dedikált gazdagép kiépítés a dedikált gazdagép-vCPU és a virtuálisgép-család vCPU kvótáját is felhasználja, de nem fogja használni a regionális vCPU.


![Képernyőkép a portál használati és kvóták oldaláról](./media/virtual-machines-common-dedicated-hosts/quotas.png)

További információ: [virtuális gépek vCPU kvótái](./windows/quotas.md).

Az ingyenes próbaverzió és az MSDN-előfizetések nem rendelkeznek kvótával az Azure dedikált gazdagépekhez.

## <a name="pricing"></a>Díjszabás

A felhasználókat a rendszer dedikált gazdagépre terheli, függetlenül attól, hogy hány virtuális gép van telepítve. A havi utasításban a gazdagépek új számlázható erőforrás-típusa jelenik meg. A dedikált gazdagépen lévő virtuális gépek továbbra is megjelennek az utasításban, de a 0. árat is magukban foglalják.

A gazdagép árát a VM-család, a típus (hardver mérete) és a régió alapján állítja be a rendszer. A gazdagép díja a gazdagépen támogatott legnagyobb virtuálisgép-mérethez képest.

A szoftver licencelése, a tárolás és a hálózat használata a gazdagéptől és a virtuális gépektől függetlenül történik. Ezek a Számlázatlan elemek nem változnak.

További információ: [Azure dedikált gazdagép díjszabása](https://aka.ms/ADHPricing).

A költségeket az [Azure dedikált gazdagépek fenntartott példányával](prepay-dedicated-hosts-reserved-instances.md)is elvégezheti.
 
## <a name="sizes-and-hardware-generations"></a>Méretek és hardveres generációk

Egy gazdagéphez van definiálva SKU, és a virtuális gép mérete adatsorozatot és típust jelöl. A különböző méretű virtuális gépeket egyetlen gazdagépen belül is keverheti, ha azok azonos méretű adatsorozattal rendelkeznek. 

A *típus* a hardver generációja. Az azonos virtuálisgép-sorozatok különböző típusai különböző CPU-szállítóktól származnak, és különböző CPU-generációkkal és magok számával rendelkeznek. 

A méretek és a hardver típusok régiónként eltérőek. További információért tekintse meg a gazdagép [díjszabását ismertető oldalt](https://aka.ms/ADHPricing) .

> [!NOTE]
> Ha egy dedikált gazdagép kiosztott, nem módosíthatja a méretet vagy a típust. Ha eltérő típusú méretre van szüksége, létre kell hoznia egy új gazdagépet.  

## <a name="host-life-cycle"></a>Gazdagép életciklusa


Az Azure figyeli és kezeli a gazdagépek állapotát. A következő állapotok lesznek visszaadva a gazdagép lekérdezésekor:

| Állapot   | Leírás       |
|----------|----------------|
| Gazdagép elérhető     | A gazdagépen nincsenek ismert problémák.   |
| A vizsgálat alatt álló gazdagép  | Problémák léptek fel a gazdagépen, amit keresünk. Ez egy átmeneti állapot szükséges ahhoz, hogy az Azure kipróbálja és azonosítani tudja az azonosított probléma hatókörét és alapvető okát. A gazdagépen futó virtuális gépek befolyásolhatják a működését. |
| Gazdagép függőben lévő felszabadítása   | Az Azure nem tudja visszaállítani a gazdagépet Kifogástalan állapotba, és megkéri, hogy telepítse újra a virtuális gépeket a gazdagépről. Ha `autoReplaceOnFailure` engedélyezve van, a virtuális gépek a *szolgáltatás* Kifogástalan állapotba kerülnek. Ellenkező esetben előfordulhat, hogy a virtuális gép olyan gazdagépen fut, amely hamarosan sikertelen lesz.|
| Gazdagép delefoglalt  | Az összes virtuális gép el lett távolítva a gazdagépről. Ez a gazdagép már nem töltődik fel, mivel a hardver elforgatása nem történt meg.   |


## <a name="next-steps"></a>Következő lépések

- A dedikált gazdagépeket [Azure PowerShell](./windows/dedicated-hosts-powershell.md), a [portál](./dedicated-hosts-portal.md)és az [Azure CLI](./linux/dedicated-hosts-cli.md)használatával helyezheti üzembe.

- [Itt](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)található egy minta sablon, amely mindkét zónát és tartalék tartományt használja a maximális rugalmasság érdekében egy régióban.

- A költségeket az [Azure dedikált gazdagépek fenntartott példányával](prepay-dedicated-hosts-reserved-instances.md)is elvégezheti.
