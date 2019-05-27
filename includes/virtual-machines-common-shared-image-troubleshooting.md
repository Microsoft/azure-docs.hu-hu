---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 1476830313296615591a69a2cadd04bcc56b22bc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158689"
---
Ha problémát észlel a megosztott rendszerkép-katalógusokon, rendszerkép-definíciókon vagy rendszerképverziókon végzett műveletek végrehajtásakor, futtassa újból a sikertelen parancsot hibakeresési módban. Hibakeresési mód aktiválása átadásával a **-debug** CLI felülettel és a **-Debug** váltson a PowerShell használatával. Megtaláltuk a hiba, miután kövesse ezt a dokumentumot, a hibák elhárítása.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nem hozható létre egy megosztott lemezkép-katalógusában

Lehetséges okok:

*A katalógus neve érvénytelen.*

Katalógus neveként engedélyezett karakterek:, kis-és nagybetűket, számokat, pontokat és időszakok. A katalógus neve nem tartalmazhat kötőjeleket. Módosítsa a gyűjtemény nevét, és próbálkozzon újra. 

*A katalógus név nem egyedi az előfizetésen belül.*

Válasszon egy másik gyűjtemény nevét, és próbálkozzon újra.


## <a name="unable-to-create-an-image-definition"></a>Nem sikerült létrehozni a rendszerkép definíciójában. 

Lehetséges okok:

*lemezkép-definíció neve érvénytelen.*

A rendszerkép definíciójában engedélyezett karakterek:, kis- és nagybetűket betűket, számokat, pontokat, kötőjeleket, és időszakokra. Módosítsa a lemezkép neve, majd próbálkozzon újra.

*Egy rendszerkép definíciójában létrehozása kötelező tulajdonságai nincsenek feltöltve adattal.*

Például a neve, közzétevő, ajánlat, termékváltozat és operációs rendszer típusa a Tulajdonságok kötelezőek. Győződjön meg arról, ha az összes tulajdonság, hogy átadta.

Győződjön meg arról, hogy a **OSType**, Linux vagy Windows, a rendszerkép definíciójában nem azonos a forrás a lemezkép-verzió létrehozásához használt lemezkép kezelt állapotúként. 


## <a name="unable-to-create-an-image-version"></a>Nem hozható létre egy lemezkép verziója 

Lehetséges okok:

*Lemezkép-verzió neve érvénytelen.*

Lemezkép-verzió engedélyezett karakterek:, számokat és pontokat. Számok belül a 32 bites egész számnak kell lennie. Formátum: *MajorVersion.MinorVersion.Patch*. Módosítsa a lemezkép-verzió neve, majd próbálkozzon újra.

*Nem található forrás felügyelt lemezkép, amelyből a lemezkép-verzió létrehozása folyamatban van.* 

Ellenőrizze, hogy a forrás lemezkép létezik-e, és a rendszerkép verziószámát ugyanabban a régióban van.

*A felügyelt rendszerkép nem fejeződött kiépítése folyamatban.*

Ellenőrizze, hogy a felügyelt forráslemezkép kiépítési állapota **sikeres**.

*A célként megadott régióban listája nem tartalmazza a forrásrégióban.*

A célként megadott régióban szerepelnie kell a lemezkép-verzió a forrásrégióban. Ellenőrizze, hogy az Azure-bA replikálni a lemezkép verziója, amelyre célrégiók listájában is szerepelnek a forrásrégióban.

*A replikáció nem fejeződött be az összes megcélzott régióban.*

Használja a **--bontsa ki a replikáció állapota** jelző ellenőrizheti, ha minden célként megadott régióban replikálás befejeződött. Ha nem, akkor várjon a feladat befejeződésére 6 órát. Ha sikertelen, futtassa a parancsot újból létrehozni, és a lemezkép-verzió replikálása. Ha a rendszerkép verziószámát replikálódnak célrégiók rengeteg, fontolja meg a replikáció fázisban történik.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nem sikerült létrehozni a virtuális gép vagy a méretezési csoport beállítása 

Lehetséges okok:

*A felhasználó megpróbált létrehozni egy virtuális gép vagy virtuálisgép-méretezési csoportot nem rendelkezik az olvasási hozzáférést a rendszerkép verziószámát.*

Forduljon az előfizetés tulajdonosa, és kérje meg őket, olvasási hozzáférés biztosítása a rendszerkép verziószámát, vagy a szülő erőforrások (például a megosztott lemezkép-katalógus vagy a rendszerkép definíciójában) keresztül [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

*A lemezkép-verzió nem található.*

Győződjön meg arról, hogy a régió, hozzon létre egy virtuális gép vagy virtuálisgép-méretezési a kívánt célrégiók a lemezkép-verzió listáját tartalmazza. Ha a régió már célrégiók listájában, majd ellenőrizze a replikációs feladat befejeződött. Használhatja a **- replikáció állapota** jelző ellenőrizheti, ha minden célként megadott régióban replikálás befejeződött. 

*A virtuális gép vagy virtuálisgép-méretezési csoport létrehozása tart sokáig beállítása.*

Ellenőrizze, hogy a **OSType** a kép verziót próbál létrehozni a virtuális gép vagy virtuálisgép-méretezési csoport állítható be van azonos **OSType** a forrás kezelni a rendszerkép verziószámát létrehozásához használt lemezkép. 

## <a name="unable-to-share-resources"></a>Nem lehet megosztott erőforrások

Megosztott lemezkép-katalógusában, a rendszerkép definíciójában és a lemezkép-verzió erőforrások megosztása több előfizetés használata van engedélyezve [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC). 

## <a name="replication-is-slow"></a>A replikálás lassú történik.

Használja a **--bontsa ki a replikáció állapota** jelző ellenőrizheti, ha minden célként megadott régióban replikálás befejeződött. Ha nem, akkor várjon, amíg befejeződik a feladat akár 6 óráig. Ha sikertelen, indítson újra hozhat létre, és a lemezkép-verzió replikálása a parancsot. Ha a rendszerkép verziószámát replikálódnak célrégiók rengeteg, fontolja meg a replikáció fázisban történik.

## <a name="azure-limits-and-quotas"></a>Az Azure-ra vonatkozó korlátok és kvóták 

[Az Azure korlátai és kvóták](https://docs.microsoft.com/azure/azure-subscription-service-limits) minden megosztott lemezkép-katalógusában, rendszerkép definíciójában és képerőforrások verzióra vonatkoznak. Ellenőrizze, hogy az előfizetések keretein belül vannak. 



