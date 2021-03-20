---
title: Virtuálisgép-készlet kezelése Azure Lab Services csapatokból
description: Megtudhatja, hogyan kezelheti a virtuális gépek készletét Azure Lab Services csapatokból.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: b838e0561bb48b20450e49aaef37baf3c9ecb4d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91946674"
---
# <a name="manage-a-vm-pool-in-lab-services-from-teams"></a>Virtuálisgép-készlet kezelése a labor Servicesben a csapatoktól

A virtuális gép (VM) létrehozása azonnal elindul, amint a sablon virtuális gépe először közzé lett téve. A labor felhasználói listán szereplő felhasználók számát megegyező virtuális gépek lesznek létrehozva. A virtuális gépeket a rendszer automatikusan hozzárendeli a tanulóknak a Azure Lab Servicesba való első bejelentkezésük alkalmával. 

## <a name="publish-a-template-and-manage-a-vm-pool"></a>Sablon közzététele és virtuálisgép-készlet kezelése

A sablon közzétételéhez lépjen a csapatok labor szolgáltatások ablakára, válassza a **sablon** lap-> **..**  ->  . lehetőséget. **Közzététel**.

Ha a sablon virtuális gépe be van állítva, és amikor a pedagógus kiválasztja a sablon közzétételét, a rendszer létrehozza a labor felhasználói listájában lévő felhasználók számával megegyező virtuális gépek számát. Miután a labor közzé lett téve, és virtuális gépek jönnek létre, a rendszer automatikusan regisztrálja a felhasználókat a laborban, és az első bejelentkezéskor a virtuális gépek hozzá lesznek rendelve a Azure Lab Serviceshoz, amikor először hozzáférnek a laphoz **Azure Lab Services** alkalmazással. 

A felhasználói lista szinkronizálásának elindítása után a labor kapacitása (a laborban található virtuális gépek száma) automatikusan frissül a csoport tagságának változásai alapján. Új virtuális gépek lesznek létrehozva, mivel a rendszer új felhasználókat ad hozzá, és a csapatból eltávolított felhasználókhoz hozzárendelt virtuális gépeket is törli. További információ: [felhasználók felügyelete a csapatokon belül](how-to-manage-user-lists-within-teams.md). 

Az oktatók továbbra is hozzáférhetnek a tanuló virtuális gépekhez közvetlenül a virtuálisgép-készlet lapról. És a pedagógusok hozzáférhetnek a **virtuális gépek készlete** lapról vagy a **saját Virtual Machines** gombra (a képernyő jobb felső sarkában). 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-vm-pool-with-teams/vm-pool.png" alt-text="Virtuálisgép-készlet":::

## <a name="next-steps"></a>Következő lépések

Lásd az alábbi cikkeket:

- [Azure Lab Services használata a csapatokon belül – áttekintés](lab-services-within-teams-overview.md)
- [Első lépések és labor Services-tesztkörnyezet létrehozása a csapatokból](how-to-get-started-create-lab-within-teams.md)
- [A labor Services felhasználói listájainak kezelése a csapatokból](how-to-manage-user-lists-within-teams.md)
- [Labor Services-ütemtervek létrehozása a csapatokból](how-to-create-schedules-within-teams.md)
- [Hozzáférés egy virtuális géphez (Student View) a labor Servicesben a csapatoktól](how-to-access-vm-for-students-within-teams.md)


