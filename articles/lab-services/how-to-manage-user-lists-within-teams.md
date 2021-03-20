---
title: Azure Lab Services felhasználói listáját a csapatokból felügyelheti
description: Ismerje meg, hogyan kezelheti Azure Lab Services felhasználói listáját a csapatokból.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: fa8f96a84be5c4c91e0153216e15963ec0e3d6f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91946649"
---
# <a name="manage-lab-services-user-lists-from-teams"></a>A labor Services felhasználói listájainak kezelése a csapatokból

Ha labort hoz létre a csapatokon belül (lásd: [első lépések és labor Services-tesztkörnyezet létrehozása a csapatokból](how-to-get-started-create-lab-within-teams.md)), a labor-felhasználók listája automatikusan ki lesz töltve és szinkronizálva van a csoport tagságával. A csapat minden tagja, beleértve a tulajdonosokat, a tagokat és a vendégeket is, automatikusan hozzáadja a labor felhasználói listához. Az Azure Lab-szolgáltatások szinkronizálást tart fenn a csoport tagságával, és az automatikus szinkronizálást 24 óránként indítja el. 

## <a name="sync-users"></a>Felhasználók szinkronizálása

A pedagógusok a **szinkronizálás** gomb használatával válthatnak manuális szinkronizálást a csoport tagságának frissítése után. 

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/how-to-manage-users-with-teams/sync-users.png" alt-text="Felhasználók szinkronizálása":::

Az automatikus vagy manuális szinkronizálás befejeződése után az alábbiak érvényesek attól függően, hogy a labor közzé van-e téve.

* Ha a labor legalább egyszer nem lett közzétéve:
    * A felhasználókat a rendszer a csoport tagságának változásai szerint adja hozzá vagy törli a labor felhasználói listából. 
* Ha a labor legalább egyszer közzé lett téve, a felhasználók hozzáadását vagy törlését követően a rendszer automatikusan frissíti a tesztkörnyezet kapacitását.
    * Ha a csapat új kiegészítésekkel bővül, az új virtuális gépek jönnek létre.
    * Ha bármelyik felhasználó törölve lett a csapatból, akkor a társított virtuális gép is törlődik.

## <a name="next-steps"></a>Következő lépések

Ha a sablon virtuális gépe be van állítva, és amikor a pedagógus kiválasztja a sablon közzétételét, a rendszer létrehozza a labor felhasználói listájában lévő felhasználók számával megegyező virtuális gépek számát. Miután a labor közzé lett téve, és virtuális gépek jönnek létre, a rendszer automatikusan regisztrálja a felhasználókat a laborban, és az első bejelentkezéskor a virtuális gépek hozzá lesznek rendelve a Azure Lab Serviceshoz, amikor először hozzáférnek a laphoz **Azure Lab Services** alkalmazással. 

A sablon virtuális gép közzétételéhez lépjen a csapatok labor szolgáltatások ablakra, válassza a **sablon** lap-> **..**  ->  . lehetőséget. **Közzététel**.

A virtuálisgép-készletek kezelésével kapcsolatban lásd: virtuálisgép [-készlet kezelése a csapatoktól a labor Servicesben](how-to-manage-vm-pool-within-teams.md).

### <a name="also-review"></a>További áttekintés

Lásd az alábbi cikkeket:

- [Azure Lab Services használata a csapatokon belül – áttekintés](lab-services-within-teams-overview.md)
- [Első lépések és labor Services-tesztkörnyezet létrehozása a csapatokból](how-to-get-started-create-lab-within-teams.md)
- [Labor Services-ütemtervek létrehozása a csapatokból](how-to-create-schedules-within-teams.md)
- [Hozzáférés egy virtuális géphez (Student View) a labor Servicesben a csapatoktól](how-to-access-vm-for-students-within-teams.md)

