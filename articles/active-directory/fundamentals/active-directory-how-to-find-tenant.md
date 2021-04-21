---
title: A bérlőazonosító megkeresi – Azure Active Directory
description: Útmutatás a bérlőazonosítók Azure Active Directory meglévő Azure-előfizetéshez való kereséséhez és a bérlőazonosítókhoz való kereséséhez.
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
ms.custom: it-pro, devx-track-azurepowershell
ms.collection: M365-identity-device-management
ms.openlocfilehash: cba823775849fdad8407c7bb697a53761e8ccbcd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764350"
---
# <a name="how-to-find-your-azure-active-directory-tenant-id"></a>A bérlőazonosító Azure Active Directory megkeresve

Az Azure-előfizetések megbízhatósági kapcsolattal Azure Active Directory (Azure AD). Az Azure AD megbízhatóan hitelesíti az előfizetés felhasználóit, szolgáltatásait és eszközeit. Minden előfizetéshez tartozik egy bérlőazonosító, és többféleképpen is megkeresheti az előfizetés bérlőazonosítóját.

## <a name="find-tenant-id-through-the-azure-portal"></a>Bérlőazonosító megkeresi a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
 
1. Válassza a **Azure Active Directory** lehetőséget.

1. Válassza ki a **Tulajdonságok** elemet.

1. Ezután görgessen le a **Bérlőazonosító mezőhöz.** A bérlőazonosító a mezőben lesz.

:::image type="content" source="media/active-directory-how-to-find-tenant/portal-tenant-id.png" alt-text="Azure Active Directory – Tulajdonságok – Bérlőazonosító – Bérlőazonosító mező":::

## <a name="find-tenant-id-with-powershell"></a>Bérlőazonosító megkeresi a PowerShell-t

A bérlőt programozott módon is megkeresheti. A bérlőazonosítót a parancsmaggal Azure PowerShell `Get-AzTenant` meg.

```azurepowershell-interactive
Connect-AzAccount
Get-AzTenant
```
   
További információkért tekintse meg a [Get-AzTenant](/powershell/module/az.accounts/get-aztenant)parancsmagok Azure PowerShell útmutatóját.


## <a name="find-tenant-id-with-cli"></a>Bérlőazonosító megkeresi a CLI-t
Ha parancssori felületen szeretné megtalálni a bérlőazonosítót, ezt az [Azure CLI](/cli/azure/install-azure-cli) vagy a [parancssori felület használatával Microsoft 365 meg.](https://pnp.github.io/cli-microsoft365/) 

Az Azure CLI esetében használja az **az login,** **az az account list**, vagy az az account tenant **list** parancsot az alábbi példában látható módon. Figyelje meg az egyes parancsok kimenetében az egyes előfizetések **tenantId** tulajdonságát.

```azurecli-interactive
az login
az account list
az account tenant list
```

További információt az [az login](/cli/azure/reference-index#az_login) command reference, az az account command reference vagy az az account tenant command reference (az account command reference) az az account command reference [(az](/cli/azure/ext/account/account) account command reference) vagy az az account tenant command reference [(az az account](/cli/azure/ext/account/account/tenant) command reference) oldalon található.


A Microsoft 365 használja a parancsmag bérlőazonosítóját **az** alábbi példában látható módon:
 
```cli
m365 tenant id get
```

További információért tekintse meg a Microsoft 365 [get parancsreferenciát.](https://pnp.github.io/cli-microsoft365/cmd/tenant/id/id-get/)


## <a name="next-steps"></a>Következő lépések

- Új Azure AD-bérlő létrehozásához lásd: [Rövid útmutató: Új](active-directory-access-create-new-tenant.md)bérlő létrehozása a Azure Active Directory.

- Előfizetés bérlőhöz való társításával vagy hozzáadásával kapcsolatos további információkért lásd: Azure-előfizetés társítása vagy hozzáadása a [Azure Active Directory bérlőhöz.](active-directory-how-subscriptions-associated-directory.md)

- Az objektumazonosító keresésének elsajátítása: A felhasználói [objektumazonosító megkeresása.](/partner-center/find-ids-and-domain-names#find-the-user-object-id)
