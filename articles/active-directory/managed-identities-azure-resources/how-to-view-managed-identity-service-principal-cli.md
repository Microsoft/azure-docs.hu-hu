---
title: A szolgáltatásnév Azure CLI-vel egy felügyelt identitás megtekintése
description: Részletes utasítások a szolgáltatásnév Azure CLI-vel egy felügyelt identitás megtekintéséhez.
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
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f379c78113a4edc1efc288617a8a1c205d03552a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60442284"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>A szolgáltatásnév Azure CLI-vel egy felügyelt identitás megtekintése

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés hitelesítő adatait a kódban nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből elsajátíthatja a szolgáltatásnév Azure CLI-vel egy felügyelt identitás megtekintése.

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md).
- Ha még nem rendelkezik Azure-fiók [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Engedélyezése [virtuális gépen a rendszer által hozzárendelt identitással](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) vagy [alkalmazás](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Három lehetősége van a CLI-példaszkriptek futtatásához:
    - Használat [Azure Cloud Shell](../../cloud-shell/overview.md) az Azure Portalon (lásd a következő szakaszban).
    - Használja a beágyazott Azure Cloud Shell-t a "Kipróbálom" gomb, mindegyik blokk jobb felső sarkában található.
    - [Az Azure CLI legújabb verziójának telepítéséhez](https://docs.microsoft.com/cli/azure/install-azure-cli) Ha inkább a helyi CLI-konzollal, és jelentkezzen be Azure-ban `az login`
 
[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="view-the-service-principal"></a>Az egyszerű szolgáltatás megtekintése

A következő parancs bemutatja, hogyan felügyelt identitással engedélyezve van az egyszerű szolgáltatás a virtuális gép vagy az alkalmazás megtekintéséhez. Cserélje le `<VM or application name>` a saját értékeire. 

```azurecli-interactive
az ad sp list --display-name <VM or application name>
```

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel az Azure AD szolgáltatásnevek kezelése további információkért lásd: [az ad sp](/cli/azure/ad/sp).


