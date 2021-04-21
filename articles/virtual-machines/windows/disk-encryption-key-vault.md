---
title: Kulcstartó létrehozása és konfigurálása Azure Disk Encryption Windows rendszerű virtuális gépen
description: Ez a cikk a Windows rendszerű virtuális gépeken Azure Disk Encryption kulcstartók létrehozásának és konfigurálásának lépéseit biztosítja.
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b9e61a72af1e2d07fc45a9265de6008fa4e509ef
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813775"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption-on-a-windows-vm"></a>Kulcstartó létrehozása és konfigurálása Azure Disk Encryption Windows rendszerű virtuális gépen

Azure Disk Encryption titkosítási Azure Key Vault a lemeztitkosítási kulcsok és titkos kulcsok vezérlésére és kezelésére.  További információ a kulcstartókról: [A kulcstartók Azure Key Vault](../../key-vault/general/overview.md) a [Kulcstartó biztonságossá biztosítása.](../../key-vault/general/security-features.md) 

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption Azure AD-val a virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához. További részletek: Kulcstartó létrehozása és konfigurálása Azure Disk Encryption [Azure AD-val (előző kiadás).](disk-encryption-key-vault-aad.md)

A kulcstartók létrehozása és konfigurálása a Azure Disk Encryption három lépésből áll:

> [!Note]
> A kötettitkosítási házirend beállításai között Azure Key Vault kell választania, hogy engedélyezze a Azure Disk Encryption titkosításhoz való hozzáférést. Ha engedélyezte a tűzfalat a kulcstartón, akkor a Kulcstartó Hálózat lapra kell belépnie, és engedélyeznie kell a hozzáférést a Microsoft megbízható szolgáltatásaihoz. 

1. Szükség esetén hozzon létre egy erőforráscsoportot.
2. Kulcstartó létrehozása. 
3. Kulcstartó speciális hozzáférési házirendek beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók mutatják be:

- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-powershell-quickstart.md)

Szükség esetén létrehozhat vagy importálhat egy kulcstitkosítási kulcsot (KEK).

> [!Note]
> A cikkben található lépések az előfeltételként szükséges [parancssori Azure Disk Encryption és](https://github.com/ejarvi/ade-cli-getting-started) az előfeltételként Azure Disk Encryption [PowerShell-szkriptben vannak automatizálva.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és csatlakozás az Azure-hoz

A cikkben található lépések az [Azure CLI,](/cli/azure/)a [Azure PowerShell Az](/powershell/azure/)modul vagy a [Azure Portal.](https://portal.azure.com)

Bár a portál elérhető a böngészőben, az Azure CLI és a Azure PowerShell helyi telepítést igényel; További [Azure Disk Encryption windowsos eszközök telepítése: Eszközök](disk-encryption-windows.md#install-tools-and-connect-to-azure) telepítése.

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
- A [Azure Disk Encryption megismeri a Windows rendszerű virtuális gépeken elérhető forgatókönyveket](disk-encryption-windows.md)
- Útmutató a [hibaelhárításhoz Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Olvassa el [a Azure Disk Encryption mint szkripteket](disk-encryption-sample-scripts.md)
