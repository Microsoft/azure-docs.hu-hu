---
title: Felügyelt identitás egyszerű szolgáltatásának megtekintése – Azure CLI – Azure AD
description: Részletes útmutató a felügyelt identitások Azure CLI használatával történő megtekintéséhez.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c0f167a36ceff6a9b62e2a3ccf24ca6e13e6294
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91611911"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>Felügyelt identitás egyszerű szolgáltatásának megtekintése az Azure CLI használatával

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést anélkül, hogy hitelesítő adatokat kellene megadnia a kódban. 

Ebből a cikkből megtudhatja, hogyan tekintheti meg egy felügyelt identitás egyszerű szolgáltatását az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md).
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A [rendszerhez rendelt identitás engedélyezése egy virtuális gépen](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) vagy [alkalmazáson](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- A példaként szolgáló szkriptek futtatásához két lehetőség közül választhat:
    - Használja a [Azure Cloud shellt](../../cloud-shell/overview.md), amelyet a kódrészletek jobb felső sarkában található **kipróbálás** gomb használatával nyithat meg.
    - Futtassa helyileg a parancsfájlokat az [Azure CLI](/cli/azure/install-azure-cli)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba az [az login](/cli/azure/reference-index#az-login)használatával. Használjon olyan Azure-előfizetéshez társított fiókot, amelyben erőforrásokat kíván létrehozni.   

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Ez a következő parancs azt mutatja be, hogyan lehet megtekinteni a felügyelt identitással rendelkező virtuális gépek vagy alkalmazások egyszerű szolgáltatását. Cserélje le a `<Azure resource name>` értéket a saját értékeire.

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>Következő lépések

Az Azure AD egyszerű szolgáltatásainak Azure CLI használatával történő kezelésével kapcsolatos további információkért lásd [az az ad SP](/cli/azure/ad/sp)-ben című témakört.
