---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-resource-manager
author: rockboyfor
manager: digimobile
ms.service: azure-resource-manager
ms.topic: include
origin.date: 02/16/2018
ms.date: 04/30/2018
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: f77a5d482c3f8632a3d86bd8e027fbb4418168c3
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122907"
---
Győződjön meg arról, hogy az intézmény felhasználói megfelelő hozzáférési szinttel rendelkeznek ezekhez az erőforrásokhoz. Nem ajánlott korlátlan hozzáférést nyújtania a felhasználóknak, de fontos biztosítania, hogy el tudják végezni a munkájukat. Szerepköralapú hozzáférés-vezérlés (RBAC) lehetővé teszi egy hatókörben bizonyos műveletek elvégzéséhez jogosult felhasználók kezelését. Egy szerepkör engedélyezett műveletek egy csoportját határozza meg. Rendelje hozzá a szerepkört egy hatókört, és adja meg, hogy mely felhasználók tartoznak a hatókör szerepkörhöz.

A hozzáférés-vezérlési stratégia megtervezésekor csak a munkájuk elvégzéséhez minimálisan szükséges jogosultságokat biztosítsa a felhasználók számára. Az alábbi képen az RBAC használatának egy javasolt mintája látható.

![Hatókör](./media/resource-manager-governance-rbac/role-examples.png)

Nincsenek három szerepkört, amely a alkalmazni az összes erőforrás - tulajdonos, közreműködő és olvasó. A tulajdonosi szerepkörhöz rendelt összes fiókot kell szigorúan ellenőrzött és a ritkán használt. Az Olvasó szerepkör felhasználóknak csak a megoldások állapotának megfigyelését kell engedélyezni.

A legtöbb felhasználók kapjanak [erőforrás-specifikus szerepkörökhöz](../articles/role-based-access-control/built-in-roles.md) vagy [egyéni szerepkörök](../articles/role-based-access-control/custom-roles.md) az előfizetés vagy a resource csoport szintjén. Ezek a szerepkörök szorosan meghatározzák az engedélyezett műveleteket. Ezeket a szerepköröket rendelhet a felhasználók, hozzáférést biztosít a szükséges felhasználók túl sok a vezérlő lehetővé tevő nélkül. Egynél több szerepkört hozzárendelheti egy fiókot, és, hogy a felhasználó a kombinált engedélyekkel szerepkörök beolvasása. Az erőforráscsoport szintjén hozzáférést gyakran túl korlátozó a felhasználók számára, de egy adott feladat készült automatikus folyamat is dolgozhat.

### <a name="who-can-assign-roles"></a>Akik rendelhet szerepköröket

A szerepkör-hozzárendelések létrehozásához és eltávolításához a felhasználóknak `Microsoft.Authorization/roleAssignments/*` hozzáféréssel kell rendelkezniük. Ez a hozzáférés a tulajdonosi vagy a felhasználói hozzáférés rendszergazdájának szerepkörével adható meg.
<!--ms.date: 04/30/2018-->