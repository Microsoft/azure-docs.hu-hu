---
title: Az Azure Managed Disks kiszolgálóoldali titkosítása
description: Az Azure Storage védi az adatait úgy, hogy titkosítja a nyugalmát, mielőtt megőrzi azt a Storage-fürtökön. Az ügyfél által felügyelt kulcsokkal kezelheti a titkosítást a saját kulcsaival, vagy a Microsoft által felügyelt kulcsokat a felügyelt lemezek titkosítására is használhatja.
author: roygara
ms.date: 03/02/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 5695b21a09199a46ab38d887f2127a61507426d3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562792"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure Disk Storage kiszolgálóoldali titkosítása

A kiszolgálóoldali titkosítás (SSE) védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az SSE alapértelmezés szerint automatikusan titkosítja az Azure Managed Disks (operációs rendszer és adatlemezek) tárolt adatait a felhőben való megőrzéskor. 

Az Azure Managed Disks szolgáltatásban tárolt adatforgalom transzparens módon, 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), az egyik legerősebb blokk titkosítási algoritmussal, valamint az FIPS 140-2-kompatibilis. Az Azure Managed Disks mögöttes titkosítási modulokkal kapcsolatos további információkért lásd: a [kriptográfiai API: következő generáció](/windows/desktop/seccng/cng-portal)

A kiszolgálóoldali titkosítás nem befolyásolja a felügyelt lemezek teljesítményét, és nincs további díj. 

> [!NOTE]
> Az ideiglenes lemezek nem felügyelt lemezek, és nem az SSE titkosítással vannak titkosítva, hacsak nem engedélyezi a titkosítást a gazdagépen.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A felügyelt lemez titkosításához a platform által felügyelt kulcsokat használhatja, vagy a titkosítást saját kulcsok használatával is kezelheti. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a felügyelt lemezeken lévő összes érték titkosítására és visszafejtésére használhat. 

A következő szakaszok részletesebben ismertetik a kulcskezelő lehetőségeit.

### <a name="platform-managed-keys"></a>Platform által felügyelt kulcsok

Alapértelmezés szerint a felügyelt lemezek platform által felügyelt titkosítási kulcsokat használnak. Az összes felügyelt lemez, pillanatkép, lemezkép és a meglévő felügyelt lemezekre írt adatok automatikusan titkosítva vannak a platform által felügyelt kulcsokkal.

### <a name="customer-managed-keys"></a>Felhasználó által kezelt kulcsok

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsokra a következő korlátozások vonatkoznak:

- Ha ez a funkció engedélyezve van a lemezen, nem tiltható le.
    Ha ennek megkerülésére van szüksége, a [Azure PowerShell modul](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) vagy az [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)használatával kell másolnia az összes olyan felügyelt lemezre, amely nem használ ügyfél által felügyelt kulcsokat.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Támogatott régiók

Az ügyfél által felügyelt kulcsok minden olyan régióban elérhetők, ahol elérhetők a felügyelt lemezek.

Az automatikus kulcs elforgatása előzetes verzióban érhető el, és csak a következő régiókban érhető el:

- USA keleti régiója
- USA 2. keleti régiója
- USA déli középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- Észak-Európa
- West Europe
- Közép-Franciaország

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

