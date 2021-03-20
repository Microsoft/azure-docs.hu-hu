---
title: SDK használata felügyelt identitások konfigurálásához egy virtuális gépen – Azure AD
description: Részletes útmutató az Azure-beli virtuális gépeken az Azure-erőforrások felügyelt identitásának konfigurálásához és használatához az Azure SDK használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41d1be35d2add24cbbc171c3da3bd4d06bce4337
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359663"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Azure-erőforrások felügyelt identitással rendelkező virtuális gépek konfigurálása Azure SDK használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory (AD) automatikusan felügyelt identitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebből a cikkből megtudhatja, hogyan engedélyezheti és távolíthatja el az Azure-beli virtuális gépekhez tartozó felügyelt identitásokat egy Azure SDK használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDK-k felügyelt identitásokkal az Azure-erőforrások támogatásához 

Az Azure több programozási platformot is támogat az [Azure SDK](https://azure.microsoft.com/downloads)-k sorozatán keresztül. Ezek közül több frissítve lett az Azure-erőforrások felügyelt identitásának támogatásához, és megfelelő mintákat biztosít a használat bemutatásához. A lista további támogatásként való hozzáadásakor frissül:

| SDK | Sample |
| --- | ------ | 
| .NET   | [Erőforrás kezelése egy olyan virtuális gépről, amelyen engedélyezve van a felügyelt identitások használata az Azure-erőforrások számára](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Felügyelt identitással rendelkező virtuális gépek tárterületének kezelése Azure-erőforrásokhoz](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Virtuális gép létrehozása rendszerhez rendelt felügyelt identitással](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Virtuális gép létrehozása rendszerhez rendelt felügyelt identitással](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Azure-beli virtuális gép létrehozása rendszer által hozzárendelt identitással](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az **Azure virtuális gép identitásának konfigurálása** című kapcsolódó cikket, amelyből megtudhatja, hogyan használhatja a Azure Portal, a PowerShell, a CLI és az erőforrás-sablonokat is.
