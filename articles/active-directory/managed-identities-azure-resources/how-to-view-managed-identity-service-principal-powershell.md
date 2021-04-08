---
title: Felügyelt identitás egyszerű szolgáltatásának megtekintése a PowerShell használatával – Azure AD
description: Részletes útmutató a felügyelt identitások szolgáltatásának a PowerShell használatával történő megtekintéséhez.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 15ccc0faa4d74a2ef95aca00a6257f27b9a209c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91611945"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Felügyelt identitás egyszerű szolgáltatásának megtekintése a PowerShell használatával

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan tekintheti meg egy felügyelt identitás egyszerű szolgáltatását a PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md).
- Ha még nem rendelkezik Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A [rendszerhez rendelt identitás engedélyezése egy virtuális gépen](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) vagy [alkalmazáson](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity).
- A példaként szolgáló szkriptek futtatásához két lehetőség közül választhat:
    - Használja a [Azure Cloud shellt](../../cloud-shell/overview.md), amelyet a kódrészletek jobb felső sarkában található **kipróbálás** gomb használatával nyithat meg.
    - Futtassa helyileg a parancsfájlokat a [Azure PowerShell](/powershell/azure/install-az-ps)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba a használatával `Connect-AzAccount` .

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

Az alábbi parancs bemutatja, hogyan tekintheti meg egy virtuális gép vagy alkalmazás egyszerű szolgáltatását, ha a rendszerhez hozzárendelt identitás engedélyezve van. Cserélje le a `<Azure resource name>` értéket a saját értékeire.

```azurepowershell-interactive
Get-AzADServicePrincipal -DisplayName <Azure resource name>
```

## <a name="next-steps"></a>Következő lépések

Az Azure AD-szolgáltatások PowerShell használatával történő megtekintésével kapcsolatos további információkért lásd: [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).
