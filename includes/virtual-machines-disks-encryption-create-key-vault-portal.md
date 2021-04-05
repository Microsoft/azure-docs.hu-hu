---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/23/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a967777b65c06cf23239a47e8e691fb3a29231b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "88815470"
---
Az ügyfél által felügyelt kulcsok beállítása a lemezekhez megköveteli, hogy az erőforrásokat egy adott sorrendben hozzon létre, ha az első alkalommal végzi el. Először létre kell hoznia és be kell állítania egy Azure Key Vault.

## <a name="set-up-your-azure-key-vault"></a>A Azure Key Vault beállítása

1. Jelentkezzen be az [Azure Portalra](https://aka.ms/diskencryptionupdates).
1. Keresse meg és válassza ki a **kulcstárolókat**.

    [![Képernyőfelvétel a Azure Portal a Keresés párbeszédpanel kibontásával.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-key-vault-portal-search.png)](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-portal-search-expanded.png#lightbox)

    > [!IMPORTANT]
    > Az Azure Key Vault, a lemezes titkosítási készlet, a virtuális gépek, a lemezek és a pillanatképek mindegyikének ugyanabban a régióban kell lennie, és az előfizetés a sikeres telepítéshez.

1. Új Key Vault létrehozásához válassza a **+ Hozzáadás** lehetőséget.
1. Új erőforráscsoport létrehozása.
1. Adja meg a kulcstároló nevét, válasszon ki egy régiót, és válasszon ki egy díjszabási szintet.

    > [!NOTE]
    > A Key Vault-példány létrehozásakor engedélyeznie kell a Soft delete és a kiürítési védelmet. A helyreállítható törlés biztosítja, hogy a Key Vault a megadott megőrzési időtartam (90 nap alapértelmezett értéke) törölt kulcsát tárolja. A védelem kiürítése biztosítja, hogy a törölt kulcsok ne legyenek véglegesen törölve, amíg a megőrzési időszak megszűnik. Ezek a beállítások a véletlen törlés miatt védik az adatok elvesztését. Ezek a beállítások akkor kötelezőek, ha Key Vault használ a felügyelt lemezek titkosításához.

1. Válassza a **felülvizsgálat + létrehozás** lehetőséget, ellenőrizze a beállításokat, majd kattintson a **Létrehozás** gombra.

    ![Képernyőkép a Azure Key Vault létrehozási felületéről. A létrehozott megadott értékek megjelenítése](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-vault.png)

1. Ha a Key Vault üzembe helyezése befejeződött, válassza ki.
1. A **Beállítások** területen válassza a **kulcsok** lehetőséget.
1. Válassza a **készítés/importálás** lehetőséget.

    ![Képernyőkép a Key Vault erőforrás-beállítások panelről. Megjeleníti a beállításokban a generált/importálás gombot.](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-key-vault-generate-settings.png)

1. Hagyja a **kulcs típusát** az **RSA** és az **RSA-kulcs méretének** értékeként **2048**-re.
1. Adja meg a többi kijelölést, majd válassza a **Létrehozás** lehetőséget.

    ![A kulcs létrehozása/importálása gomb kiválasztásakor megjelenő, a Key (létrehozás/importálás) gombra kattintva megjelenő képernyőkép](./media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-key-generate.png)

## <a name="set-up-your-disk-encryption-set"></a>A lemez titkosítási készletének beállítása

1. Keresse meg a **lemezes titkosítási csoportokat** , és jelölje ki.
1. A **lemez titkosítási készletek** paneljén válassza a **+ Hozzáadás** lehetőséget.

    ![Képernyőkép a lemez titkosítási portálján Főképernyőről. A Hozzáadás gomb kiemelése](./media/virtual-machines-disk-encryption-portal/sever-side-encryption-create-disk-encryption-set.png)

1. Válassza ki az erőforráscsoportot, nevezze el a titkosítási csoportot, és válassza ki ugyanazt a régiót, mint a kulcstartó.
1. A **titkosítás típusa** beállításnál válassza az **ügyfél által felügyelt kulcs** melletti titkosítás lehetőséget.

    > [!NOTE]
    > Ha egy adott titkosítási típussal rendelkező lemezes titkosítási készletet hoz létre, az nem módosítható. Ha más titkosítási típust szeretne használni, létre kell hoznia egy új lemezes titkosítási készletet.

1. **A kulcs kiválasztásához kattintson a kattintás gombra**.
1. Válassza ki a korábban létrehozott Key vaultot és kulcsot, valamint a verziót.
1. Kattintson a **kiválasztás** gombra.
1. Válassza az **Áttekintés és létrehozás**, majd a **Létrehozás** lehetőséget.

    ![Képernyőkép a lemez titkosítás-létrehozási paneljéről. Az előfizetés, az erőforráscsoport, a lemez titkosítási készlet neve, a régió és a Key Vault + Key választó megjelenítése.](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-set-blade.png)

1. Nyissa meg a lemez titkosítási készletet a létrehozás befejeződése után, és válassza ki a felugró riasztást.

    ![Képernyőkép a riasztás előugró ablakáról: "lemez, lemezkép vagy pillanatkép lemezes titkosítási készlettel való hozzárendeléséhez engedélyeket kell biztosítania a kulcstartó számára". A folytatáshoz válassza ki ezt a riasztást](./media/virtual-machines-disk-encryption-portal/server-side-encryption-disk-encryption-set-alert-fix.png)

    Két értesítésnek kell megjelennie, és sikeresnek kell lennie. Ez lehetővé teszi a lemezes titkosítási készlet használatát a kulcstartóval.

    ![A Key Vault sikeres engedélyének és szerepkör-hozzárendelésének képernyőképe.](./media/virtual-machines-disk-encryption-portal/disk-encryption-notification-success.png)