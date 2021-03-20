---
title: A VMware virtuális gép felderítési hatókörének beállítása Azure Migrate
description: Ismerteti, hogyan állítható be a felderítési hatókör a VMware virtuális gépek felméréséhez és a Azure Migrate-re való áttelepítéshez.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dc5bd178c837deea7a22fb3be5ba438085c0e748
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753552"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Felderítési hatókör beállítása a VMware virtuális gépekhez

Ez a cikk azt ismerteti, hogyan korlátozható a felderített VMware virtuális gépek köre:

- Virtuális gépek felfedése a [Azure Migrate berendezéssel](migrate-appliance-architecture.md) , ha a Azure Migrate: Server Assessment eszközt használja.
- A virtuális gépeknek a [Azure Migrate berendezéssel](migrate-appliance-architecture.md) való felfedezése a Azure Migrate: Server áttelepítési eszköz használata esetén a VMWare virtuális gépek Azure-ba történő áttelepítéséhez.

A készülék beállításakor a vCenter Serverhoz csatlakozik, és elindítja a felderítést. Mielőtt csatlakoztatná a készüléket a vCenter Serverhoz, korlátozhatja a felderítést vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyedi virtuális gépekre vonatkozó mappákra. A hatókör beállításához engedélyeket kell rendelnie arra a fiókra, amelyet a készülék használ a vCenter Server eléréséhez.

## <a name="before-you-start"></a>Előkészületek

Ha még nem állított be olyan vCenter-felhasználói fiókot, amelyet a Azure Migrate a felderítéshez használ, most [értékelje](./tutorial-discover-vmware.md#prepare-vmware) vagy [ügynök nélküli Migrálás](./migrate-support-matrix-vmware-migration.md#agentless-migration)esetén.


## <a name="assign-permissions-and-roles"></a>Engedélyek és szerepkörök kiosztása

A VMware Inventory objektumokhoz a következő két módszer egyikével rendelhet engedélyeket:

- A készülék által használt fiókban rendeljen hozzá egy szerepkört a hatókörhöz használni kívánt objektumokhoz szükséges engedélyekkel.
- Másik lehetőségként rendeljen hozzá egy szerepkört a fiókhoz az adatközpont szintjén, és propagálja az alárendelt objektumokra. Ezután adja meg a fióknak a hatókörben nem kívánt összes objektumhoz **hozzáférési** szerepkört. Ezt a megközelítést nem javasoljuk, mert nehézkes, és elérhetővé teheti a hozzáférés-vezérlést, mivel minden új gyermekobjektum automatikusan megkapja a szülőtől örökölt hozzáférést.

A vCenter VM-mappa szintjén nem lehet hatókör-felderítést felderíteni. Ha a virtuálisgép-mappában lévő virtuális gépekre vonatkozó felderítési hatókörre van szükség, hozzon létre egy felhasználót, és adja meg a hozzáférést egyenként az összes szükséges virtuális géphez. A gazdagép és a fürt mappái támogatottak.


### <a name="assign-a-role-for-assessment"></a>Szerepkör kiosztása az értékeléshez

1. A felderítéshez használt vCenter-fiókban alkalmazza a **csak olvasási** szerepkört minden olyan szülőobjektum esetében, amely a felderíteni és értékelni kívánt virtuális gépeket üzemelteti (gazdagép, fürt, gazdagépek mappa, fürtök mappa, akár Datacenter).
2. Ezeket az engedélyeket a hierarchia alárendelt objektumaira propagálja.

    ![Engedélyek hozzárendelése](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Szerepkör társítása az ügynök nélküli Migrálás számára

1. Az áttelepítéshez használt vCenter-fiókban alkalmazza a felderíteni és áttelepíteni kívánt virtuális gépeket üzemeltető összes szülő objektumra a [szükséges engedélyekkel](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)rendelkező felhasználó által definiált szerepkört.
2. A szerepkört a könnyebben azonosíthatók szerint nevezheti el. Például <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>A virtuálisgép-mappa korlátozásának megoldása

A kiszolgáló-értékelési eszköz jelenleg nem tudja felderíteni a virtuális gépeket, ha a hozzáférés a vCenter virtuálisgép-mappa szintjén történik. Ha a felderítést és az értékelést virtuálisgép-mappák alapján szeretné elvégezni, használja ezt a megkerülő megoldást.

1. Rendeljen írásvédett engedélyeket a felderítéshez és értékeléshez használni kívánt virtuálisgép-mappákban található összes virtuális GÉPHEZ.
2. Csak olvasási hozzáférést biztosítson az összes olyan szülőobjektum számára, amely a virtuális gépek gazdagépét, a fürtöt, a gazdagépek mappáját, a fürtök mappát vagy az adatközpontot tárolja. Nem kell propagálnia az engedélyeket az összes gyermekobjektum számára.
3. A felderítéshez használandó hitelesítő adatok használatához válassza az adatközpontot **gyűjtemény hatókörként**.


A szerepköralapú hozzáférés-vezérlés beállítása biztosítja, hogy a megfelelő vCenter-felhasználói fiók csak a bérlők által meghatározott virtuális gépekhez férhessen hozzá.


## <a name="next-steps"></a>Következő lépések

[A készülék beállítása](how-to-set-up-appliance-vmware.md)