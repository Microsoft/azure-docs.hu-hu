---
title: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálása
description: Az Azure VMware-megoldás erőforrás-szolgáltatójának regisztrálásához szükséges lépések.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 80010a232f80865b20c2e3d953dc1d9d22ece1c6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653167"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Az Azure VMware-megoldás használatához először regisztrálnia kell az erőforrás-szolgáltatót az előfizetésében. További információ az erőforrás-szolgáltatókkal kapcsolatban: [Azure Resource Providers és types](/azure/azure-resource-manager/management/resource-providers-and-types).

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Azure Portal
 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Az Azure Portal menüjében válassza a **Minden szolgáltatás** lehetőséget.

1. A **minden szolgáltatás** mezőben adja meg az **előfizetés** elemet, majd válassza az **előfizetések** lehetőséget.

1. Válassza ki az előfizetést az előfizetés listából a megtekintéshez.

1. Válassza az **erőforrás-szolgáltatók** lehetőséget, és írja be a **Microsoft. AVS** kifejezést a keresésbe. 
 
1. Ha az erőforrás-szolgáltató nincs regisztrálva, válassza a **regisztráció** lehetőséget.
