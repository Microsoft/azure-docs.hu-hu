---
title: Azure-beli felügyelt lemezek kiszolgálóoldali titkosítása
description: Az Azure Storage úgy védi az adatokat, hogy titkosítja az adatokat, mielőtt a Storage-fürtökben maradnak. Az ügyfél által kezelt kulcsokkal kezelheti a titkosítást a saját kulcsokkal, vagy a Microsoft által felügyelt kulcsokra támaszkodhat a felügyelt lemezek titkosítása során.
author: roygara
ms.date: 04/15/2021
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4607778c78b8b062b265a5754337c09c41ba83f1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531536"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Az adattitkosítás kiszolgálóoldali Azure Disk Storage

Az Azure által felügyelt lemezek legtöbbje Azure Storage-titkosítással van titkosítva, amely kiszolgálóoldali titkosítással (SSE) védi az adatokat, és segít a vállalati biztonsági és megfelelőségi követelmények teljesítésében. Az Azure Storage-titkosítás automatikusan titkosítja az azure-beli felügyelt lemezeken (operációs rendszeren és adatlemezen) tárolt adatokat alapértelmezés szerint, amikor a felhőben tárolja az adatokat. A gazdagépen engedélyezett titkosítással rendelkező lemezek azonban nincsenek titkosítva az Azure Storage-ban. A gazdagépen engedélyezett titkosítással rendelkező lemezek esetén a virtuális gépet üzemeltető kiszolgáló biztosítja az adatok titkosítását, és a titkosított adatok az Azure Storage-ba áramlnak.

Az Azure felügyelt lemezei 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)transzparens módon titkosítják az adatokat, az egyik legerősebb elérhető blokktitkosítással, és FIPS 140-2 szabványnak megfelelőek. További információ az Azure managed Disks alapjául szolgáló titkosítási modulokról: [Titkosítási API: Következő generáció](/windows/desktop/seccng/cng-portal)

Az Azure Storage-titkosítás nincs hatással a felügyelt lemezek teljesítményére, és nincs további költség. További információ az Azure Storage-titkosításról: [Azure Storage-titkosítás.](/azure/storage/common/storage-service-encryption)

> [!NOTE]
> Az ideiglenes lemezek nem felügyelt lemezek, és az SSE nem titkosítja őket, hacsak nem engedélyezi a titkosítást a gazdagépen.

## <a name="about-encryption-key-management"></a>A titkosítási kulcskezelés

A felügyelt lemez titkosítása platform által felügyelt kulcsokkal is kezelhető, de saját kulcsokkal is kezelhető. Ha úgy dönt, hogy a titkosítást a saját kulcsokkal kezeli, megadhat egy felhasználó által kezelt kulcsot, amely *a* felügyelt lemezeken található összes adat titkosításához és visszafejtéséhez használható. 

A következő szakaszok részletesebben ismertetik a kulcskezelés egyes lehetőségeit.

### <a name="platform-managed-keys"></a>Platform által felügyelt kulcsok

Alapértelmezés szerint a felügyelt lemezek platform által felügyelt titkosítási kulcsokat használnak. A rendszer a meglévő felügyelt lemezekre írt összes felügyelt lemezt, pillanatképet, rendszerképet és adatot automatikusan titkosítja a platform által felügyelt kulcsokkal.

### <a name="customer-managed-keys"></a>Felhasználó által kezelt kulcsok

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Korlátozások

Az ügyfél által kezelt kulcsokra jelenleg a következő korlátozások vonatkoznak:

- Ha ez a funkció engedélyezve van a lemezen, nem tilthatja le.
    Ha meg kell ezt dolgoznia, az összes adatot a [Azure PowerShell](windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) modul vagy az [Azure CLI](linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)használatával egy teljesen más felügyelt lemezre kell másolnia, amely nem használ ügyfél által kezelt kulcsokat.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Támogatott régiók

Az ügyfél által felügyelt kulcsok minden olyan régióban elérhetők, ahol a felügyelt lemezek elérhetők.

Az automatikus kulcsrotáció előzetes verzióban érhető el, és csak a következő régiókban érhető el:

