---
title: Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz a Azure Portal-Azure AD használatával
description: Részletes útmutató felügyelt identitás hozzárendeléséhez egy erőforráshoz egy másik erőforráshoz való hozzáféréshez a Azure Portal használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2af718c3555176167eb154b0a718218c42e93dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608296"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Felügyelt identitás-hozzáférés kiosztása erőforráshoz a Azure Portal használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást egy felügyelt identitással, megadhatja a felügyelt identitás hozzáférését egy másik erőforráshoz, ugyanúgy, mint a rendszerbiztonsági tag. Ez a cikk bemutatja, hogyan adhat egy Azure-beli virtuális gépet vagy virtuálisgép-méretezési csoport felügyelt identitás-hozzáférését egy Azure Storage-fiókhoz a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés társítása egy másik erőforráshoz a RBAC használatával

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, például egy [Azure](qs-configure-portal-windows-vm.md) -beli virtuális gépen vagy egy Azure-beli [virtuálisgép-méretezési csoporton](qs-configure-portal-windows-vmss.md):

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) az Azure-előfizetéshez társított fiókkal, amely alatt a felügyelt identitást konfigurálta.

2. Navigáljon ahhoz a kívánt erőforráshoz, amelyen módosítani szeretné a hozzáférés-vezérlést. Ebben a példában egy Azure-beli virtuális gépet adunk hozzá egy Storage-fiókhoz, ezért navigáljon a Storage-fiókhoz.

3. Válassza ki az erőforrás **hozzáférés-vezérlés (iam)** lapját, és válassza a **+ szerepkör-hozzárendelés hozzáadása**elemet. Ezután adja meg a **szerepkört**, **rendeljen hozzá hozzáférést**, és adja meg a megfelelő **előfizetést**. A keresési feltételek területen tekintse meg az erőforrást. Válassza ki az erőforrást, és kattintson a **Mentés**gombra. 

   ![Hozzáférés-vezérlés (IAM) képernyőkép](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>További lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- Az Azure-beli virtuális gépek felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrásokhoz a Azure Portal használatával](qs-configure-portal-windows-vm.md).
- Az Azure virtuálisgép-méretezési csoport felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuálisgép-méretezési csoportokban a Azure Portal használatával](qs-configure-portal-windows-vmss.md).


