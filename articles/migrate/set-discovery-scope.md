---
title: A VMware rendszerű kiszolgálók felderítési hatókörének beállítása Azure Migrate
description: Leírja, hogyan kell beállítani a felderítési hatókört a VMware Assessment és a Azure Migrate használatával történő áttelepítés során üzemeltetett kiszolgálókhoz.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775357"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Felderítési hatókör beállítása a kiszolgálók számára a VMware-környezetben

Ez a cikk azt ismerteti, hogyan lehet korlátozni a VMware-környezetben található kiszolgálók felderítésének hatókörét a következő esetekben:

- Kiszolgálók felderítése a [Azure Migrate berendezéssel](migrate-appliance-architecture.md) , amikor a Azure Migrate: Discovery and Assessment eszközt használja.
- Kiszolgálók felfedése a [Azure Migrate berendezéssel](migrate-appliance-architecture.md) a Azure Migrate: Server áttelepítési eszköz használata esetén a VMware-környezetből az Azure-ba irányuló kiszolgálók ügynök nélküli áttelepítése esetén.

A készülék beállításakor a vCenter Serverhoz csatlakozik, és elindítja a felderítést. Mielőtt csatlakoztatná a készüléket a vCenter Serverhoz, korlátozhatja a felderítést vCenter Server adatközpontokra, fürtökre, fürtökre, gazdagépekre, gazdagépekre vagy egyedi kiszolgálókra vonatkozó mappára. A hatókör beállításához engedélyeket kell rendelnie arra a fiókra, amelyet a készülék használ a vCenter Server eléréséhez.

## <a name="before-you-start"></a>Előkészületek

Ha még nem állított be olyan vCenter-felhasználói fiókot, amelyet a Azure Migrate a felderítéshez használ, most [értékelje](./tutorial-discover-vmware.md#prepare-vmware) vagy [ügynök nélküli Migrálás](./migrate-support-matrix-vmware-migration.md#agentless-migration)esetén.


## <a name="assign-permissions-and-roles"></a>Engedélyek és szerepkörök kiosztása

A VMware Inventory objektumokhoz a következő két módszer egyikével rendelhet engedélyeket:

- A készülék által használt fiókban rendeljen hozzá egy szerepkört a hatókörhöz használni kívánt objektumokhoz szükséges engedélyekkel.
- Másik lehetőségként rendeljen hozzá egy szerepkört a fiókhoz az adatközpont szintjén, és propagálja az alárendelt objektumokra. Ezután adja meg a fióknak a hatókörben nem kívánt összes objektumhoz **hozzáférési** szerepkört. Ezt a megközelítést nem javasoljuk, mert nehézkes, és elérhetővé teheti a hozzáférés-vezérlést, mivel minden új gyermekobjektum automatikusan megkapja a szülőtől örökölt hozzáférést.

A vCenter-kiszolgáló mappa szintjén nem lehet hatókör-felderítést felderíteni. Ha meg kell adni a hatókört a mappákban található kiszolgálókon, hozzon létre egy felhasználót, és adja meg a hozzáférést egyenként a szükséges kiszolgálókhoz. A gazdagép és a fürt mappái támogatottak.


### <a name="assign-a-role-for-assessment"></a>Szerepkör kiosztása az értékeléshez

1. A felderítéshez használt vCenter-fiókban alkalmazza a **csak olvasási** szerepkört a felderíteni és értékelni kívánt kiszolgálókat üzemeltető összes szülőobjektum számára (gazdagép, fürt, gazdagépek mappa, fürtök mappa, akár Datacenter).
2. Ezeket az engedélyeket a hierarchia alárendelt objektumaira propagálja.

    ![Engedélyek hozzárendelése](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Szerepkör társítása az ügynök nélküli Migrálás számára

1. Az áttelepítéshez használt vCenter-fiókban alkalmazza a [szükséges engedélyekkel](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)rendelkező felhasználó által definiált szerepkört a felderíteni és áttelepíteni kívánt kiszolgálókat futtató összes szülő objektumra.
2. A szerepkört a könnyebben azonosíthatók szerint nevezheti el. Például <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>A kiszolgálói mappa korlátozásának megkerülése

Jelenleg a Azure Migrate: felderítési és értékelési eszköz nem tudja felderíteni a kiszolgálókat, ha a hozzáférés a vCenter-kiszolgáló mappa szintjén van megadva. Ha a felderítést és az értékelést kiszolgálói mappák alapján szeretné elvégezni, használja ezt a megkerülő megoldást.

1. Rendeljen írásvédett engedélyeket a felderítéshez és értékeléshez használni kívánt mappákban található összes kiszolgálón.
2. Csak olvasási hozzáférést biztosítson az összes olyan szülőobjektum számára, amely a kiszolgálók gazdagépét, a fürtöt, a gazdagépek mappáját, a fürtök mappáját és az adatközpontot tárolja. Nem kell propagálnia az engedélyeket az összes gyermekobjektum számára.
3. A felderítéshez használandó hitelesítő adatok használatához válassza az adatközpontot **gyűjtemény hatókörként**.


A szerepköralapú hozzáférés-vezérlés beállítása biztosítja, hogy a megfelelő vCenter-felhasználói fiók csak a bérlő-specifikus kiszolgálókhoz férhessen hozzá.


## <a name="next-steps"></a>Következő lépések

[A készülék beállítása](how-to-set-up-appliance-vmware.md)