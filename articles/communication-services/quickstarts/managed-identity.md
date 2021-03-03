---
title: Felügyelt identitások használata a kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services quickstart
description: A felügyelt identitások lehetővé teszik az Azure kommunikációs szolgáltatások elérésének engedélyezését az Azure-beli virtuális gépeken, a Function apps-ben és más erőforrásokon futó alkalmazásokból.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657620"
---
# <a name="use-managed-identities"></a>Felügyelt identitások használata
Az Azure kommunikációs szolgáltatások használatának első lépései felügyelt identitások használatával. A kommunikációs szolgáltatások identitás-és SMS-ügyféloldali kódtárai támogatják a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/overview.md).

Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti az identitás-és SMS-ügyféloldali kódtárak hozzáférését egy olyan Azure-környezetből, amely támogatja a felügyelt identitásokat. Azt is leírja, hogyan tesztelheti a kódot egy fejlesztési környezetben.

## <a name="prerequisites"></a>Előfeltételek

 - Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free)
 - Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Beállítás

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Felügyelt identitások engedélyezése virtuális gépen vagy app Service-ben

A felügyelt identitásokat engedélyezni kell az Ön által engedélyezett Azure-erőforrásokon. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Azure-szerepkörök kiosztása a Azure Portal

1. Lépjen az Azure Portalra.
1. Navigáljon az Azure kommunikációs szolgáltatás erőforrásához.
1. Navigáljon a Access Control (IAM) menüre – > + Add-> szerepkör-hozzárendelés hozzáadása.
1. Válassza ki a "közreműködő" szerepkört (ez az egyetlen támogatott szerepkör).
1. Válassza ki a "felhasználóhoz rendelt felügyelt identitás" elemet (vagy egy "rendszerhez rendelt felügyelt identitást"), majd válassza ki a kívánt identitást. Mentse a kijelölést.

![Felügyelt identitási szerepkör](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Azure-szerepkörök kiosztása a PowerShell-lel

Szerepkörök és engedélyek a PowerShell használatával történő hozzárendeléséhez lásd: [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása Azure PowerShell használatával](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end