A felügyelt lemezek ügyfelek által felügyelt kulcsainak engedélyezéséhez tekintse meg a cikkek, amelyek a [Azure PowerShell modul](windows/disks-enable-customer-managed-keys-powershell.md), az [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) vagy a [Azure Portal](disks-enable-customer-managed-keys-portal.md)engedélyezését ismertetik. Ha szeretné megtudni, hogyan engedélyezheti az ügyfelek által felügyelt kulcsokat automatikus kulcs-elforgatással, tekintse meg a [Azure Key Vault és a DiskEncryptionSet automatikus kulccsal történő elforgatásával (előzetes verzió)](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Titkosítás a virtuális gép adatai számára a gazdagép-végpontok közötti titkosításnál

Ha engedélyezi a titkosítást a gazdagépen, a titkosítás a virtuális gép gazdagépén indul el, az Azure-kiszolgáló, amelyhez a virtuális gép hozzá van rendelve. Az ideiglenes lemez és az operációs rendszer/adatlemez-gyorsítótárak adatait a rendszer a virtuális gép gazdagépén tárolja. Miután engedélyezte a titkosítást a gazdagépen, az összes adatok titkosítva maradnak a nyugalmi állapotban és a tárolási szolgáltatásba titkosított folyamatokban. A gazdagép titkosítása lényegében teljes körűen titkosítja adatait. A gazdagépen lévő titkosítás nem használja a virtuális gép PROCESSZORát, és nem befolyásolja a virtuális gép teljesítményét. 

Az ideiglenes lemezek és az elmúló operációsrendszer-lemezek a platform által felügyelt kulcsokkal titkosítottak, ha engedélyezi a végpontok közötti titkosítást. Az operációs rendszer és az adatlemez gyorsítótárai a kiválasztott lemez titkosítási típusától függően az ügyfél által felügyelt vagy a platform által felügyelt kulcsokkal vannak titkosítva. Ha például egy lemez az ügyfél által felügyelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára az ügyfél által felügyelt kulcsokkal van titkosítva, és ha a lemez a platform által felügyelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára a platform által felügyelt kulcsokkal van titkosítva.

### <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

A végpontok közötti titkosítás engedélyezéséhez a gazdagépen lévő titkosítás használatával tekintse meg a cikkek, amelyek a [Azure PowerShell modul](windows/disks-enable-host-based-encryption-powershell.md), az [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)vagy a [Azure Portal](disks-enable-host-based-encryption-portal.md)engedélyezését ismertetik.

## <a name="double-encryption-at-rest"></a>Dupla titkosítás a nyugalmi állapotban

A fokozott biztonságú bizalmas ügyfelek, akik az adott titkosítási algoritmushoz, megvalósításhoz vagy kulcshoz kapcsolódó kockázatokat érintik, mostantól további titkosítási réteget is választhatnak, ha az infrastruktúra rétegében a platform által felügyelt titkosítási kulcsok használatával más titkosítási algoritmust/üzemmódot használnak. Ezt az új réteget a megőrzött operációs rendszerre és adatlemezekre, pillanatképekre és képekre lehet alkalmazni, amelyek mindegyike titkosítva lesz a kettős titkosítással.

### <a name="supported-regions"></a>Támogatott régiók

A kettős titkosítás minden olyan régióban elérhető, ahol elérhetők a felügyelt lemezek.

A felügyelt lemezek kettős titkosításának engedélyezéséhez tekintse meg a cikkek a [Azure PowerShell modul](windows/disks-enable-double-encryption-at-rest-powershell.md), az [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) vagy a [Azure Portal](disks-enable-double-encryption-at-rest-portal.md)használatával történő engedélyezését ismertető cikket.

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kiszolgálóoldali titkosítás és az Azure Disk Encryption

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) kihasználja a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját vagy a Windows [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) szolgáltatását, hogy a felügyelt lemezeket a vendég virtuális gépen lévő ügyfél által felügyelt kulcsokkal titkosítsa.  Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.
> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Következő lépések

- Engedélyezze a végpontok közötti titkosítást a gazdagépen, a [Azure PowerShell modul](windows/disks-enable-host-based-encryption-powershell.md), az [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)vagy a [Azure Portal](disks-enable-host-based-encryption-portal.md)használatával.
- A felügyelt lemezeken a [Azure PowerShell modul](windows/disks-enable-double-encryption-at-rest-powershell.md), az [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) vagy a [Azure Portal](disks-enable-double-encryption-at-rest-portal.md)használatával engedélyezze a kettős titkosítást.
- Az ügyfél által felügyelt kulcsok engedélyezése a [Azure PowerShell modul](windows/disks-enable-customer-managed-keys-powershell.md), az [Azure CLI](linux/disks-enable-customer-managed-keys-cli.md) vagy a [Azure Portal](disks-enable-customer-managed-keys-portal.md)használatával felügyelt lemezeken.
- [Az ügyfél által felügyelt kulcsokkal rendelkező titkosított lemezek létrehozásához Azure Resource Manager-sablonok megismerése](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../key-vault/general/overview.md)