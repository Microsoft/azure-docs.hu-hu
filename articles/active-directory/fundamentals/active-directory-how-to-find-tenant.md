---
title: A bérlői azonosító megkeresése – Azure Active Directory
description: Útmutató a bérlői azonosító megkereséséhez és Azure Active Directoryéhez egy meglévő Azure-előfizetéshez.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, devx-track-azurecli
ms.collection: M365-identity-device-management
ms.openlocfilehash: 910e68e5b8933875f252faf65fb58e514dee8254
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96326590"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>A Azure Active Directory-bérlő AZONOSÍTÓjának megkeresése

Az Azure-előfizetések megbízhatósági kapcsolatban állnak Azure Active Directory (Azure AD) szolgáltatással. Az Azure AD megbízható az előfizetéshez tartozó felhasználók, szolgáltatások és eszközök hitelesítéséhez. Az egyes előfizetésekhez hozzá van rendelve egy bérlői azonosító, és néhány módon megtalálhatja az előfizetés bérlői AZONOSÍTÓját.

## <a name="find-tenant-id-through-the-azure-portal"></a>Bérlő AZONOSÍTÓjának megkeresése a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
 
1. Válassza a **Azure Active Directory** lehetőséget.

1. Válassza ki a **Tulajdonságok** elemet.

1. Ezután görgessen le a **bérlői azonosító** mezőhöz. A bérlő azonosítója a dobozban lesz.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory-Properties-bérlő azonosítója – bérlő azonosítója mező":::

## <a name="find-tenant-id-with-powershell"></a>Bérlő AZONOSÍTÓjának megkeresése a PowerShell-lel

A bérlő programozott módon is megkereshető. A Azure PowerShellrel rendelkező bérlői azonosító megkereséséhez használja a parancsmagot `Get-AzTenant` .

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
További információ a [Get-AzTenant](/powershell/module/az.accounts/get-aztenant)című Azure PowerShell parancsmag-referenciában található.


## <a name="find-tenant-id-with-cli"></a>Bérlői azonosító keresése a parancssori felülettel
Ha parancssori felületen szeretné megkeresni a bérlő AZONOSÍTÓját, ezt megteheti az [Azure CLI](/cli/azure/install-azure-cli) -vel vagy [Microsoft 365 CLI](https://pnp.github.io/cli-microsoft365/)-vel. 

Az Azure CLI esetében az alábbi példában látható módon használja az az **login**, **az Account List** vagy **az az Account bérlő List** parancsot. Figyelje meg az egyes előfizetések **tenantId** tulajdonságát minden egyes parancs kimenetében.

```azurecli-interactive
az login
az account list
az account tenant list
```

További információ: [az login](/cli/azure/reference-index#az_login) parancs referenciája, [az Account](/cli/azure/ext/account/account) Command Reference vagy [az az Account bérlő](/cli/azure/ext/account/account/tenant) command reference.


Microsoft 365 CLI esetében használja a parancsmag **bérlői azonosítóját** az alábbi példában látható módon:
 
```cli
m365 tenant id get
```

További információkért tekintse meg a Microsoft 365 [bérlői azonosító lekérése](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/) parancs referenciáját.


## <a name="next-steps"></a>Következő lépések

- Új Azure AD-bérlő létrehozásához tekintse meg a rövid útmutató [: új bérlő létrehozása a Azure Active Directory-ben](active-directory-access-create-new-tenant.md)című témakört.

- Ha szeretné megismerni, hogyan lehet előfizetést hozzárendelni vagy hozzáadni a bérlőhöz, tekintse meg az [Azure-előfizetés hozzárendelése vagy hozzáadása a Azure Active Directory bérlőhöz](active-directory-how-subscriptions-associated-directory.md)című témakört.

- Az objektumazonosító megkereséséről a [felhasználói objektum azonosítójának megkeresése](/partner-center/find-ids-and-domain-names#find-the-user-object-id)című témakörben olvashat bővebben.