---
title: Felügyelt identitások használata az Azure-erőforrások egy Azure virtuális gépen az Azure SDK-k
description: Kódminták az Azure SDK-k használata Azure virtuális gép rendelkezik felügyelt identitások az Azure-erőforrásokhoz.
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
ms.openlocfilehash: 00c86562e0fdb4e6d62d44088b7aba08e45e22a4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60293234"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Felügyelt identitások használata az Azure-erőforrások egy Azure virtuális gépen az Azure SDK-k 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
A cikk ismerteti az SDK-mintákat, amelyek bemutatják, használja a megfelelő Azure SDK-támogatás felügyelt identitások az Azure-erőforrások listáját.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - Az összes kódot minta parancsfájl ebben a cikkben azt feltételezi, hogy a felügyelt identitások az Azure-erőforrások engedélyezve van a virtuális gép fut az ügyfél. Az Azure Portalon a virtuális gép "Csatlakozás" funkció használatával távolról csatlakozzon a virtuális Géphez. Felügyelt identitások a virtuális gép Azure-erőforrások engedélyezésével kapcsolatos részletekért lásd: [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md), vagy az variant cikkekben (PowerShell, CLI, egy sablon vagy az Azure használatával SDK-T). 

## <a name="sdk-code-samples"></a>SDK-Kódminták

| SDK             | Kódminta |
| --------------- | ----------- |
| .NET            | [Felügyelt identitások használatával az Azure-erőforrások Windows rendszerű virtuális gépről egy Azure Resource Manager-sablon üzembe helyezése](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure-szolgáltatás hívása egy Linux rendszerű virtuális gép felügyelt identitások használatával az Azure-erőforrásokhoz](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Felügyelt identitások használatával az Azure-erőforrások-erőforrások kezelése](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Felügyelt identitások az Azure-erőforrások használatával egyszerűen a virtuális Gépen belüli hitelesítéshez](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Erőforrások kezelése a felügyelt identitások az Azure-erőforrások engedélyezve van a virtuális gép](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>További lépések

- Lásd: [Azure SDK-k](https://azure.microsoft.com/downloads/) teljes listája megtalálható az Azure SDK-erőforrásokat, többek között a szalagtár letöltések, dokumentáció és egyéb.
- Engedélyezheti a felügyelt identitások az Azure-erőforrások egy Azure-beli virtuális gépen [konfigurálása felügyelt identitások az Azure-erőforrások a virtuális gép az Azure portal használatával](qs-configure-portal-windows-vm.md).








