---
title: Azure Disk Encryption az Azure AD-alkalmazás előfeltételeivel (előző kiadás)
description: Ez a cikk kiegészíti az Azure AD-vel való Azure Disk Encryption további követelményeket és előfeltételeket biztosító Linux rendszerű virtuális gépek Azure Disk Encryption.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e27eb64139c20ac2b8c776edc2d0840b80fddb62
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558338"
---
# <a name="azure-disk-encryption-with-azure-active-directory-ad-previous-release"></a>Azure Disk Encryption Azure Active Directory (AD) (előző kiadás)

A Azure Disk Encryption új kiadása szükségtelenné teszi a Azure Active Directory (Azure AD) alkalmazás paraméterének megadását a VM-lemezek titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos útmutatásért lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](disk-encryption-overview.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát

Ez a cikk kiegészíti az Azure AD-vel (korábbi kiadással) Azure Disk Encryptionekhez szükséges további követelményekkel és előfeltételekkel [Azure Disk Encryption linuxos virtuális gépekhez](disk-encryption-overview.md) .

Az ezekben a fejezetekben található információk a következők maradnak:

- [Támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuális gépekre vonatkozó követelmények](disk-encryption-overview.md#additional-vm-requirements)


## <a name="networking-and-group-policy"></a>Hálózatkezelés és Csoportházirend

Ha engedélyezni szeretné a Azure Disk Encryption funkciót a régebbi HRE paraméter szintaxisával, az infrastruktúra (IaaS) virtuális gépeknek meg kell felelniük a következő hálózati végpontok konfigurációs követelményeinek: 
  - Ahhoz, hogy jogkivonatot kapjon a kulcstartóhoz való kapcsolódáshoz, a IaaS virtuális gépnek képesnek kell lennie csatlakozni egy Azure AD-végponthoz ( \[ login.microsoftonline.com \] ).
  - A titkosítási kulcsok a kulcstartóba való írásához a IaaS virtuális gépnek csatlakoznia kell a Key Vault-végponthoz.
  - A IaaS virtuális gépnek képesnek kell lennie csatlakozni egy Azure Storage-végponthoz, amely az Azure-bővítmény adattárát és a VHD-fájlokat tároló Azure Storage-fiókot tartalmazza.
  -  Ha a biztonsági házirend korlátozza az Azure-beli virtuális gépekről az internetre való hozzáférést, az előző URI-t megoldhatja, és konfigurálhat egy adott szabályt, hogy engedélyezze a kimenő kapcsolatot az IP-címekkel. További információ: [Azure Key Vault tűzfal mögött](../../key-vault/general/access-behind-firewall.md).
  - Windows rendszeren, ha a TLS 1,0 explicit módon le van tiltva, és a .NET verziója nem frissül 4,6 vagy újabb verzióra, a következő beállításjegyzék-módosítás lehetővé teszi, hogy Azure Disk Encryption válassza ki a legújabb TLS-verziót:

  ```config-registry
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001
    
  [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
  "SystemDefaultTlsVersions"=dword:00000001
  "SchUseStrongCrypto"=dword:00000001` 
  ```

### <a name="group-policy"></a>Csoportházirend
 - A Azure Disk Encryption megoldás a BitLocker külső kulcstartót használja a Windows IaaS virtuális gépekhez. A tartományhoz csatlakoztatott virtuális gépek esetében ne küldjön le olyan csoportházirendeket, amelyek kikényszerítik a TPM-védőket. További információ a **BitLocker kompatibilis TPM nélküli engedélyezése** beállításának Csoportházirendáról: a [BitLocker csoportházirend referenciája](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- Az egyéni Csoportházirend rendelkező, tartományhoz csatlakoztatott virtuális gépeken a BitLocker-házirendnek tartalmaznia kell a következő beállítást: a [BitLocker helyreállítási adatok felhasználói tárolójának konfigurálása – > engedélyezi a 256 bites helyreállítási kulcsot](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption sikertelen, ha a BitLocker egyéni Csoportházirend beállításai nem kompatibilisek. A megfelelő házirend-beállítással nem rendelkező gépeken alkalmazza az új házirendet, kényszerítse az új házirendet a frissítésre (gpupdate.exe/Force), majd indítsa újra, ha szükséges. 

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei 

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

További információ: [Key Vault létrehozása és konfigurálása Azure Disk Encryption az Azure ad-vel (korábbi kiadás)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Következő lépések

- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](disk-encryption-key-vault-aad.md)
- [Azure Disk Encryption engedélyezése az Azure AD-vel Linux rendszerű virtuális gépeken (korábbi kiadás)](disk-encryption-linux-aad.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
