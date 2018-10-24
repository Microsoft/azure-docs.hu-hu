---
title: Felügyelt identitások az Azure-erőforrások a virtuális gép konfigurálása az Azure SDK használata
description: Lépésről lépésre vonatkozó, konfigurálásával és használatával felügyelt identitások az Azure-erőforrások egy Azure virtuális gépen, az Azure SDK-val.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: daveba
ms.openlocfilehash: 80368c6e6e879478df98d5c0740d60fa59703f2d
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338385"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Felügyelt identitások az Azure-erőforrások egy Azure SDK-val rendelkező virtuális gép konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directory (AD) automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és távolítsa el a felügyelt identitások az Azure-erőforrások egy Azure virtuális gép, egy Azure SDK-val.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Az Azure SDK-k az Azure-erőforrások felügyelt identitások támogatásához 

Az Azure támogatja több programozási platformon keresztül sorozatát [Azure SDK-k](https://azure.microsoft.com/downloads). Ezek a felügyelt identitásokból támogatják az Azure-erőforrásokhoz, és megfelelő mintákat a használatot bemutató frissültek. Ez a lista frissül a kiegészítő támogatás:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Erőforrás kezelése az Azure-erőforrások engedélyezve van a felügyelt identitásokból engedélyezve van egy virtuális gépről](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Tárhely kezelése a felügyelt identitások Azure-erőforrások számára engedélyezve van egy virtuális gépről](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [A rendszer által kiosztott, felügyelt identitás engedélyezve van a virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [A rendszer által kiosztott, felügyelt identitás engedélyezve van a virtuális gép létrehozása](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Azure virtuális gép létrehozása egy rendszer által hozzárendelt identitással engedélyezve](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>További lépések

- Tekintse meg a kapcsolódó cikkeket **egy Azure virtuális gép konfigurálása identitás**, megtudhatja, hogyan használhatja az Azure portal, PowerShell, CLI és erőforrás-sablonok.
