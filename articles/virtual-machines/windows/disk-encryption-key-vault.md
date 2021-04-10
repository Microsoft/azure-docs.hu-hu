---
title: Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz Windows rendszerű virtuális gépen
description: Ez a cikk a kulcstartó létrehozásának és konfigurálásának lépéseit ismerteti a Windows rendszerű virtuális gépek Azure Disk Encryption való használatához.
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: af48bd74bbc38b1cd9b4d3b0f127e7bdf5d3e037
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555448"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption-on-a-windows-vm"></a>Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz Windows rendszerű virtuális gépen

A Azure Disk Encryption a Azure Key Vault használatával vezérli és kezeli a lemezes titkosítási kulcsokat és titkokat.  További információ a kulcstartókkal kapcsolatban: [Bevezetés a Azure Key Vault használatába](../../key-vault/general/overview.md) és [a kulcstartó biztonságossá tétele](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Ha korábban már használta Azure Disk Encryption az Azure AD-vel egy virtuális gép titkosításához, akkor továbbra is ezt a beállítást kell használnia a virtuális gép titkosításához. További részletekért lásd: [kulcstartó létrehozása és konfigurálása az Azure Disk Encryptionhoz az Azure ad-vel (előző kiadás)](disk-encryption-key-vault-aad.md) .

A Key Vault létrehozásához és konfigurálásához Azure Disk Encryption a következő három lépésből áll:

> [!Note]
> A Azure Key Vault hozzáférési házirend beállításainál válassza a beállítás lehetőséget a kötetek titkosításához Azure Disk Encryption való hozzáférés engedélyezéséhez. Ha engedélyezte a tűzfalat a kulcstartón, lépjen a Key Vault hálózatkezelés lapjára, és engedélyezze a hozzáférést a Microsoft megbízható szolgáltatásaihoz. 

1. Ha szükséges, hozzon létre egy erőforráscsoportot.
2. Kulcstartó létrehozása. 
3. A Key Vault speciális hozzáférési házirendjeinek beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók szemléltetik:

- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](disk-encryption-cli-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](disk-encryption-powershell-quickstart.md)

Ha szeretné, létrehozhat vagy importálhat egy kulcs-titkosítási kulcsot (KEK) is.

> [!Note]
> A cikkben ismertetett lépések a [Azure Disk Encryption előfeltételként megadott parancssori felület parancsfájljában](https://github.com/ejarvi/ade-cli-getting-started) és [Azure Disk Encryption előfeltételek PowerShell-parancsfájlban](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)találhatók.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

A cikkben ismertetett lépések az [Azure CLI](/cli/azure/)-vel, az Azure PowerShell az [module](/powershell/azure/)vagy a [Azure Portal](https://portal.azure.com)használatával végezhetők el.

Amíg a portál elérhető a böngészőben, az Azure CLI és a Azure PowerShell helyi telepítést igényel; a részletekért lásd [: Azure Disk Encryption a Windows rendszerhez: eszközök telepítése](disk-encryption-windows.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy a Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéséhez. Ehhez [Jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli), [Jelentkezzen be az Azure PowerShell](/powershell/azure/authenticate-azureps)-lel, vagy adja meg a hitelesítő adatait a Azure Portalhoz, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Következő lépések

- [Előfeltételként Azure Disk Encryption parancssori felület parancsfájlja](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure Disk Encryption előfeltételek PowerShell-parancsfájl](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Azure Disk Encryption forgatókönyvek megismerése Windows rendszerű virtuális gépeken](disk-encryption-windows.md)
- Tudnivalók a [Azure Disk Encryption hibakereséséről](disk-encryption-troubleshooting.md)
- A [Azure Disk Encryption minta parancsfájljainak](disk-encryption-sample-scripts.md) beolvasása
