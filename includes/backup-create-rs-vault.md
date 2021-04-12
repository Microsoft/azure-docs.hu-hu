---
title: fájl belefoglalása
description: fájlbefoglalás
services: backup
author: v-amallick
manager: carmonm
ms.service: backup
ms.topic: include
ms.date: 10/18/2018
ms.author: v-amallick
ms.custom: include file
ms.openlocfilehash: c126c349897016a00753b402b696c60fdd7b1b2e
ms.sourcegitcommit: af6eba1485e6fd99eed39e507896472fa930df4d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2021
ms.locfileid: "106294166"
---
## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló egy olyan felügyeleti entitás, amely eltárolja az idők során létrehozott biztonsági mentéseket, és egy felületet biztosít a biztonsági mentésekkel kapcsolatos műveletek végrehajtásához. Ezek közé tartoznak az igény szerinti biztonsági másolatok készítése, a visszaállítások végrehajtása, illetve a biztonsági mentési szabályzatok létrehozása.

Recovery Services-tároló létrehozásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az előfizetésébe az [Azure Portalon](https://portal.azure.com/).

1. A bal oldali menüben válassza a **Minden szolgáltatás** lehetőséget.

    ![Válassza a Minden szolgáltatás elemet](./media/backup-create-rs-vault/click-all-services.png)

1. A **Minden szolgáltatás** párbeszédpanelen írja be a következőt: *Recovery Services*. A rendszer a megadott kulcsszavak alapján szűri az erőforrások listáját. Az erőforrások listájából válassza a **Recovery Services-tárolók** elemet.

    ![Recovery Services-tárolók megadása és kiválasztása](./media/backup-create-rs-vault/all-services.png)

    Megjelenik az előfizetésben található Recovery Services-tárolók listája.

1. A **Recovery Services-tárolók** irányítópultján kattintson a **Hozzáadás** elemre.

    ![Recovery Services-tároló hozzáadása](./media/backup-create-rs-vault/add-button-create-vault.png)

    Megnyílik a **Recovery Services-tároló** párbeszédpanel. Adja meg a **Név**, **Előfizetés**, **Erőforráscsoport** és **Hely** mező értékét.

    ![Recovery Services-tároló konfigurálása](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Név**: Adjon meg egy, a tárolót azonosító rövid nevet. A névnek egyedinek kell lennie az Azure-előfizetésben. Olyan nevet adjon meg, amely legalább 2, legfeljebb 50 karakterből áll. A névnek egy betűvel kell kezdődnie, és csak betűket, számokat és kötőjeleket tartalmazhat.
   - **Előfizetés**: Válassza ki a használni kívánt előfizetést. Ha csak egyetlen előfizetés tagja, azt a nevet fogja látni. Ha nem biztos benne, hogy melyik előfizetést szeretné használni, használja az alapértelmezett (javasolt) előfizetést. Csak akkor lesz több választási lehetőség, ha a munkahelyi vagy iskolai fiók több Azure-előfizetéshez van társítva.
   - **Erőforráscsoport**: Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Az előfizetésben elérhető erőforráscsoportok listájának megjelenítéséhez válassza ki a **Meglévő használata** elemet, majd válasszon egy erőforrást a legördülő listából. Új erőforráscsoport létrehozásához kattintson az **Új létrehozása** elemre, majd adjon meg egy nevet. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../articles/azure-resource-manager/management/overview.md).
   - **Hely**: Válassza ki a tároló földrajzi régióját. Az adatforrások védelmére létrehozott tárolónak ugyanabban a régióban *kell* lennie, mint az adott adatforrásnak.

      > [!IMPORTANT]
      > Ha nem biztos az adatforrás helyében, zárja be a párbeszédpanelt. Nyissa meg az erőforrások listáját a portálon. Ha több régióban rendelkezik adatforrásokkal, minden egyes régióban hozzon létre egy Recovery Services-tárolót. Hozza létre a tárolót az első helyen, mielőtt létrehozná a következő tárolót egy másik helyen. Az adatok biztonsági másolatának tárolásához nincs szükség tárfiókok megadására. A Recovery Services-tároló és az Azure Backup szolgáltatás ezt automatikusan kezeli.
      >
      >

1. Az értékek megadása után válassza a **Felülvizsgálat + létrehozás** elemet.

    ![A Recovery Services-tároló létrehozása folyamatban lévő felülvizsgálat + létrehozás gombot megjelenítő képernyőkép.](./media/backup-create-rs-vault/review-and-create.png)

1. Amikor készen áll a Recovery Services-tároló létrehozására, válassza a **Létrehozás** elemet.

    ![Recovery Services-tároló létrehozása](./media/backup-create-rs-vault/click-create-button.png)

    A Recovery Services-tároló létrehozása eltarthat egy ideig. Figyelje az állapotértesítéseket a portál jobb felső sarkában található **Értesítések** területen. Miután a tároló létrejött, megjelenik a Recovery Services-tárolók listájában. Ha nem látja a tárat, válassza a **Frissítés** lehetőséget.

     ![Biztonsági mentési tárak listájának frissítése](./media/backup-create-rs-vault/refresh-button.png)

>[!IMPORTANT]
> Javasoljuk, hogy a tárban a biztonsági mentések konfigurálása előtt tekintse át a **Tárolóreplikáció típusa** és a **Biztonsági beállítások** alapértelmezett beállításait. További információt a [Tárhely-redundancia beállítása](../articles/backup/backup-create-rs-vault.md#set-storage-redundancy) szakaszban talál.