---
title: 'Rövid útmutató: Azure dedikált HSM létrehozása az Azure CLI-vel'
description: Azure dedikált HSM létrehozása, megjelenítése, listázása, frissítése és törlése az Azure CLI használatával.
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurecli
ms.date: 01/06/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e07bc758b1ef86b3d8c605cbce72f6db564a355f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020961"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-by-using-the-azure-cli"></a>Rövid útmutató: Azure dedikált HSM létrehozása az Azure CLI használatával

Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet egy Azure dedikált HSM-t az az [dedikált-HSM](/cli/azure/ext/hardware-security-modules/dedicated-hsm) Azure CLI bővítmény használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) .
  
  Ha több Azure-előfizetéssel rendelkezik, állítsa be az előfizetést az Azure CLI az [Account set](/cli/azure/account#az_account_set) paranccsal való számlázáshoz.
  
  ```azurecli-interactive
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]  
  
- A dedikált HSM esetében minden követelmény teljesült, beleértve a regisztrációt, a jóváhagyást, valamint a kiépítés során használandó virtuális hálózatot és virtuális gépet. A dedikált HSM-követelményekkel és előfeltételekkel kapcsolatos további információkért lásd [: oktatóanyag: a HSM telepítése meglévő virtuális hálózatra az Azure CLI használatával](tutorial-deploy-hsm-cli.md).
  

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) egy logikai tároló az Azure-erőforrások csoportként történő üzembe helyezéséhez és kezeléséhez. Ha még nem rendelkezik erőforráscsoporthoz a dedikált HSM-hez, hozzon létre egyet az az [Group Create](/cli/azure/group#az_group_create) paranccsal. A következő példában létrehozunk egy nevű erőforráscsoportot `myRG` az `westus` Azure-régióban:

```azurecli-interactive
az group create --name myRG --location westus
```

## <a name="create-a-dedicated-hsm"></a>Dedikált HSM létrehozása

Dedikált HSM létrehozásához használja az az [dedikált-HSM Create](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_create) parancsot. Az alábbi példa egy, `hsm1` a `westus` régióban, az `myRG` erőforráscsoportban és a megadott előfizetésben, a virtuális hálózaton és az alhálózatban MEGnevezett dedikált HSM-t foglal le. A kötelező paraméterek a következők:, `name` `location` és `resource group` .

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

A telepítés elvégzése körülbelül 25 – 30 percet vesz igénybe.

## <a name="get-a-dedicated-hsm"></a>Dedikált HSM beszerzése

A jelenlegi dedikált HSM beszerzéséhez futtassa az az [dedikált-HSM show](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_show) parancsot. Az alábbi példa lekéri a `hsm1` DEDIKÁLT HSM-et az `myRG` erőforráscsoporthoz.

```azurecli-interactive
az dedicated-hsm show --resource-group myRG --name hsm1
```

## <a name="update-a-dedicated-hsm"></a>Dedikált HSM frissítése

Az az [dedikált-HSM Update](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_update) paranccsal frissítheti a dedikált HSM-et. Az alábbi példa frissíti a `hsm1` DEDIKÁLT HSM-et az `myRG` erőforráscsoporthoz, valamint annak címkéit:

```azurecli-interactive
az dedicated-hsm update --resource-group myRG –-name hsm1 --tags resourceType="hsm" Environment="prod" Slice="A"
```

## <a name="list-dedicated-hsms"></a>Dedikált HSM listázása

Futtassa az az [dedikált-HSM List](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_list) paranccsal a jelenlegi dedikált HSM kapcsolatos információk lekéréséhez. Az alábbi példa az erőforráscsoport dedikált HSM sorolja fel `myRG` :

```azurecli-interactive
az dedicated-hsm list --resource-group myRG
```

## <a name="remove-a-dedicated-hsm"></a>Dedikált HSM eltávolítása

Dedikált HSM eltávolításához használja az az [dedikált-HSM delete](/cli/azure/ext/hardware-security-modules/dedicated-hsm#ext_hardware_security_modules_az_dedicated_hsm_delete) parancsot. A következő példa törli a `hsm1` DEDIKÁLT HSM-et az `myRG` erőforráscsoporthoz:

```azurecli-interactive
az dedicated-hsm delete --resource-group myRG –-name hsm1
```

## <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

Ha már nincs szüksége a dedikált HSM-hez létrehozott erőforráscsoporthoz, az az [Group delete](/cli/azure/group#az_group_delete) parancs futtatásával törölheti. Ez a parancs törli a csoportot és az összes erőforrást, beleértve azokat is, amelyek nem kapcsolódnak a dedikált HSM-hez. Az alábbi példa törli az `myRG` erőforráscsoportot és a benne található összeset:

```azurecli-interactive
az group delete --name myRG
```

## <a name="next-steps"></a>Következő lépések

További információ az Azure dedikált HSM-ről: [Azure DEDIKÁLT HSM](overview.md).
