---
title: Azure CLI-példaszkret – Batch-fiók létrehozása – felhasználói előfizetés
description: Ez a szkript létrehoz egy Azure Batch-fiókot felhasználói előfizetési módban. Ez a fiók lefoglalja a számítási csomópontokat az előfizetésben.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9bd7b7ac3dbb52ebafa00499e64ec3cff0969a13
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768333"
---
# <a name="cli-example-create-a-batch-account-in-user-subscription-mode"></a>CLI-példa: Batch-fiók létrehozása felhasználói előfizetési módban

Ez a szkript létrehoz egy Azure Batch-fiókot felhasználói előfizetési módban. Az olyan fiókokat, amelyek számítási csomópontokat foglalnak le az előfizetésében, egy Azure Active Directory-tokennel kell hitelesíteni. A lefoglalt számítási csomópontok beleszámítanak az előfizetés vCPU- (mag-) kvótájába. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez az oktatóanyaghoz az Azure CLI 2.0.20-as vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.  

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch/create-account/create-account-user-subscription.sh "Create Account using user subscription")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A következő paranccsal távolítható el az erőforráscsoport és az ahhoz kapcsolódó összes erőforrás.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az role assignment create](/cli/azure/role) | Létrehoz egy új szerepkör-hozzárendelést egy felhasználóhoz, csoporthoz vagy egyszerű szolgáltatáshoz. |
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az keyvault create](/cli/azure/keyvault#az_keyvault_create) | Létrehoz egy kulcstárolót. |
| [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) | Frissíti a megadott kulcstároló biztonsági szabályzatát. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Létrehoz egy Batch-fiókot.  |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Hitelesíti a megadott Batch-fiókot további parancssori felületi interakcióhoz.  |
| [az group delete](/cli/azure/group#az_group_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).
