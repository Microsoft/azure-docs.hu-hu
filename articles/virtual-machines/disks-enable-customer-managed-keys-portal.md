---
title: Azure Portal – ügyfél által felügyelt kulcsok engedélyezése az SSE által felügyelt lemezekkel
description: Az ügyfél által felügyelt kulcsok engedélyezése a felügyelt lemezeken a Azure Portalon keresztül.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 7fbcf37620f9d6edfb0f312c2eb09ef2d7994232
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731647"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>A Azure Portal használatával engedélyezheti a kiszolgálóoldali titkosítást az ügyfél által felügyelt kulcsokkal a felügyelt lemezeken

A Azure Disk Storage lehetővé teszi a saját kulcsok kezelését, ha kiszolgálóoldali titkosítást (SSE) használ a felügyelt lemezekhez, ha a lehetőséget választja. Az ügyfelek által felügyelt kulcsokkal és az egyéb felügyelt lemezes titkosítási típusokkal kapcsolatos koncepcionális információk a lemezes titkosítási cikk **ügyfél által felügyelt kulcsok** szakaszában találhatók:

- Linux esetén: [ügyfél által felügyelt kulcsok](./disk-encryption.md#customer-managed-keys)
- Windows esetén: [ügyfél által felügyelt kulcsok](./disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsokra a következő korlátozások vonatkoznak:

- Ha ez a funkció engedélyezve van a lemezen, nem tiltható le.
    Ha ennek megkerülésére van szüksége, az összes olyan felügyelt lemezre át kell másolnia az összes olyan felügyelt lemezt, amely nem használ ügyfél által felügyelt kulcsokat:

    - Linux esetén: [felügyelt lemez másolása](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Windows esetén: [felügyelt lemez másolása](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

A következő fejezetek az ügyfél által felügyelt kulcsok engedélyezését és használatát ismertetik a felügyelt lemezeken:

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

Most, hogy létrehozta és beállította a kulcstartót és a lemez titkosítási készletét, a titkosítás használatával üzembe helyezhet egy virtuális gépet.
A virtuális gép telepítési folyamata hasonló a normál telepítési folyamathoz, az egyetlen különbség, hogy a virtuális gépet ugyanabban a régióban kell telepíteni, mint a többi erőforrást, és Ön úgy dönt, hogy az ügyfél által felügyelt kulcsot használja.

1. Keressen rá **Virtual Machines** , és válassza a **+ Hozzáadás** elemet a virtuális gép létrehozásához.
1. Az **alapszintű** panelen válassza ki ugyanazt a régiót, mint a lemez titkosítási készletét, és Azure Key Vault.
1. Adja meg a többi értéket az **alapszintű** panelen, ahogy szeretné.

    ![Képernyőfelvétel a virtuális gépek létrehozásának élményéről, a régió értékének kiemelésével.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. A **lemezek** panelen válassza a titkosítás inaktív állapotban lehetőséget **egy ügyfél által felügyelt kulccsal**.
1. Válassza ki a lemez titkosítási készletét a **lemez titkosítási készlete** legördülő menüből.
1. Végezze el a többi kijelölést úgy, ahogy szeretné.

    ![Képernyőkép a virtuális gép létrehozási felületéről, a lemezek panelről. A lemez titkosítási készletének legördülő menüje kiemelve.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>Engedélyezés meglévő lemezen

> [!CAUTION]
> A virtuális géphez csatlakoztatott lemezeken a lemez titkosításának engedélyezéséhez le kell állítania a virtuális gépet.
    
1. Navigáljon egy olyan virtuális gépre, amely ugyanabban a régióban található, mint az egyik lemezes titkosítási csoport.
1. Nyissa meg a virtuális gépet, és válassza a **Leállítás** lehetőséget.

    ![Képernyőkép a példaként szolgáló virtuális gép fő átfedéséről, a Leállítás gomb kiemelve.](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. A virtuális gép leállítása után válassza a **lemezek** lehetőséget, majd válassza ki a titkosítani kívánt lemezt.

    ![Képernyőkép a példaként szolgáló virtuális gépről a lemezek panel megnyitásával. Az operációsrendszer-lemez ki van emelve, mint például a kiválasztható lemez.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. Válassza a **titkosítás** lehetőséget, majd válassza a titkosítás lehetőséget az **ügyfél által felügyelt kulccsal** , majd válassza ki a lemez titkosítási készletét a legördülő listából.
1. Kattintson a **Mentés** gombra.

    ![Képernyőkép a példa operációsrendszer-lemezéről. A titkosítás panel meg van nyitva, és az ügyfél által felügyelt kulccsal történő titkosítás van kiválasztva, valamint a példa Azure Key Vault. A beállítások megadása után a Mentés gomb van kiválasztva.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. Ismételje meg ezt a folyamatot a titkosítani kívánt virtuális géphez csatlakoztatott bármely más lemez esetében.
1. Ha a lemezek befejezik a váltást az ügyfél által felügyelt kulcsokra, ha nincsenek olyan csatlakoztatott lemezek, amelyeket titkosítani szeretne, elindíthatja a virtuális gépet.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Következő lépések

- [Az ügyfél által felügyelt kulcsokkal rendelkező titkosított lemezek létrehozásához Azure Resource Manager-sablonok megismerése](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../key-vault/general/overview.md)
- [Gépek replikálása az ügyfél által felügyelt kulcsokkal rendelkező lemezekkel](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [VMware virtuális gépek Azure-ba történő vészhelyreállításának beállítása a PowerShell használatával](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Hyper-V virtuális gépek Azure-ba történő vészhelyreállításának beállítása a PowerShell és az Azure Resource Manager használatával](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)