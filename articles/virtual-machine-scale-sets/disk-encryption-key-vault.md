---
title: Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz
description: Ez a cikk a Key Vault létrehozásával és konfigurálásával kapcsolatos lépéseket ismerteti Azure Disk Encryption
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: acd2ae54d81fb508d5f8c02262cf8c2f0f071fb5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016794"
---
# <a name="create-and-configure-a-key-vault-for-azure-disk-encryption"></a>Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz

A Azure Disk Encryption a Azure Key Vault használatával vezérli és kezeli a lemezes titkosítási kulcsokat és titkokat.  További információ a kulcstartókkal kapcsolatban: [Bevezetés a Azure Key Vault használatába](../key-vault/general/overview.md) és [a kulcstartó biztonságossá tétele](../key-vault/general/secure-your-key-vault.md).

A Key Vault létrehozásához és konfigurálásához Azure Disk Encryption a következő három lépésből áll:

1. Ha szükséges, hozzon létre egy erőforráscsoportot.
2. Kulcstartó létrehozása. 
3. A Key Vault speciális hozzáférési házirendjeinek beállítása.

Ezeket a lépéseket az alábbi rövid útmutatók szemléltetik:

Ha szeretné, létrehozhat vagy importálhat egy kulcs-titkosítási kulcsot (KEK) is.

## <a name="install-tools-and-connect-to-azure"></a>Eszközök telepítése és az Azure-hoz való kapcsolódás

A cikkben ismertetett lépések az [Azure CLI](/cli/azure/)-vel, az Azure PowerShell az [module](/powershell/azure/)vagy a [Azure Portal](https://portal.azure.com)használatával végezhetők el.

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Az Azure CLI vagy a Azure PowerShell használata előtt először csatlakoznia kell az Azure-előfizetéséhez. Ehhez [Jelentkezzen be az Azure CLI-vel](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [jelentkezzen be Azure PowerShellkal](/powershell/azure/authenticate-azureps?view=azps-2.5.0), vagy adja meg a hitelesítő adatait a Azure Portalhoz, amikor a rendszer kéri.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>További lépések

- [Azure Disk Encryption áttekintése](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása a Azure PowerShell használatával](disk-encryption-powershell.md)
