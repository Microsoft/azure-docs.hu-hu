---
title: Gyakori kérdések – Azure Disk Encryption Windows rendszerű virtuális gépekhez
description: Ez a cikk a Windows IaaS virtuális gépek Microsoft Azure lemezes titkosításával kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6371a6bd0c4b8151c97a892ec70789429b4bda75
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562197"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Azure Disk Encryption a Windows rendszerű virtuális gépekkel kapcsolatos gyakori kérdések

Ez a cikk a Windows rendszerű virtuális gépek Azure Disk Encryptionával kapcsolatos gyakori kérdésekre (GYIK) nyújt válaszokat. További információ erről a szolgáltatásról: [Azure Disk Encryption Overview (áttekintés](disk-encryption-overview.md)).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Mi Azure Disk Encryption a Windows rendszerű virtuális gépekhez?

A Windows rendszerű virtuális gépek Azure Disk Encryption a Windows BitLocker szolgáltatásával biztosítja az operációsrendszer-lemez és az adatlemezek teljes lemezes titkosítását. Emellett az ideiglenes lemez titkosítását is biztosítja, ha a [VolumeType paraméter mind](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  A virtuális gépről a tárolási háttérbe titkosított tartalom-adatfolyamok. Ezáltal teljes körű titkosítást biztosít az ügyfél által felügyelt kulccsal.
 
Lásd: [támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Hol Azure Disk Encryption az általánosan elérhető (GA)?

A Azure Disk Encryption általánosan elérhető az összes Azure-beli nyilvános régióban.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Milyen felhasználói élményt kínál a Azure Disk Encryption?

Azure Disk Encryption a GA támogatja a Azure Resource Manager sablonokat, a Azure PowerShell és az Azure CLI-t. A különböző felhasználói élmény rugalmasságot biztosít. Három különböző lehetőség áll rendelkezésre a lemez titkosításának engedélyezéséhez a virtuális gépeken. A Azure Disk Encryptionban elérhető felhasználói élményről és lépésenkénti útmutatóról a [Windows Azure Disk Encryption forgatókönyvei](disk-encryption-windows.md)című témakörben olvashat bővebben.

## <a name="how-much-does-azure-disk-encryption-cost"></a>Mennyibe kerül a Azure Disk Encryption?

A virtuálisgép-lemezek Azure Disk Encryption-mel való titkosítása díjmentes, de a Azure Key Vault használatával járó díjak is rendelkezésre állnak. Azure Key Vault költségekkel kapcsolatos további információkért tekintse meg a [Key Vault díjszabását](https://azure.microsoft.com/pricing/details/key-vault/) ismertető oldalt.

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Hogyan használhatom a Azure Disk Encryption?

Első lépésként olvassa el a [Azure Disk Encryption áttekintése című témakört](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Milyen virtuálisgép-méreteket és operációs rendszereket támogat a Azure Disk Encryption?

A [Azure Disk Encryption áttekintő](disk-encryption-overview.md) cikk a [virtuális gépek méretét](disk-encryption-overview.md#supported-vms) és a Azure Disk Encryption támogató virtuálisgép- [operációs rendszereket](disk-encryption-overview.md#supported-operating-systems) sorolja fel.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Titkosítható a rendszerindító és az adatkötetek Azure Disk Encryption is?

Titkosíthatja a rendszerindító és az adatköteteket is, de az operációs rendszer kötetének első titkosítása nélkül nem titkosíthatja az adattitkosítást.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Titkosítható a leválasztott kötetek Azure Disk Encryption?

Nem, Azure Disk Encryption csak a csatlakoztatott köteteket titkosítja.

## <a name="what-is-storage-server-side-encryption"></a>Mi a Storage kiszolgálóoldali titkosítás?

A Storage kiszolgálóoldali titkosítása titkosítja az Azure Managed Disks-t az Azure Storage-ban. A felügyelt lemezek alapértelmezés szerint a kiszolgálóoldali titkosítással vannak titkosítva a platform által felügyelt kulccsal (2017. június 10-ig). A felügyelt lemezek titkosítását a saját kulcsaival kezelheti az ügyfél által felügyelt kulcs megadásával. További információ: [Az Azure Managed Disks kiszolgálóoldali titkosítása](../disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>Miben különbözik Azure Disk Encryption a Storage kiszolgálóoldali titkosítás az ügyfél által felügyelt kulccsal, és mikor kell használnia az egyes megoldásokat?

Azure Disk Encryption az operációsrendszer-lemez, az adatlemezek és az ügyfél által felügyelt kulccsal rendelkező ideiglenes lemez végpontok közötti titkosítását biztosítja.

- Ha a követelmények közé tartozik az összes fenti és végpontok közötti titkosítás titkosítása, használja a Azure Disk Encryption. 
- Ha a követelmények magukban foglalják a csak az ügyfél által felügyelt adatok titkosítását, használja a [kiszolgálóoldali titkosítást az ügyfél által felügyelt](../disk-encryption.md)kulcsokkal. A Azure Disk Encryption és a Storage kiszolgálóoldali titkosítással rendelkező lemezek nem titkosíthatók az ügyfél által felügyelt kulcsokkal.
- Ha olyan forgatókönyvet használ, amelyet a Windows nem [támogatott forgatókönyvei](disk-encryption-windows.md#unsupported-scenarios)használnak, érdemes lehet [kiszolgálóoldali titkosítást használni az ügyfél által felügyelt kulcsokkal](../disk-encryption.md). 
- Ha a szervezet házirendje lehetővé teszi a tartalom titkosítását egy Azure által felügyelt kulccsal, akkor nincs szükség beavatkozásra – a tartalom alapértelmezés szerint titkosítva van. A felügyelt lemezek esetében a tárterületen belüli tartalom alapértelmezés szerint titkosítva van a platform által felügyelt kulccsal rendelkező kiszolgálóoldali titkosítással. A kulcsot az Azure Storage szolgáltatás kezeli. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Hogyan elforgatni a titkokat vagy a titkosítási kulcsokat?

A titkok elforgatásához egyszerűen hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, egy másik Key Vault megadásával. A kulcs titkosítási kulcsának elforgatásához hívja meg ugyanazt a parancsot, amelyet eredetileg a lemez titkosításának engedélyezéséhez használt, és megadhatja az új kulcs titkosítását. 

>[!WARNING]
> - Ha korábban már használta [Azure Disk Encryption az Azure ad-alkalmazással](disk-encryption-windows-aad.md) az Azure ad-beli hitelesítő adatok megadásával a virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Ezen a titkosított virtuális gépen nem használható Azure Disk Encryption, mivel ez nem támogatott forgatókönyv, ami azt jelenti, hogy a titkosított virtuális gép esetében a HRE alkalmazásból való átállás még nem támogatott.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Hogyan a kulcs titkosítási kulcsának hozzáadását vagy eltávolítását, ha az eredetileg nem használ ilyet?

A kulcs titkosítási kulcsának hozzáadásához hívja meg újra az Enable parancsot a kulcs titkosítási kulcsának paraméterének átadásával. A kulcs titkosítási kulcsának eltávolításához hívja meg újra az Enable parancsot a kulcs titkosítási kulcs paramétere nélkül.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>A Azure Disk Encryption lehetővé teszi a saját kulcs (BYOK) használatát?

Igen, saját kulcsú titkosítási kulcsokat is megadhat. A kulcsok védelme Azure Key Vault, amely a Azure Disk Encryption kulcstárolója. A kulcs titkosítási kulcsait támogató forgatókönyvekkel kapcsolatos további információkért lásd: [kulcstartó létrehozása és konfigurálása a Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Használhatok Azure által létrehozott kulcs-titkosítási kulcsot?

Igen, a Azure Key Vault használatával létrehozhat egy kulcs-titkosítási kulcsot az Azure Disk Encryption használatához. A kulcsok védelme Azure Key Vault, amely a Azure Disk Encryption kulcstárolója. További információ a kulcs titkosítási kulcsáról: [kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Használhatok helyszíni kulcskezelő szolgáltatást vagy HSM-et a titkosítási kulcsok védelme érdekében?

A helyszíni kulcskezelő szolgáltatás vagy a HSM nem használható a titkosítási kulcsok Azure Disk Encryption való védelmére. A titkosítási kulcsok védelme érdekében csak a Azure Key Vault szolgáltatást használhatja. További információ a kulcs titkosítási kulcsának támogatási forgatókönyvekről: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Mik a Azure Disk Encryption konfigurálásának előfeltételei?

A Azure Disk Encryption előfeltételei. Tekintse meg a [Key Vault létrehozása és konfigurálása a Azure Disk Encryption](disk-encryption-key-vault.md) cikkhez új kulcstartó létrehozásához vagy egy meglévő kulcstartó beállítása a titkosítás engedélyezéséhez, valamint a titkok és kulcsok védelméhez. További információ a kulcs titkosítási kulcsának támogatási forgatókönyvekről: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Mik az előfeltételek a Azure Disk Encryption konfigurálásához egy Azure AD-alkalmazással (előző kiadás)?

A Azure Disk Encryption előfeltételei. Tekintse meg az Azure AD-tartalommal [rendelkező Azure Disk Encryption](disk-encryption-windows-aad.md) Azure Active Directory alkalmazás létrehozásához, egy új kulcstartó létrehozásához vagy egy meglévő Key Vault beállításához a titkosítás engedélyezéséhez, valamint a titkok és kulcsok védelméhez. További információ a kulcs titkosítási kulcsának támogatási forgatókönyvekről: [Key Vault létrehozása és konfigurálása Azure Disk Encryption Azure ad-vel](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Egy Azure AD-alkalmazás (korábbi kiadás) használatával Azure Disk Encryption továbbra is támogatott?
Igen. Az Azure AD-alkalmazással történő lemezes titkosítás továbbra is támogatott. Az új virtuális gépek titkosításakor azonban javasoljuk, hogy az új metódust használja az Azure AD-alkalmazással való titkosítás helyett. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Áttelepíthetek egy Azure AD-alkalmazással titkosított virtuális gépeket az Azure AD-alkalmazás nélküli titkosításhoz?
  Jelenleg nincs közvetlen áttelepítési útvonal olyan gépekhez, amelyek Azure ad-alkalmazással lettek titkosítva az Azure AD-alkalmazás nélkül. Emellett nincs közvetlen elérési út a titkosításból, ha az Azure AD-alkalmazás nem rendelkezik egy AD-alkalmazással való titkosításhoz. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>A Azure PowerShell milyen verzióját támogatja Azure Disk Encryption?

A Azure Disk Encryption konfigurálásához használja a Azure PowerShell SDK legújabb verzióját. Töltse le a [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)legújabb verzióját. A Azure Disk Encryption az Azure SDK 1.1.0-es verziójában *nem* támogatott.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Mi a lemez "Bek Volume" vagy "/mnt/azure_bek_disk"?

A "Bek Volume" egy helyi adatkötet, amely biztonságosan tárolja a titkosított Azure-beli virtuális gépek titkosítási kulcsait.

> [!NOTE]
> Ne töröljön vagy szerkesszen semmilyen tartalmat ebben a lemezen. Ne válassza le a lemez leválasztását, mivel a titkosítási kulcs jelenléte szükséges a IaaS virtuális gépen található bármely titkosítási művelethez.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Milyen titkosítási módszert használ a Azure Disk Encryption?

Azure Disk Encryption kiválasztja a BitLocker titkosítási módszerét a Windows-verzió alapján a következőképpen:

| Windows-verziók                 | Verzió | Titkosítási módszer        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 vagy újabb  | >= 1511 |XTS – AES 256 bit           |
| Windows Server 2012, Windows 8, 8,1, 10 | < 1511 |AES 256 bites *              |
| Windows Server 2008R2            |        |AES 256-bit diffúzorral |

\* Az AES 256-bit a diffúzorral nem támogatott a Windows 2012 és újabb verziókban.

A Windows operációs rendszer verziójának meghatározásához futtassa a "winver" eszközt a virtuális gépen.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Készíthetek biztonsági másolatot és állíthatok vissza egy titkosított virtuális gépet? 

Azure Backup lehetővé teszi a titkosított virtuális gépek biztonsági mentését és visszaállítását ugyanazon előfizetésen és régión belül.  Útmutatásért tekintse meg a [titkosított virtuális gépek biztonsági mentése és visszaállítása Azure Backupokkal](../../backup/backup-azure-vms-encryption.md)című témakört.  A titkosított virtuális gépek másik régióba való visszaállítása jelenleg nem támogatott.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Hol tehetek fel kérdéseket vagy visszajelzést?

Felteheti kérdéseit, vagy visszajelzést küldhet a [Microsoft Q&a Azure Disk Encryption kérdéseit tartalmazó oldalt](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Következő lépések
Ebben a dokumentumban többet is megtudhat a Azure Disk Encryptionekkel kapcsolatos leggyakoribb kérdésekről. A szolgáltatással kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Az Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Lemez titkosításának alkalmazása Azure Security Center](../../security-center/asset-inventory.md)
- [Inaktív adatok titkosítása az Azure-ban](../../security/fundamentals/encryption-atrest.md)