---
title: Törölt tárfiók helyreállítása
titleSuffix: Azure Storage
description: Megtudhatja, hogyan állíthatja helyre a törölt Storage-fiókot a Azure Portalon belül.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97357346"
---
# <a name="recover-a-deleted-storage-account"></a>Törölt tárfiók helyreállítása

Előfordulhat, hogy egy törölt Storage-fiók a Azure Portalon belül bizonyos esetekben helyreáll. A Storage-fiók helyreállításához a következő feltételeknek kell teljesülnie:

- A Storage-fiók az elmúlt 14 napban törölve lett.
- A Storage-fiók a Azure Resource Manager üzembehelyezési modellel lett létrehozva.
- Nem jött létre új Storage-fiók ugyanazzal a névvel, mert az eredeti fiókot törölték.

A törölt Storage-fiók helyreállításának megkísérlése előtt győződjön meg arról, hogy az adott fiókhoz tartozó erőforráscsoport létezik. Ha az erőforráscsoport törölve lett, újra létre kell hoznia. Egy erőforráscsoport helyreállítása nem lehetséges. További információ: [erőforráscsoportok kezelése](../../azure-resource-manager/management/manage-resource-groups-portal.md).

Ha a törölt Storage-fiók az ügyfél által felügyelt kulcsokat Azure Key Vault és a kulcstartót is törölték, akkor a Storage-fiók visszaállítása előtt vissza kell állítania a kulcstárolót. További információ: [Azure Key Vault Recovery áttekintése](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> A törölt Storage-fiók helyreállítása nem garantált. A helyreállítás a legjobb próbálkozás. A Microsoft azt javasolja, hogy az erőforrások zárolása megakadályozza a fiókok véletlen törlését. Az erőforrás-zárolásokkal kapcsolatos további információkért lásd: [erőforrások zárolása a módosítások megakadályozása érdekében](../../azure-resource-manager/management/lock-resources.md).
>
> Egy másik ajánlott eljárás a fiókok véletlen törlésének elkerülése érdekében, hogy korlátozza azon felhasználók számát, akik engedéllyel rendelkeznek a fiók törléséhez szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával. További információ: [ajánlott eljárások az Azure RBAC](../../role-based-access-control/best-practices.md)-hoz.

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>Törölt fiók helyreállítása a Azure Portal

A törölt Storage-fiókok egy másik Storage-fiókból történő helyreállításához kövesse az alábbi lépéseket:

1. Navigáljon a Azure Portal meglévő Storage-fiókjához tartozó áttekintő oldalra.
1. A **támogatás + hibaelhárítás** szakaszban válassza a **Törölt fiók helyreállítása** lehetőséget.
1. A legördülő listából válassza ki a helyreállítani kívánt fiókot az alábbi ábrán látható módon. Ha a helyreállítani kívánt Storage-fiók nem szerepel a legördülő listában, akkor nem állítható helyre.

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="A Storage-fiók helyreállítását bemutató képernyőkép Azure Portal":::

1. A fiók visszaállításához kattintson a **helyreállítás** gombra. A portálon megjelenik egy értesítés arról, hogy a helyreállítás folyamatban van.

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>Törölt fiók helyreállítása támogatási jegyen keresztül

1. A Azure Portal navigáljon a **Súgó + támogatás** elemre.
1. Válassza az **Új támogatási kérelem** lehetőséget.
1. Az **alapvető beállítások** lap **probléma típusa** mezőjében válassza a **technikai** lehetőséget.
1. Az **előfizetés** mezőben válassza ki azt az előfizetést, amely a törölt Storage-fiókot tárolja.
1. A **szolgáltatás** mezőben válassza a **Storage-fiókok kezelése** lehetőséget.
1. Az **erőforrás** mezőben válassza ki a Storage-fiók erőforrásait. A törölt Storage-fiók nem fog megjelenni a listában.
1. Adja meg a probléma rövid összefoglalását.
1. A **probléma típusa** mezőben válassza a **Törlés és helyreállítás** lehetőséget.
1. A **probléma altípusa** mezőben válassza a **törölt Storage-fiók helyreállítása** lehetőséget. Az alábbi képen egy példa látható az **alapismeretek** lap kitöltésére:

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="Képernyőfelvétel a Storage-fiókok támogatási jegyen keresztüli helyreállításáról – alapismeretek lap":::

1. Ezután navigáljon a **megoldások** lapra, és válassza az **ügyfél által vezérelt Storage-fiók helyreállítása** lehetőséget az alábbi ábrán látható módon:

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="Képernyőfelvétel a Storage-fiókoknak a támogatási jegy – megoldások lapon való helyreállításáról":::

1. A legördülő listából válassza ki a helyreállítani kívánt fiókot az alábbi ábrán látható módon. Ha a helyreállítani kívánt Storage-fiók nem szerepel a legördülő listában, akkor nem állítható helyre.

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="A Storage-fiók támogatási jegyen keresztüli helyreállítását bemutató képernyőkép":::

1. A fiók visszaállításához kattintson a **helyreállítás** gombra. A portálon megjelenik egy értesítés arról, hogy a helyreállítás folyamatban van.

## <a name="next-steps"></a>Következő lépések

- [Tárfiókok áttekintése](storage-account-overview.md)
- [Tárfiók létrehozása](storage-account-create.md)
- [Frissítés általános célú v2-tárfiókra](storage-account-upgrade.md)
- [Azure Storage-fiók áthelyezése másik régióba](storage-account-move.md)