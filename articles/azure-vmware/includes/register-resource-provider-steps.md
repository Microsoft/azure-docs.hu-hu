---
title: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálása
description: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálásához szükséges lépések.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555923"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Az Azure VMware-megoldás használatához először regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében. További információ az erőforrás-szolgáltatókkal kapcsolatban: [Azure Resource Providers és types](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portál](#tab/azure-portal)
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

1. A **minden szolgáltatás** mezőben adja meg az **előfizetés** elemet, majd válassza az **előfizetések** lehetőséget.

1. Válassza ki az előfizetést az előfizetés listából a megtekintéshez.

1. Válassza az **erőforrás-szolgáltatók** lehetőséget, és írja be a **Microsoft. AVS** kifejezést a keresésbe. 
 
1. Ha az erőforrás-szolgáltató nincs regisztrálva, válassza a **regisztráció** lehetőséget.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatának megkezdéséhez:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Jelentkezzen be az Azure VMware-megoldás üzembe helyezéséhez használt Azure-előfizetésbe az Azure CLI-n keresztül. Regisztrálja az `Microsoft.AVS` erőforrás-szolgáltatót az az [Provider Register](/cli/azure/provider#az_provider_register) paranccsal:

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Az az [Provider List](/cli/azure/provider#az_provider_list) parancs használatával megtekintheti az összes elérhető szolgáltatót.

---


 
