---
title: Azure Disk Encryption az Azure AD-vel (előző kiadás)
description: Ez a cikk a IaaS virtuális gépek Microsoft Azure lemezes titkosításának használatának előfeltételeit ismerteti.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 8db9aad279c151073f8c674854d139276a84b064
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555380"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Azure Disk Encryption az Azure AD-vel (előző kiadás)

**A Azure Disk Encryption új kiadása szükségtelenné teszi az Azure AD-alkalmazás paramétereinek biztosítását a virtuális gépek lemezes titkosításának engedélyezéséhez. Az új kiadással már nem szükséges az Azure AD-beli hitelesítő adatok megadása a titkosítás engedélyezése lépés során. Az új kiadás használatával az új virtuális gépeket az Azure AD-alkalmazás paramétereinek használata nélkül kell titkosítani. A VM Disk Encryption új kiadással való engedélyezésével kapcsolatos utasítások megtekintéséhez lásd: [Azure Disk Encryption Windows rendszerű virtuális gépekhez](disk-encryption-overview.md). Azok a virtuális gépek, amelyek már az Azure AD-alkalmazás paramétereivel lettek titkosítva, továbbra is támogatottak, és továbbra is fenn kell tartaniuk a HRE szintaxisát**

Ez a cikk a [Windows rendszerű virtuális gépek Azure Disk Encryption a](disk-encryption-overview.md) további követelményekkel és az Azure ad-vel (korábbi kiadással) való Azure Disk Encryption előfeltételeivel egészíti ki. A [támogatott virtuális gépek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems) szakasza ugyanaz marad.

## <a name="networking-and-group-policy"></a>Hálózatkezelés és Csoportházirend

**Ha engedélyezni szeretné a Azure Disk Encryption funkciót a régebbi HRE paraméter szintaxisával, a IaaS virtuális gépeknek meg kell felelniük a következő hálózati végpont-konfigurációs követelményeknek:** 
  - Ahhoz, hogy jogkivonatot kapjon a kulcstartóhoz való kapcsolódáshoz, a IaaS virtuális gépnek képesnek kell lennie csatlakozni egy Azure Active Directory végponthoz, \[ login.microsoftonline.com \] .
  - A titkosítási kulcsok a kulcstartóba való írásához a IaaS virtuális gépnek csatlakoznia kell a Key Vault-végponthoz.
  - A IaaS virtuális gépnek képesnek kell lennie csatlakozni egy Azure Storage-végponthoz, amely az Azure-bővítmény adattárát és a VHD-fájlokat tároló Azure Storage-fiókot tartalmazza.
  -  Ha a biztonsági házirend korlátozza az Azure-beli virtuális gépekről az internetre való hozzáférést, az előző URI-t megoldhatja, és konfigurálhat egy adott szabályt, hogy engedélyezze a kimenő kapcsolatot az IP-címekkel. További információ: [Azure Key Vault tűzfal mögött](../../key-vault/general/access-behind-firewall.md).
  - A titkosítani kívánt virtuális gépet úgy kell konfigurálni, hogy a TLS 1,2-et használja alapértelmezett protokollként. Ha a TLS 1,0 explicit módon le van tiltva, és a .NET verziója nem frissült a 4,6-es vagy újabb verzióra, a beállításjegyzék következő módosítása lehetővé teszi az ADE-t, hogy kiválassza a legújabb TLS-verziót:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Csoportházirend:**
 - A Azure Disk Encryption megoldás a BitLocker külső kulcstartót használja a Windows IaaS virtuális gépekhez. Tartományhoz csatlakozó virtuális gépek esetében ne küldjön le olyan csoportházirendeket, amelyek kikényszerítik a TPM-védőket. További információ a "BitLocker engedélyezése kompatibilis TPM nélkül" Csoportházirendről: a [bitlocker csoportházirend referenciája](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A tartományhoz csatlakoztatott, egyéni csoportházirendtel rendelkező virtuális gépekre vonatkozó BitLocker-házirendnek tartalmaznia kell a következő beállítást: a [BitLocker helyreállítási adatok felhasználói tárolójának konfigurálása – > engedélyezi a 256 bites helyreállítási kulcsot](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Azokon a számítógépeken, amelyeken nem volt megfelelő házirend-beállítás, alkalmazza az új házirendet, kényszerítse az új házirend frissítését (gpupdate.exe/Force), majd szükség lehet az újraindításra.  

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

Részletekért lásd: [kulcstartó létrehozása és konfigurálása az Azure Disk Encryptionhoz az Azure ad-vel (előző kiadás)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Következő lépések

- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](disk-encryption-key-vault-aad.md)
- [Azure Disk Encryption engedélyezése az Azure AD-vel Windows rendszerű virtuális gépeken (korábbi kiadás)](disk-encryption-windows-aad.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
