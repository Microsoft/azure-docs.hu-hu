---
title: Felügyelt identitások használata Azure-beli virtuális gépeken Azure SDK-val – Azure AD
description: Az Azure SDK-k Azure-beli virtuális gépekkel való használatára szolgáló, Azure-erőforrásokhoz felügyelt identitásokkal rendelkező Azure SDK-k használata.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 422b4c63a86904721fc6910193ffa63a8fe46f0a
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184153"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Azure-erőforrások felügyelt identitásának használata Azure-beli virtuális gépeken Azure SDK-k használatával 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk az SDK-minták listáját mutatja be, amely bemutatja az Azure-erőforrások felügyelt identitásának támogatását a megfelelő Azure SDK-ban.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - A cikkben szereplő összes mintakód/szkript azt feltételezi, hogy az ügyfél olyan virtuális gépen fut, amelyen engedélyezve van az Azure-erőforrások felügyelt identitása. Használja a Azure Portal virtuális gép "kapcsolódás" szolgáltatását a virtuális géphez való távoli kapcsolódáshoz. A virtuális gépeken található Azure-erőforrások felügyelt identitásának engedélyezésével kapcsolatos részletekért lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrások számára a Azure Portal](qs-configure-portal-windows-vm.md)vagy az egyik varianting article (POWERSHELL, CLI, sablon vagy Azure SDK használatával). 

## <a name="sdk-code-samples"></a>SDK-kód minták

| SDK             | Kódminta |
| --------------- | ----------- |
| .NET            | [Azure Resource Manager-sablon központi telepítése Windows rendszerű virtuális gépről felügyelt identitások használatával Azure-erőforrásokhoz](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure-szolgáltatások meghívása linuxos virtuális gépről az Azure-erőforrások felügyelt identitásai használatával](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Erőforrások kezelése felügyelt identitások használatával az Azure-erőforrásokhoz](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Felügyelt identitások használata Azure-erőforrásokhoz egyszerűen egy virtuális gépen belülről történő hitelesítéshez](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Felügyelt identitással rendelkező virtuális gépek erőforrásainak kezelése engedélyezett Azure-erőforrások esetén](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>További lépések

- Tekintse meg az Azure SDK-kat az Azure SDK-erőforrások teljes listájához, beleértve a könyvtárak letöltését [, a](https://azure.microsoft.com/downloads/) dokumentációt és egyebeket.
- Az Azure-beli virtuális gépeken található Azure-erőforrások felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrásokhoz a Azure Portal használatával](qs-configure-portal-windows-vm.md).








