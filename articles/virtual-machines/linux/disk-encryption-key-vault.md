---
title: Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz
description: Ez a cikk a Linux rendszerű virtuális gépeken Azure Disk Encryption kulcstartó létrehozásához és konfigurálásához nyújt lépéseket.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 313f7030d56a8a199c6d2d04fa0d979429d0bce1
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750805"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz

Azure Disk Encryption a Azure Key Vault a lemeztitkosítási kulcsok és titkos kulcsok vezérlésére és kezelésére.  További információ a kulcstartókról: [A](../../key-vault/general/overview.md) kulcstartók Azure Key Vault [a Key Vault biztonságossá tere.](../../key-vault/general/security-overview.md) 

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption Azure AD-val a virtuális gép titkosításához, akkor továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részletekért lásd: Kulcstartó létrehozása és konfigurálása Azure Disk Encryption [Azure AD-val (korábbi kiadás).](disk-encryption-key-vault-aad.md)

A kulcstartók létrehozása és konfigurálása a Azure Disk Encryption három lépésből áll:

1. Erőforráscsoport létrehozása, ha szükséges.
2. Kulcstartó létrehozása. 
3. Kulcstartó speciális hozzáférési szabályzatának beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók mutatják be:

- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-powershell-quickstart.md)

Szükség esetén létrehozhat vagy importálhat kulcstitkosítási kulcsot (KEK).

> [!Note]
> A cikkben található lépések az előfeltételként szükséges parancssori [Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) és az előfeltételként Azure Disk Encryption [PowerShell-szkriptben vannak automatizálva.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

A cikkben található lépések az [Azure CLI,](/cli/azure/)a [Azure PowerShell Az](/powershell/azure/)modul vagy a [Azure Portal.](https://portal.azure.com) 

Bár a portál a böngészőn keresztül érhető el, az Azure CLI és a Azure PowerShell helyi telepítést igényel; További [Azure Disk Encryption Linuxhoz: Eszközök telepítése.](disk-encryption-linux.md#install-tools-and-connect-to-azure)

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy a Azure PowerShell előtt csatlakoznia kell az Azure-előfizetéséhez. Ezt az [Azure CLI-val](/cli/azure/authenticate-azure-cli)való bejelentkezéssel, az [Azure PowerShell-fiókkal](/powershell/azure/authenticate-azureps)való bejelentkezéssel, vagy a hitelesítő adatoknak a parancssori Azure Portal meg.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption cli-szkript használata előfeltételként](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption Előfeltételként szükséges PowerShell-szkript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- További [Azure Disk Encryption Linux rendszerű virtuális gépeken](disk-encryption-linux.md)
- Útmutató a problémák [Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Olvassa el [a Azure Disk Encryption mint szkripteket](disk-encryption-sample-scripts.md)
