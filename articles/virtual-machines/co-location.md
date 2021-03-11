---
title: Közelségi elhelyezési csoportok
description: További információ az elhelyezési csoportok Azure-beli használatáról.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: conceptual
ms.date: 3/07/2021
ms.reviewer: zivr
ms.openlocfilehash: 1a65a1e4ecd989f3a7c4968c424472c3c6dfe472
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559075"
---
# <a name="proximity-placement-groups"></a>Közelségi elhelyezési csoportok

A virtuális gépek egyetlen régióban való elhelyezése csökkenti a példányok közötti fizikai távolságot. Ha egyetlen rendelkezésre állási zónába helyezi őket, az is fizikailag szorosabban fog működni. Mivel azonban az Azure-lábnyom növekszik, az egyetlen rendelkezésre állási zóna több fizikai adatközpontra is kiterjedhet, ami az alkalmazást érintő hálózati késést eredményezhet. 

Ha a lehető legközelebb szeretné lekérni a virtuális gépeket, a lehető legkevesebb késést kell megvalósítania, egy közelségi elhelyezési csoporton belül kell telepítenie.

A közelségi elhelyezési csoport olyan logikai csoport, amely biztosítja, hogy az Azure számítási erőforrásai fizikailag közel legyenek egymáshoz. A közelségi csoportok olyan munkaterhelések esetén hasznosak, ahol az alacsony késés követelmény.


- Kis késleltetés az önálló virtuális gépek között.
- Kis késleltetés egyetlen rendelkezésre állási csoportba tartozó virtuális gépek és egy virtuálisgép-méretezési csoport között. 
- Kis késleltetés az önálló virtuális gépek, több rendelkezésre állási csoportba tartozó virtuális gépek vagy több méretezési csoport között. A többrétegű alkalmazások összevonásához több számítási erőforrás is tartozhat egyetlen elhelyezési csoportban. 
- Kis késleltetés több alkalmazás-réteg között különböző típusú hardvereszközök használatával. Például, ha a hátteret a rendelkezésre állási csoportban található M-sorozattal és a D sorozatú példányon lévő előtérrel futtatja, egy méretezési csoport egyetlen közelségi elhelyezési csoportjában.


