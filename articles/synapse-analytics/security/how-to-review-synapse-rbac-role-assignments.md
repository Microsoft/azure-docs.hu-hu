---
title: A szinapszis RBAC szerepkör-hozzárendeléseinek áttekintése a szinapszis Studióban
description: Ez a cikk bemutatja, hogyan tekintheti át a szinapszis RBAC szerepkör-hozzárendeléseket a szinapszis Studio használatával
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9065ca9c7638f3d2bda36e4831b81963936f4b45
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100102155"
---
# <a name="how-to-review-synapse-rbac-role-assignments"></a>A szinapszis RBAC szerepkör-hozzárendeléseinek áttekintése

A szinapszis RBAC szerepkörei lehetővé teszik a felhasználók, csoportok és más rendszerbiztonsági tag engedélyeinek hozzárendelését a szinapszis-erőforrások hozzáférésének és használatának engedélyezéséhez.  [További információ](./synapse-workspace-synapse-rbac.md)

Ez a cikk a munkaterület aktuális szerepkör-hozzárendeléseinek áttekintését ismerteti.

Bármely szinapszis RBAC szerepkörrel listázhatja a szinapszis RBAC szerepkör-hozzárendeléseket az összes hatókörhöz, beleértve azokat az objektumokat is, amelyekhez nincs hozzáférése. Csak a szinapszis-rendszergazda biztosíthat RBAC-hozzáférést.  

>[!Note]
>A vendég felhasználók (egy másik AD-bérlőből származó felhasználók) nem láthatják és nem kezelhetik a szerepkör-hozzárendeléseket, még akkor sem, ha a szinapszis rendszergazdai szerepkört rendelték    

## <a name="open-synapse-studio"></a>A szinapszis Studio megnyitása  

A szerepkör-hozzárendelések áttekintéséhez először [nyissa meg a szinapszis Studio alkalmazást](https://web.azuresynapse.net/) , és válassza ki a munkaterületet. 

![Bejelentkezés a munkaterületre](./media/common/login-workspace.png) 
 
 Miután megnyitotta a munkaterületet, válassza a bal oldali **kezelés** hubot, majd a **Biztonság** szakaszt, és válassza a **hozzáférés-vezérlés** lehetőséget. 

 ![Válassza Access Control a biztonság szakaszban a bal oldalon](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

## <a name="review-workspace-role-assignments"></a>Munkaterület szerepkör-hozzárendeléseinek áttekintése

A hozzáférés-vezérlési képernyő felsorolja a munkaterület összes aktuális szerepkör-hozzárendelését, szerepkör szerint csoportosítva. Minden hozzárendelés tartalmazza a résztvevő nevét, a résztvevő típusát, a szerepkört és annak hatókörét.

![Access Control képernyő](./media/how-to-review-synapse-rbac-role-assignments/access-control-assignments.png)

Ha egy rendszerbiztonsági tag ugyanazt a szerepkört rendeli hozzá a különböző hatókörökhöz, több hozzárendelést fog látni a rendszerbiztonsági tag számára, egyet pedig minden hatókörhöz.  

Ha egy szerepkör hozzá van rendelve egy biztonsági csoporthoz, a rendszer explicit módon hozzárendeli a csoporthoz, de a fölérendelt csoportoktól nem örökölt szerepköröket.  

A listát egyszerű név vagy e-mail-cím alapján szűrheti, és szelektív módon szűrheti az Objektumtípusok, a szerepkörök és a hatókörök listáját. adja meg a nevet vagy az e-mail-aliast a Name (név) szűrőben az Önhöz hozzárendelt szerepkörök megtekintéséhez. Csak a szinapszis-rendszergazdák módosíthatják a szerepköröket.

>[!Important] 
>Ha közvetlenül vagy közvetve egy olyan csoport tagja, amely hozzárendelt szerepköröket tartalmaz, előfordulhat, hogy a rendszer nem jelenít meg engedélyeket.

>[!tip]
>A csoporttagságok a Azure Portal Azure Active Directory használatával találhatók meg.  

Ha új munkaterületet hoz létre, Ön és a munkaterület MSI egyszerű szolgáltatása automatikusan megkapja a szinapszis rendszergazdai szerepkört a munkaterület hatókörében.

## <a name="next-steps"></a>Következő lépések

Ismerje meg [, hogyan kezelheti a SZINAPSZIS RBAC szerepkör-hozzárendeléseit](./how-to-manage-synapse-rbac-role-assignments.md).

[Az adott feladatok](./synapse-workspace-understand-what-role-you-need.md) elvégzéséhez szükséges szerepkör megismerése