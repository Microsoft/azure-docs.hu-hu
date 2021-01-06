---
title: Rövid útmutató – Azure Key Vault létrehozása Azure PowerShell
description: Gyors útmutató, amely bemutatja, hogyan hozhat létre Azure Key Vault a Azure PowerShell használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 73d247464f167040c6f7129bdf7014b877317fc5
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936260"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Gyors útmutató: kulcstartó létrehozása a PowerShell használatával

A Azure Key Vault egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a kulcsok, [titkos](../secrets/index.yml) [kódok](../keys/index.yml)és [tanúsítványok](../certificates/index.yml)számára. További információ a Key Vaultről: [About Azure Key Vault](overview.md); a Key vaultban tárolt adatokkal kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](about-keys-secrets-certificates.md).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ebben a rövid útmutatóban egy kulcstartót hoz létre [Azure PowerShell](/powershell/azure/). Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell-modul 1.0.0-as vagy újabb verziójára lesz szükség. `$PSVersionTable.PSVersion`A verzió megkereséséhez írja be a következőt:. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

```azurepowershell-interactive
New-AzResourceGroup -Name 'myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy Key Vault az erőforráscsoporthoz az előző lépésben. A következő információkat kell megadnia:

- Key Vault neve: 3 – 24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.

  > [!Important]
  > Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.

- Erőforráscsoport neve: **myResourceGroup**.
- A hely: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-key-vault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- Tár **neve**: a fenti--Name paraméterhez megadott név.
- Tároló **URI-ja**: a példában ez a https://<az egyedi-kulcstartó-neve>. Vault.Azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, használhatja a Azure PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vaultt a Azure PowerShell használatával. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](overview.md)
- Tekintse meg a [Azure PowerShell Key Vault-parancsmagok](/powershell/module/az.keyvault/) referenciáját
- Tekintse át a [Azure Key Vault biztonsági áttekintést](security-overview.md)

