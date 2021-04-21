---
title: Kulcstartó létrehozása és konfigurálása Azure Disk Encryption Windows rendszerű virtuális gépen
description: Ez a cikk a Windows rendszerű virtuális gépeken Azure Disk Encryption kulcstartó létrehozásához és konfigurálásához nyújt lépéseket.
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5e45b242d85d761dcbe3593c59174942ef1a62a5
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749059"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption-on-a-windows-vm"></a>Kulcstartó létrehozása és konfigurálása Azure Disk Encryption Windows rendszerű virtuális gépen

Azure Disk Encryption a Azure Key Vault a lemeztitkosítási kulcsok és titkos kulcsok vezérlésére és kezelésére.  További információ a kulcstartókról: [A](../../key-vault/general/overview.md) kulcstartók Azure Key Vault [a Key Vault biztonságossá tere.](../../key-vault/general/security-overview.md) 

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption Azure AD-val a virtuális gép titkosításához, akkor továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. A részletekért lásd: Kulcstartó létrehozása és konfigurálása Azure Disk Encryption [Azure AD-val (korábbi kiadás).](disk-encryption-key-vault-aad.md)

A kulcstartók létrehozása és konfigurálása a Azure Disk Encryption három lépésből áll:

> [!Note]
> A kötettitkosításhoz való hozzáférés engedélyezéséhez Azure Key Vault hozzáférési szabályzat beállításai között Azure Disk Encryption beállítást. Ha engedélyezte a tűzfalat a kulcstartón, a Kulcstartó Hálózat lapját kell elérnie, és engedélyeznie kell a Hozzáférést a Microsoft megbízható szolgáltatásaihoz. 

1. Erőforráscsoport létrehozása, ha szükséges.
2. Kulcstartó létrehozása. 
3. Kulcstartó speciális hozzáférési szabályzatának beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók mutatják be:

- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-powershell-quickstart.md)

Szükség esetén létrehozhat vagy importálhat kulcstitkosítási kulcsot (KEK).

> [!Note]
> A cikkben található lépések az előfeltételként szükséges parancssori [Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) és az előfeltételként Azure Disk Encryption [PowerShell-szkriptben vannak automatizálva.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

A cikkben található lépések az [Azure CLI,](/cli/azure/)a [Azure PowerShell Az](/powershell/azure/)modul vagy a [Azure Portal.](https://portal.azure.com)

Bár a portál a böngészőn keresztül érhető el, az Azure CLI és a Azure PowerShell helyi telepítést igényel; További [Azure Disk Encryption a Windowshoz: Eszközök telepítése.](disk-encryption-windows.md#install-tools-and-connect-to-azure)

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy a Azure PowerShell előtt csatlakoznia kell az Azure-előfizetéséhez. Ezt az [Azure CLI-val](/cli/azure/authenticate-azure-cli)való bejelentkezéssel, az [Azure PowerShell-rel](/powershell/azure/authenticate-azureps)való bejelentkezéssel, vagy a hitelesítő adatoknak a parancssori Azure Portal meg.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption cli-szkript használata](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption Előfeltételként szükséges PowerShell-szkript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- További [Azure Disk Encryption windowsos](disk-encryption-windows.md) virtuális gépeken
- Útmutató a problémák [Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Olvassa el [a Azure Disk Encryption mint szkripteket](disk-encryption-sample-scripts.md)
