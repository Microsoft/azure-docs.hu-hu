---
title: Azure Disk Encryption engedélyezése Windows rendszerű virtuális gépekhez
description: Ez a cikk útmutatást nyújt a Windows rendszerű virtuális gépek Microsoft Azure lemezes titkosításának engedélyezéséhez.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 8e95f770a3335d66eae0a690e148c4d6ddc22d5c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555329"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption Windows rendszerű virtuális gépekhez

Az Azure Disk Encryption segít az adatok biztonságos megőrzésében a vállalat által előírt biztonsági és megfelelőségi követelmények kielégítése érdekében. A Windows [BitLocker](https://en.wikipedia.org/wiki/BitLocker) szolgáltatásával biztosítja a kötetek titkosítását az Azure Virtual Machines (VM) operációsrendszer-és adatlemezei számára, és integrálva van [Azure Key Vault](../../key-vault/index.yml) a lemezes titkosítási kulcsok és titkos kódok felügyeletéhez és kezeléséhez.

Azure Disk Encryption a zóna rugalmas, ugyanúgy, mint Virtual Machines. Részletekért lásd: [Availability Zones támogató Azure-szolgáltatások](../../availability-zones/az-region.md).

Ha [Azure Security Center](../../security-center/index.yml)használ, a rendszer riasztást küld, ha nem titkosított virtuális gépek vannak. A riasztások magas súlyosságot mutatnak, és a javasolt a virtuális gépek titkosítása.

![Azure Security Center lemez titkosítási riasztása](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. Részletekért lásd: [Azure Disk Encryption az Azure ad-vel (előző kiadás)](disk-encryption-overview-aad.md) . 
> - Bizonyos javaslatok növelhetik az adatok, a hálózat vagy a számítási erőforrások használatát, ami további licenc-vagy előfizetési költségeket eredményezhet. Érvényes aktív Azure-előfizetéssel kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre az Azure-ban a támogatott régiókban.

Néhány perc alatt megismerheti a Windows Azure Disk Encryption alapjait, és a Windows rendszerű virtuális gépek [létrehozása és titkosítása az Azure CLI](disk-encryption-cli-quickstart.md) gyors üzembe helyezésével, valamint a [Windows rendszerű virtuális gép létrehozása és titkosítása Azure PowerShell](disk-encryption-powershell-quickstart.md)gyors útmutatóval.

## <a name="supported-vms-and-operating-systems"></a>Támogatott virtuális gépek és operációs rendszerek

### <a name="supported-vms"></a>Támogatott virtuális gépek

A Windows rendszerű virtuális gépek [számos méretben](../sizes-general.md)érhetők el. A Azure Disk Encryption az 1. és a 2. generációs virtuális gépek esetében támogatott. A Premium Storage szolgáltatással rendelkező virtuális gépek esetében Azure Disk Encryption is elérhető.

Azure Disk Encryption nem érhető el az [alapszintű, a sorozatú](https://azure.microsoft.com/pricing/details/virtual-machines/series/)virtuális gépeken, illetve a 2 GB-nál kevesebb memóriával rendelkező virtuális gépeken.  A Azure Disk Encryption nem érhető el a virtuálisgép-rendszerképeken, Temp lemezek nélkül (DV4, Dsv4, Ev4 és Esv4).  Lásd: Azure-beli [virtuális gépek mérete helyi ideiglenes lemez nélkül](../azure-vms-no-temp-disk.md).  További kivételeket a [Azure Disk Encryption: nem támogatott forgatókönyvek](disk-encryption-windows.md#unsupported-scenarios)című témakörben talál.

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

- Windows-ügyfél: Windows 8 és újabb verziók.
- Windows Server: Windows Server 2008 R2 és újabb verziók.  
 
> [!NOTE]
> A Windows Server 2008 R2 megköveteli, hogy a .NET-keretrendszer 4,5 legyen telepítve a titkosításhoz; telepítse azt Windows Update a Windows Server 2008 R2 x64-alapú rendszerek ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)) választható frissítés Microsoft .net keretrendszere 4.5.2-es verziójával.  
>  
> A Windows Server 2012 R2 Core és a Windows Server 2016 Core megköveteli, hogy a bdehdcfg összetevőt a virtuális gépre telepítse a titkosításhoz.


## <a name="networking-requirements"></a>Hálózati követelmények
A Azure Disk Encryption engedélyezéséhez a virtuális gépeknek meg kell felelniük a hálózati végpont következő konfigurációs követelményeinek:
  - Ahhoz, hogy jogkivonatot kapjon a kulcstartóhoz való kapcsolódáshoz, a Windows rendszerű virtuális gépnek képesnek kell lennie csatlakozni egy Azure Active Directory végponthoz, a \[ login.microsoftonline.com \] .
  - A titkosítási kulcsok a kulcstartóba való írásához a Windows rendszerű virtuális gépnek csatlakoznia kell a Key Vault-végponthoz.
  - A Windows rendszerű virtuális gépnek képesnek kell lennie csatlakozni egy Azure Storage-végponthoz, amely az Azure-bővítmény adattárát és a VHD-fájlokat tároló Azure Storage-fiókot üzemelteti.
  -  Ha a biztonsági házirend korlátozza az Azure-beli virtuális gépekről az internetre való hozzáférést, az előző URI-t megoldhatja, és konfigurálhat egy adott szabályt, hogy engedélyezze a kimenő kapcsolatot az IP-címekkel. További információ: [Azure Key Vault tűzfal mögött](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Csoportházirend követelmények

A Azure Disk Encryption a Windows rendszerű virtuális gépekhez készült BitLocker External Key protectort használja. Tartományhoz csatlakozó virtuális gépek esetében ne küldjön le olyan csoportházirendeket, amelyek kikényszerítik a TPM-védőket. További információ a "BitLocker engedélyezése kompatibilis TPM nélkül" Csoportházirendről: a [bitlocker csoportházirend referenciája](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

A tartományhoz csatlakoztatott, egyéni csoportházirendtel rendelkező virtuális gépekre vonatkozó BitLocker-házirendnek tartalmaznia kell a következő beállítást: a [BitLocker helyreállítási adatok felhasználói tárolójának konfigurálása – > engedélyezi a 256 bites helyreállítási kulcsot](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A Azure Disk Encryption sikertelen lesz, ha a BitLocker egyéni csoportházirend-beállításai nem kompatibilisek. Azokon a számítógépeken, amelyeken nem volt megfelelő házirend-beállítás, alkalmazza az új házirendet, kényszerítse az új házirend frissítését (gpupdate.exe/Force), majd szükség lehet az újraindításra.

A Azure Disk Encryption sikertelen lesz, ha a tartományi szintű csoportházirend letiltja a BitLocker által használt AES-CBC algoritmust.

## <a name="encryption-key-storage-requirements"></a>Titkosítási kulcs tárolási követelményei  

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key vaultnak és a virtuális gépeknek ugyanabban az Azure-régióban és-előfizetésben kell lenniük.

További információ: [Key Vault létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminológia
Az alábbi táblázat az Azure Disk Encryption dokumentációjában használt általános kifejezéseket ismerteti:

| Terminológia | Definíció |
| --- | --- |
| Azure Key Vault | Key Vault egy kriptográfiai, kulcskezelő szolgáltatás, amely a szövetségi Information Processing Standards (FIPS) ellenőrzött hardveres biztonsági modulokon alapul. Ezek a szabványok segítenek megvédeni a titkosítási kulcsokat és a bizalmas titkokat. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| Azure CLI | [Az Azure CLI](/cli/azure/install-azure-cli) az Azure-erőforrások parancssorból történő kezelésére és felügyeletére van optimalizálva.|
| BitLocker |A [BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) egy iparág által felismert Windows mennyiségi titkosítási technológia, amely lehetővé teszi a lemezes titkosítás használatát a Windows rendszerű virtuális gépeken. |
| Kulcs titkosítási kulcsa (KEK) | Az aszimmetrikus kulcs (RSA 2048), amellyel védetté teheti vagy becsomagolhatja a titkos kulcsot. Megadhatja a hardveres biztonsági modul (HSM) által védett kulcsot vagy szoftveresen védett kulcsot. További információkért tekintse meg a [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dokumentációját, és [hozzon létre és konfiguráljon egy Key vaultot a Azure Disk Encryptionhoz](disk-encryption-key-vault.md). |
| PowerShell-parancsmagok | További információ: [Azure PowerShell parancsmagok](/powershell/azure/). |

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató – Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel ](disk-encryption-cli-quickstart.md)
- [Rövid útmutató – Windows rendszerű virtuális gép létrehozása és titkosítása Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez](disk-encryption-windows.md)
- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started) 
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](disk-encryption-key-vault.md)