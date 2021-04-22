---
title: 'Rövid útmutató: Azure Dedicated HSM létrehozása az Azure CLI használatával'
description: Azure Dedicated HSM-eket hozhat létre, mutatat be, list használhat, frissíthet és törölhet az Azure CLI használatával.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 80d5bbb54715c5a1a5102f8991f366e273145edc
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868950"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Rövid útmutató: Azure Dedicated HSM létrehozása az Azure CLI használatával

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet Azure Dedicated HSM az [az dedicated-hsm](/cli/azure/dedicated-hsm) Azure CLI-bővítmény használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még [nincs fiókja,](https://azure.microsoft.com/free/) létrehozhat egy ingyenes fiókot.
  
  Ha több Azure-előfizetéssel rendelkezik, állítsa be a számlázáshoz használt előfizetést az Azure CLI [az account set parancsával.](/cli/azure/account#az_account_set)
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- A dedikált HSM-hez szükséges összes követelmény, beleértve a regisztrációt, a jóváhagyást, valamint a kiépítéshez használható virtuális hálózatot és virtuális gépet. A dedikált HSM követelményeivel és előfeltételeivel kapcsolatos további információkért [lásd: Oktatóanyag: HSM-ek](tutorial-deploy-hsm-cli.md)üzembe helyezése meglévő virtuális hálózaton az Azure CLI használatával.
  

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) egy logikai tároló az Azure-erőforrások csoportként való üzembe helyezéséhez és kezeléséhez. Ha még nem rendelkezik erőforráscsoporttal a dedikált HSM-hez, hozzon létre egyet [az az group create paranccsal.](/cli/azure/group#az_group_create) Az alábbi példa egy nevű erőforráscsoportot hoz létre `myRG` az `westus` Azure-régióban:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Dedikált HSM létrehozása

Dedikált HSM létrehozásához használja [az az dedicated-hsm create](/cli/azure/dedicated-hsm#az_dedicated_hsm_create) parancsot. Az alábbi példa egy nevű dedikált HSM-et ad ki a régióban, az erőforráscsoportban és a megadott előfizetésben, virtuális hálózatban és `hsm1` `westus` `myRG` alhálózatban. A kötelező paraméterek a `name` , a és a `location` `resource group` .

```azurecli-interactive
az dedicated-hsm create \
   --resource-group myRG \
   --name "hsm1" \
   --location "westus" \
   --network-profile-network-interfaces private-ip-address="1.0.0.1" \
   --subnet id="/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/hsm-group/providers/Microsoft.Network/virtualNetworks/MyHSM-vnet/subnets/MyHSM-vnet" \
   --stamp-id "stamp1" \
   --sku name="SafeNet Luna Network HSM A790" \
   --tags resourceType="hsm" Environment="test" \
   --zones "AZ1"
```

Az üzembe helyezés körülbelül 25–30 percet vesz igénybe.

## <a name="get-a-dedicated-hsm"></a>Dedikált HSM lekért

Az aktuális dedikált HSM lekért futtatásához futtassa [az az dedicated-hsm show](/cli/azure/dedicated-hsm#az_dedicated_hsm_show) parancsot. Az alábbi példa lekérte `hsm1` az erőforráscsoportban a dedikált HSM-et. `myRG`

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Dedikált HSM frissítése

Dedikált HSM frissítéséhez használja az [az dedicated-hsm update](/cli/azure/dedicated-hsm#az_dedicated_hsm_update) parancsot. Az alábbi példa frissíti az erőforráscsoportban a dedikált `hsm1` HSM-et `myRG` és annak címkéit:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Dedikált HSM-ek felsorolása

Futtassa [az az dedicated-hsm list parancsot](/cli/azure/dedicated-hsm#az_dedicated_hsm_list) az aktuális dedikált HSM-ekkel kapcsolatos információk lekért érdekében. Az alábbi példa az erőforráscsoportban található dedikált `myRG` HSM-eket sorolja fel:

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Dedikált HSM eltávolítása

Dedikált HSM eltávolításához használja [az az dedicated-hsm delete](/cli/azure/dedicated-hsm#az_dedicated_hsm_delete) parancsot. Az alábbi példa törli a dedikált `hsm1` HSM-et az `myRG` erőforráscsoportból:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

Ha már nincs szüksége a dedikált HSM-hez létrehozott erőforráscsoportra, az [az group delete](/cli/azure/group#az_group_delete) parancs futtatásával törölheti. Ez a parancs törli a csoportot és a benne álló összes erőforrást, beleértve azokat is, amelyek nem kapcsolódnak a dedikált HSM-hez. Az alábbi példa törli az `myRG` erőforráscsoportot és mindent, ami benne van:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Következő lépések

További információ a Azure Dedicated HSM: [Azure Dedicated HSM.](overview.md)
