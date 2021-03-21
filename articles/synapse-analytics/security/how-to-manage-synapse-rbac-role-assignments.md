---
title: Szinapszis RBAC-hozzárendeléseinek kezelése a szinapszis Studióban
description: Ez a cikk a szinapszis RBAC szerepköreinek hozzárendelését és visszavonását ismerteti a rendszerbiztonsági tag HRE
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: bed0c00b8cb5718456302dff06e98ff2f7f2b4e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102189"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>A szinapszis RBAC szerepkör-hozzárendeléseinek kezelése a szinapszis Studióban

A szinapszis RBAC szerepköröket használ a felhasználók, csoportok és más rendszerbiztonsági tag engedélyeinek hozzárendeléséhez, hogy engedélyezze a szinapszis-erőforrások és-összetevők hozzáférését és használatát.  [További információ](./synapse-workspace-synapse-rbac.md)

Ez a cikk bemutatja, hogyan adhat hozzá és törölhet szinapszis RBAC szerepkör-hozzárendeléseket.

>[!Note]
>- A szinapszis RBAC szerepkör-hozzárendelések kezeléséhez rendelkeznie kell a szinapszis rendszergazdai szerepkörrel a munkaterületen, vagy egy alacsonyabb szintű hatókörben, amely tartalmazza a kezelni kívánt objektumokat. Ha a munkaterületen a szinapszis-rendszergazda, a munkaterület összes objektumához hozzáférést biztosíthat. 
>- Egy másik AD-bérlő **vendég felhasználói** nem láthatják vagy kezelhetik a szerepkör-hozzárendeléseket, még akkor is, ha a szinapszis rendszergazdai szerepkört rendelték hozzájuk.
>- Annak érdekében, hogy a munkaterülethez ne lehessen hozzáférni, ha nem rendel hozzá és nem érhető el a szinapszis-rendszergazdák, a munkaterületen lévő **Azure RBAC** szerepkör-hozzárendelések kezeléséhez szükséges engedélyekkel rendelkező felhasználók is kezelhetik a szinapszis **RBAC** szerepkör-hozzárendeléseket, így a szinapszis-rendszergazda vagy más szinapszis szerepkör-hozzárendelések hozzáadásával is rendelkezhetnek.
>- Az SQL-készletek elérését az SQL-engedélyek segítségével kezelheti.  A szinapszis-rendszergazda és a szinapszis SQL-rendszergazdai szerepkörök kivételével a szinapszis RBAC szerepkörei nem biztosítanak hozzáférést az SQL-készletekhez.

>[!important]
>- A szinapszis RBAC szerepkör-hozzárendelésein végrehajtott módosítások 2-5 percet is igénybe vehetnek. 
>- Ha a biztonsági csoportok tagságának módosításával a szinapszis RBAC-engedélyeket kezeli, a tagságot a Azure Active Directory használatával felügyeli.  A csoporttagságok módosítása 10-15 percet vagy hosszabb időt is igénybe vehet.

## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása  

Ha szerepkört szeretne hozzárendelni egy felhasználóhoz, csoporthoz, egyszerű szolgáltatáshoz vagy felügyelt identitáshoz, először [nyissa meg a szinapszis studiót](https://web.azuresynapse.net/) , és válassza ki a munkaterületet. 

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png) 
 
 Miután megnyitotta a munkaterületet, bontsa ki a **Biztonság** szakaszt a bal oldalon, és válassza a **hozzáférés-vezérlés** lehetőséget. 

 ![Válassza Access Control a biztonság szakaszban a bal oldalon](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

A hozzáférés-vezérlési képernyő felsorolja az aktuális szerepkör-hozzárendeléseket.  A listát egyszerű név vagy e-mail-cím alapján szűrheti, és szelektív módon szűrheti a benne foglalt objektumtípusok, szerepkörök vagy hatókörök listáját. Ebből a képernyőből szerepkör-hozzárendeléseket adhat hozzá vagy távolíthat el.  

## <a name="add-a-synapse-role-assignment"></a>Szinapszis szerepkör-hozzárendelés hozzáadása

A hozzáférés-vezérlési képernyőn válassza a **+ Hozzáadás** lehetőséget az új szerepkör-hozzárendelés létrehozásához.

![Új szerepkör-hozzárendelés létrehozásához kattintson a + Hozzáadás elemre.](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

A szerepkör-hozzárendelés hozzáadása lapon létrehozhat szerepkör-hozzárendeléseket a munkaterület hatóköre vagy a munkaterület elem hatókörében. 

## <a name="add-workspace-scoped-role-assignment"></a>Munkaterület hatókörű szerepkör-hozzárendelésének hozzáadása

Először válassza ki a **munkaterület** elemet a hatókör területen, majd válassza a **szinapszis RBAC szerepkört**.  Válassza ki a szerepkörhöz hozzárendelni kívánt **rendszerbiztonsági tag (eke)** t, majd hozza létre a szerepkör-hozzárendelés (eke) t. 

![Munkaterület szerepkör-hozzárendelés hozzáadása – szerepkör kiválasztása](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

A hozzárendelt szerepkör a munkaterület összes vonatkozó objektumára érvényes lesz.

## <a name="add-workspace-item-scoped-role-assignment"></a>Munkaterület-elem hatókörű szerepkör-hozzárendelésének hozzáadása

Ha egy szerepkört finomabb hatókörben szeretne hozzárendelni, válassza ki a **munkaterület elemet** a hatókör területen, majd válassza ki a hatóköri **elem típusát**.       

![Munkaterület-elem szerepkör-hozzárendelésének hozzáadása – elem típusának kiválasztása](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

Válassza ki a hatókörként használni kívánt adott **elemet** , majd válassza ki a legördülő listából azt a **szerepkört** , amelyet hozzá szeretne rendelni.  A legördülő lista csak azokat a szerepköröket sorolja fel, amelyek a kiválasztott elemtípus esetében érvényesek. [További információ](./synapse-workspace-synapse-rbac.md).  

![Munkaterület-elem szerepkör-hozzárendelésének hozzáadása – szerepkör kiválasztása](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
Ezután **válassza ki azokat a rendszerbiztonsági tag (eke)** t, amelyekhez hozzá szeretné rendelni a szerepkört.  Több résztvevőt is kijelölhet iteratív.  A szerepkör-hozzárendelés (ek) létrehozásához kattintson az **alkalmaz** gombra.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Szinapszis RBAC szerepkör-hozzárendelésének eltávolítása

A szinapszis RBAC-hozzáférés visszavonásához el kell távolítania a megfelelő szerepkör-hozzárendeléseket.  A hozzáférés-vezérlési képernyőn a szűrők segítségével keresse meg az eltávolítani kívánt szerepkör-hozzárendelés (eke) t.  Jelölje ki a szerepkör-hozzárendeléseket, majd válassza a **hozzáférés eltávolítása** lehetőséget.   

![Szerepkör-hozzárendelés törlése a hozzáférés eltávolításához](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

Ne feledje, hogy a szerepkör-hozzárendelések módosítása 2-5 percet vesz igénybe.   

## <a name="next-steps"></a>Következő lépések

[Az általános feladatok végrehajtásához szükséges szinapszis RBAC szerepköreinek megismerése](./synapse-workspace-understand-what-role-you-need.md)