![Proximity elhelyezési csoportok grafikája](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Proximity elhelyezési csoportok használata 

A közelségi elhelyezési csoport egy új erőforrástípus az Azure-ban. Először létre kell hoznia egyet, mielőtt más erőforrásokkal kellene használnia. A létrehozást követően a virtuális gépekkel, rendelkezésre állási csoportokkal vagy virtuálisgép-méretezési csoportokkal használható. Ha a közelségi elhelyezési csoport AZONOSÍTÓját biztosító számítási erőforrásokat hoz létre, a közelségi elhelyezési csoportot kell megadnia. 

Egy meglévő erőforrást is át lehet helyezni egy közelségi elhelyezési csoportba. Ha az erőforrást egy közelségi elhelyezési csoportba helyezi át, akkor először állítsa le (szabadítsa fel) az eszközt, mert az újra üzembe kerül a régió egy másik adatközpontjában, hogy az kielégítse az elhelyezési korlátozást. 

A rendelkezésre állási készletek és a virtuálisgép-méretezési csoportok esetében az egyes virtuális gépek helyett az erőforrás szintjén kell beállítani a közelségi elhelyezési csoportot. 

A közelségi elhelyezési csoport egy kitűzési mechanizmus helyett közös elhelyezési megkötés. Egy adott adatközpontba van rögzítve, amely az első erőforrás központi telepítését használja. Ha a közelségi elhelyezési csoportot használó összes erőforrás le lett állítva (fel van foglalva) vagy törölve lett, már nem rögzített. Ezért, ha a közelségi elhelyezési csoportot több virtuálisgép-sorozattal használja, fontos, hogy az összes szükséges típust megadja a sablonban, amikor lehetséges, vagy egy központi telepítési sorozatot követve, amely javítja a sikeres üzembe helyezés esélyét. Ha a telepítés sikertelen, indítsa újra a telepítést a virtuálisgép-mérettel, amely az első üzembe helyezési méretnél meghiúsult.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Mire számíthat a közelségi elhelyezési csoportok használatakor 
A közelségi elhelyezési csoportok közös helyet foglalnak el ugyanabban az adatközpontban. Mivel azonban a közelségi elhelyezési csoportok további központi telepítési korlátozást jelentenek, lefoglalási hibák léphetnek fel. Néhány felhasználási esetet láthat, ha foglalási hibák merülnek fel a közelségi csoportok használatakor:

- Ha az első virtuális gépet a közelségi elhelyezési csoportban kéri, a rendszer automatikusan kiválasztja az adatközpontot. Bizonyos esetekben előfordulhat, hogy a másik virtuális gép SKU-jának második kérése meghiúsul, ha az nem létezik az adatközpontban. Ebben az esetben a rendszer **OverconstrainedAllocationRequest** -hibát ad vissza. Ennek elkerüléséhez próbálja meg módosítani azt a sorrendet, amelyben az SKU-ket üzembe helyezi, vagy mindkét erőforrást egyetlen ARM-sablonnal telepíti.
-   Rugalmas munkaterhelések esetén, ahol virtuálisgép-példányokat adhat hozzá és távolíthat el, a központi telepítéshez való közelségi csoport megkötése miatt előfordulhat, hogy a kérelem nem felel meg a **AllocationFailure** hibájának. 
- A rugalmasság elérésének egy másik módja a virtuális gépek leállítása (felszabadítása) és szükség szerint történő elindítása. Mivel a rendszer nem tartja fenn a kapacitást, ha leállítja (felszabadítja) a virtuális gépet, az újraindítással **AllocationFailure** hibát okozhat.

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Tervezett karbantartás és közelségi csoportok

A tervezett karbantartási események, például az Azure-adatközpontok hardveres leszerelése potenciálisan érinthetik az erőforrások közelségét az elhelyezési csoportokban. Előfordulhat, hogy az erőforrásokat egy másik adatközpontba helyezik át, ami megszakítja a közelségi elhelyezési csoporthoz kapcsolódó létrehozásakor közös elhelyezés és késési elvárásokat.

### <a name="check-the-alignment-status"></a>Az igazítás állapotának bejelölése

A következő műveleteket végezheti el a közelségi elhelyezési csoportok igazítási állapotának vizsgálatához.


- A közelségi elhelyezési csoport egyhelyes állapotát a portál, a CLI és a PowerShell használatával lehet megtekinteni.

    -   A PowerShell használata esetén a közös elhelyezés állapota Get-AzProximityPlacementGroup parancsmag használatával szerezhető be, ha a választható "-ColocationStatus" paraméterrel is rendelkezik.

    -   A CLI használatakor a közös elhelyezési állapotot a (z) paranccsal lehet megszerezni `az ppg show` a következő opcionális paraméterrel: "--include-közös Location-status".

- Az egyes közelségi elhelyezési csoportok esetében az **egyhelyes állapot** tulajdonság a csoportosított erőforrások aktuális igazítási állapotának összegzését jeleníti meg. 

    - **Igazított**: az erőforrás a közelségi elhelyezési csoport ugyanazon késési keretén belül van.

    - **Ismeretlen**: a virtuálisgép-erőforrások közül legalább egy fel van foglalva. Miután sikeresen megkezdte a visszaállítást, az állapotnak **jobbra** kell állnia.

    - **Nincs igazítva**: legalább egy virtuálisgép-erőforrás nincs igazítva a közelségi elhelyezési csoporttal. A nem igazított erőforrások külön is meghívhatók a tagság szakaszban

- A rendelkezésre állási csoportok esetében a rendelkezésre állási csoport áttekintése lapon tekintheti meg az egyes virtuális gépek igazításával kapcsolatos információkat.

- A méretezési csoportok esetében az egyes példányok igazításával kapcsolatos információkat a méretezési csoport **Áttekintés** oldalának **példányok** lapján tekintheti meg. 


### <a name="re-align-resources"></a>Erőforrások ismételt igazítása 

Ha a közelségi elhelyezési csoport van `Not Aligned` , akkor stop\deallocate, majd újraindíthatja az érintett erőforrásokat. Ha a virtuális gép rendelkezésre állási csoportba vagy méretezési csoportba esik, a rendelkezésre állási csoporton vagy a méretezési csoporton belül minden virtuális gépnek először stopped\deallocated kell lennie az újraindítás előtt.

Ha az üzembe helyezési megkötések miatt lefoglalási hiba merül fel, előfordulhat, hogy az érintett közelségi elhelyezési csoport összes erőforrását először be kell stop\deallocate (beleértve az igazított erőforrásokat), majd újra kell indítania őket az igazítás visszaállításához.

## <a name="best-practices"></a>Ajánlott eljárások 
- A legkisebb késés érdekében használja a közeli elhelyezési csoportokat a gyorsított hálózatkezeléssel együtt. További információ: [Linux rendszerű virtuális gép gyorsított hálózatkezeléssel való létrehozása](../virtual-network/create-vm-accelerated-networking-cli.md) vagy [Windows rendszerű virtuális gép létrehozása gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-powershell.md).
- Minden virtuálisgép-méret üzembe helyezése egyetlen sablonban. Ha el szeretné kerülni, hogy a hardver nem támogatja a szükséges összes virtuálisgép-t és méretet, vegye fel az összes alkalmazási szintet egyetlen sablonba, hogy azok mind egyszerre legyenek telepítve.
- Ha az üzembe helyezést a PowerShell, a CLI vagy az SDK használatával végezi el, lefoglalási hibaüzenetet kaphat `OverconstrainedAllocationRequest` . Ebben az esetben le kell állítania/fel kell szabadítania az összes meglévő virtuális GÉPET, és módosítania kell a telepítési parancsfájlban szereplő sorozatot, hogy a sikertelen virtuálisgép-SKU/-méretekkel kezdődjön. 
- Ha olyan meglévő elhelyezési csoportot kíván újra használni, amelyből a virtuális gépek törölve lettek, várjon, amíg a törlés teljes mértékben befejeződik, mielőtt hozzáadja a virtuális gépeket.
- Ha a késés az első prioritás, a virtuális gépeket egy közelségi elhelyezési csoportba és a teljes megoldásba helyezheti egy rendelkezésre állási zónában. Ha azonban a rugalmasság a legfontosabb prioritás, a példányokat több rendelkezésre állási zónában is eloszthatja (az egyetlen közelségi elhelyezési csoport nem terjedhet ki a zónákra).

## <a name="next-steps"></a>Következő lépések

Virtuális gép üzembe helyezése központi elhelyezési csoportban az [Azure CLI](./linux/proximity-placement-groups.md) vagy a [PowerShell](./windows/proximity-placement-groups.md) használatával

Megtudhatja, hogyan [tesztelheti a hálózati késést](../virtual-network/virtual-network-test-latency.md).

Útmutató a [hálózati átviteli sebesség optimalizálásához](../virtual-network/virtual-network-optimize-network-bandwidth.md).  

Ismerje meg, hogyan [használhatók a Proximity csoportok az SAP-alkalmazásokkal](./workloads/sap/sap-proximity-placement-scenarios.md).