- USA keleti régiója
- USA 2. keleti régiója
- USA déli középső régiója
- USA nyugati régiója
- USA 2. nyugati régiója
- Észak-Európa
- Nyugat-Európa
- Közép-Franciaország

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, a Azure Active Directory (Azure AD) szolgáltatását. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást az erőforrásokhoz. Ha később áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt az egyik Azure AD-címtárból egy másikba, a felügyelt lemezekhez társított felügyelt identitás nem lesz áthelyezve az új bérlőbe, így előfordulhat, hogy az ügyfél által felügyelt kulcsok nem működnek tovább. További információ: [Előfizetés átvitele Azure AD-könyvtárak között.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

Ha engedélyezni szeretné az ügyfél által felügyelt kulcsokat a felügyelt lemezeken, tekintse meg a következő cikkeket: hogyan engedélyezhető az [Azure PowerShell modullal,](windows/disks-enable-customer-managed-keys-powershell.md)az [Azure CLI-val](linux/disks-enable-customer-managed-keys-cli.md) vagy a [Azure Portal.](disks-enable-customer-managed-keys-portal.md) Ha szeretné megtudni, hogyan engedélyezheti az ügyfél által kezelt kulcsokat az automatikus kulcsrotálás használatával, olvassa el a Azure Key Vault és a [DiskEncryptionSet](windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-with-automatic-key-rotation-preview)automatikus kulcsrotálást (előzetes verzió) bemutató cikkét.

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Titkosítás a gazdagépen – A virtuális gép adatainak végpontok között titkosítása

Amikor engedélyezi a titkosítást a gazdagépen, a titkosítás magára a virtuálisgép-gazdagépre, az Azure-kiszolgálóra indul, amely számára a virtuális gép le van osztva. Az ideiglenes lemez és az operációsrendszer-/adatlemez gyorsítótárának adatait a rendszer a virtuálisgép-gazdagépen tárolja. Miután engedélyezte a titkosítást a gazdagépen, az összes tárolt adat titkosítva lesz, és a Storage szolgáltatásba lesz titkosítva, ahol megőrzve van. A gazdagépen való titkosítás lényegében végpontok között titkosítja az adatokat. A gazdagépen való titkosítás nem használja a virtuális gép processzorát, és nincs hatással a virtuális gép teljesítményére. 

Az ideiglenes lemezeket és a ideiglenes operációsrendszer-lemezeket a rendszer platform által felügyelt kulcsokkal titkosítja, amikor engedélyezi a teljes adattitkosítást. Az operációs rendszer és az adatlemez gyorsítótárai az ügyfél által felügyelt vagy a platform által felügyelt kulcsokkal vannak titkosítva, a kiválasztott lemeztitkosítási típustól függően. Ha például egy lemez ügyfél által kezelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára ügyfél által kezelt kulcsokkal van titkosítva, és ha a lemez platform által kezelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára platform által kezelt kulcsokkal lesz titkosítva.

### <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

A virtuálisgép-méreteket programozott módon is megkeresheti. Ha meg szeretne ismerkedni a lekérésük programozott módon történő használatával, tekintse meg a támogatott [virtuálisgép-méretek](windows/disks-enable-host-based-encryption-powershell.md#finding-supported-vm-sizes) megkeresésről Azure PowerShell Azure CLI-cikkeket. [](linux/disks-enable-host-based-encryption-cli.md#finding-supported-vm-sizes)

Ha a gazdagépen titkosítással szeretné engedélyezni a végpontok között titkosítást, tekintse meg a [Azure PowerShell](windows/disks-enable-host-based-encryption-powershell.md)modul, az [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)vagy a [Azure Portal.](disks-enable-host-based-encryption-portal.md)

## <a name="double-encryption-at-rest"></a>Dupla titkosítás az adatáttitkosításhoz

Azok a magas szintű biztonságra érzékeny ügyfelek, akik az adott titkosítási algoritmussal, implementációval vagy kulccsal járó kockázat miatt aggódnak, mostantól dönthetnek úgy, hogy egy másik titkosítási algoritmust/módot használó további titkosítási réteget alkalmaznak az infrastruktúrarétegben a platform által felügyelt titkosítási kulcsokkal. Ez az új réteg alkalmazható a megőrzött operációs rendszerekre és adatlemezekre, pillanatképekre és képekre, amelyek mind kettős titkosítással lesznek titkosítva.

### <a name="supported-regions"></a>Támogatott régiók

A dupla titkosítás minden olyan régióban elérhető, ahol a felügyelt lemezek elérhetők.

Ha engedélyezni szeretné a dupla titkosítást az azure-beli felügyelt lemezeken, tekintse meg a következő cikkeket: az [Azure PowerShell](windows/disks-enable-double-encryption-at-rest-powershell.md)modul, az [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) vagy a [Azure Portal.](disks-enable-double-encryption-at-rest-portal.md)

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kiszolgálóoldali titkosítás és Azure Disk Encryption

[Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) Linux [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funkcióját vagy a Windows [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) funkcióját használja a felügyelt lemezek ügyfél által felügyelt kulcsokkal történő titkosítására a vendég virtuális gépen.  Az ügyfél által kezelt kulcsokkal való kiszolgálóoldali titkosítás azáltal javítja az ADE-t, hogy lehetővé teszi, hogy bármilyen operációsrendszer-típust és rendszerképet használjon a virtuális gépekhez a Storage szolgáltatásban tárolt adatok titkosításával.
> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, a Azure Active Directory (Azure AD) szolgáltatását. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást az erőforrásokhoz. Ha később áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt az egyik Azure AD-címtárból egy másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőbe, így előfordulhat, hogy az ügyfél által felügyelt kulcsok nem működnek tovább. További információ: [Előfizetés átvitele Azure AD-könyvtárak között.](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

## <a name="next-steps"></a>Következő lépések

- Engedélyezze a végpontok között titkosítást a gazdagépen a Azure PowerShell [modul,](windows/disks-enable-host-based-encryption-powershell.md)az [Azure CLI](linux/disks-enable-host-based-encryption-cli.md)vagy a [Azure Portal.](disks-enable-host-based-encryption-portal.md)
- Engedélyezze a dupla titkosítást az olyan felügyelt lemezeken, mint a [Azure PowerShell modul,](windows/disks-enable-double-encryption-at-rest-powershell.md)az [Azure CLI](linux/disks-enable-double-encryption-at-rest-cli.md) vagy a [Azure Portal.](disks-enable-double-encryption-at-rest-portal.md)
- Engedélyezze az ügyfél által felügyelt kulcsokat a felügyelt lemezeken a Azure PowerShell [modullal,](windows/disks-enable-customer-managed-keys-powershell.md)az [Azure CLI-val](linux/disks-enable-customer-managed-keys-cli.md) vagy a [Azure Portal.](disks-enable-customer-managed-keys-portal.md)
- [Ismerje meg Azure Resource Manager ügyfél által kezelt kulcsokkal titkosított lemezek létrehozásához elérhető sablonokat](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../key-vault/general/overview.md)
