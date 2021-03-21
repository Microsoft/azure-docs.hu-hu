---
title: Felügyelt identitások használata Azure-beli virtuális gépen a bejelentkezéshez – Azure ADV
description: Részletes utasítások és példák Azure-beli virtuális gépek által felügyelt identitások használatára az Azure-erőforrások egyszerű üzembe helyezéséhez a parancsfájl-ügyfél bejelentkezéséhez és az erőforrás-hozzáféréshez.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 61e83bd27c9434c4222e0161e3b643b183d1aa84
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99090960"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Azure-erőforrások felügyelt identitásának használata Azure-beli virtuális gépen a bejelentkezéshez 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk PowerShell-és CLI-parancsfájl-példákat tartalmaz a bejelentkezéshez az Azure-erőforrások egyszerű szolgáltatásához felügyelt identitások használatával, valamint útmutatást nyújt a fontos témakörökhöz, például a hibák kezeléséhez.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha a cikkben a Azure PowerShell vagy az Azure CLI-példákat is használni szeretné, telepítse az [Azure PowerShell](/powershell/azure/install-az-ps) vagy az [Azure CLI](/cli/azure/install-azure-cli)legújabb verzióját. 

> [!IMPORTANT]
> - A cikkben szereplő összes minta parancsfájl azt feltételezi, hogy a parancssori ügyfél olyan virtuális gépen fut, amelyen engedélyezve van az Azure-erőforrások felügyelt identitása. Használja a Azure Portal virtuális gép "kapcsolódás" szolgáltatását a virtuális géphez való távoli kapcsolódáshoz. A virtuális gépeken található Azure-erőforrások felügyelt identitásának engedélyezésével kapcsolatos részletekért lásd: [felügyelt identitások konfigurálása egy virtuális gépen az Azure-erőforrások számára a Azure Portal](qs-configure-portal-windows-vm.md)vagy az egyik varianting article (POWERSHELL, CLI, sablon vagy Azure SDK használatával). 
> - Az erőforrás-hozzáférés során felmerülő hibák megelőzése érdekében a virtuális gép felügyelt identitásának legalább "olvasó" hozzáférési jogosultsággal kell rendelkeznie a megfelelő hatókörben (a virtuális gépen vagy magasabb szinten), hogy engedélyezze a virtuális gép Azure Resource Manager műveleteit. További részletekért lásd: [felügyelt identitások társítása az Azure-erőforrásokhoz az erőforrásokhoz való hozzáféréshez a Azure Portal használatával](howto-assign-access-portal.md) .

## <a name="overview"></a>Áttekintés

Az Azure-erőforrások felügyelt identitásai biztosítják a [szolgáltatás egyszerű objektumát](../develop/developer-glossary.md#service-principal-object) , amely a virtuális gépen található [Azure-erőforrások felügyelt identitásának engedélyezésekor jön létre](overview.md) . Az egyszerű szolgáltatás hozzáférést biztosíthat az Azure-erőforrásokhoz, és identitásként használhatja a bejelentkezéshez és az erőforrásokhoz való hozzáféréshez parancsfájl-vagy parancssori ügyfelekként. Hagyományosan a saját identitása alá tartozó biztonságos erőforrások eléréséhez a parancsfájl-ügyfélnek a következőket kell tennie:  

   - regisztrálva kell lennie az Azure AD-ben, bizalmas/webes ügyfélalkalmazásként
   - Jelentkezzen be az egyszerű szolgáltatásnév alá, és használja az alkalmazás hitelesítő adatait (amelyek valószínűleg a szkriptbe vannak beágyazva)

Az Azure-erőforrásokhoz tartozó felügyelt identitások esetében a parancsfájl-ügyfélnek többé nem kell tennie, mert az Azure-erőforrások szolgáltatásnév felügyelt identitások területén tud bejelentkezni. 

## <a name="azure-cli"></a>Azure CLI

Az alábbi szkript a következőket mutatja be:

1. Jelentkezzen be az Azure AD-be a virtuális gép felügyelt identitása Azure-erőforrások egyszerű szolgáltatásához  
2. Hívja meg Azure Resource Manager és szerezze be a virtuális gép egyszerű szolgáltatásnév AZONOSÍTÓját. A CLI gondoskodik a jogkivonat-beszerzések és-használat automatikus kezeléséről. Ügyeljen rá, hogy helyettesítse be a virtuális gép nevét `<VM-NAME>` .  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

Az alábbi szkript a következőket mutatja be:

1. Jelentkezzen be az Azure AD-be a virtuális gép felügyelt identitása Azure-erőforrások egyszerű szolgáltatásához  
2. Hívja meg az Azure Resource Manager parancsmagot a virtuális géppel kapcsolatos információk lekéréséhez. A PowerShell gondoskodik a jogkivonat-használat automatikus kezeléséről.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Az Azure-szolgáltatások erőforrás-azonosítói

Tekintse meg az Azure ad- [hitelesítést támogató Azure-szolgáltatásokat](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) az Azure ad-t támogató erőforrások listájáért, valamint az Azure-erőforrások felügyelt identitásokkal való tesztelését, valamint a hozzájuk tartozó erőforrás-azonosítókat.

## <a name="error-handling-guidance"></a>Hiba a kezelési útmutatóban 

A következő válaszok például azt jelezhetik, hogy a virtuális gép felügyelt identitása nem lett megfelelően konfigurálva az Azure-erőforrásokhoz:

- PowerShell: *meghívó-webkérés: nem lehet csatlakozni a távoli kiszolgálóhoz*
- CLI: *MSI: nem sikerült beolvasni a tokent a `http://localhost:50342/oauth2/token` következő hibával: "HTTPConnectionPool (host =" localhost ", Port = 50342)* 

Ha ezen hibák valamelyike megjelenik, térjen vissza az Azure-beli virtuális géphez a [Azure Portal](https://portal.azure.com) , és nyissa meg az **Identity (identitás** ) lapot, és gondoskodjon róla, hogy a **rendszer hozzárendelve** legyen az "igen" értékre.

## <a name="next-steps"></a>Következő lépések

- Azure-beli virtuális gépen lévő Azure-erőforrások felügyelt identitásának engedélyezéséhez lásd: [felügyelt identitások konfigurálása](qs-configure-powershell-windows-vm.md)Azure-beli virtuális gépen Azure-beli virtuális gépeken a PowerShell használatával, illetve felügyelt identitások konfigurálása Azure-beli virtuális gépeken az Azure [CLI használatával](qs-configure-cli-windows-vm.md)