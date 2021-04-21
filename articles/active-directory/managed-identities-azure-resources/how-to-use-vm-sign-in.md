---
title: Felügyelt identitások használata Azure-beli virtuális gépen a bejelentkezéshez – Azure ADV
description: Részletes utasítások és példák azure-beli virtuális gép által felügyelt identitások használatára az Azure-erőforrások szolgáltatásnévhez a szkriptek ügyfél-bejelentkezése és erőforrás-hozzáférése esetében.
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
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 59366f1a5b4bd0572af1b36f7be2f5bf91392660
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784784"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-for-sign-in"></a>Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának használata a bejelentkezéshez 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
Ez a cikk PowerShell- és CLI-példaszkprogramokat tartalmaz az Azure-erőforrások szolgáltatásnév felügyelt identitásaival való bejelentkezéshez, valamint útmutatást nyújt az olyan fontos témakörökhöz, mint a hibakezelés.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Ha a cikkben említett Azure PowerShell Azure CLI-példákat használja, győződjön meg arról, hogy a Azure PowerShell vagy az Azure CLI legújabb [verzióját telepíti.](/cli/azure/install-azure-cli) [](/powershell/azure/install-az-ps) 

> [!IMPORTANT]
> - A cikkben található összes példaszkvóta feltételezi, hogy a parancssori ügyfél egy olyan virtuális gépen fut, amelynél engedélyezve vannak az Azure-erőforrások felügyelt identitásai. Használja a virtuális gép "Csatlakozás" funkcióját a Azure Portal, hogy távolról csatlakozzon a virtuális géphez. Az Azure-erőforrások felügyelt identitásának virtuális gépen való engedélyezésével kapcsolatos részletekért lásd: Azure-erőforrások felügyelt identitásának konfigurálása virtuális gépen [az Azure Portal](qs-configure-portal-windows-vm.md)használatával, vagy az egyik változatcikk (a PowerShell, a parancssori felület, egy sablon vagy egy Azure SDK használatával). 
> - Az erőforrás-hozzáférés során előforduló hibák elkerülése érdekében a virtuális gép felügyelt identitásának legalább "Olvasó" hozzáférést kell adni a megfelelő hatókörben (a virtuális gépen vagy magasabb szinten), hogy Azure Resource Manager műveleteket a virtuális gépen. Részletekért [lásd: Azure-erőforrások felügyelt](howto-assign-access-portal.md) identitásának hozzárendelése erőforráshoz való hozzáféréshez a Azure Portal használatával.

## <a name="overview"></a>Áttekintés

Az Azure-erőforrások felügyelt identitása egy szolgáltatásnév-objektumot [biztosít,](../develop/developer-glossary.md#service-principal-object) amely akkor jön létre, amikor engedélyezi az [Azure-erőforrások](overview.md) felügyelt identitását a virtuális gépen. A szolgáltatásnév hozzáférést kaphat az Azure-erőforrásokhoz, és a bejelentkezéshez és az erőforrás-hozzáféréshez szkript-/parancssori ügyfelek identitásként használhatják. Hagyományosan ahhoz, hogy a biztonságos erőforrásokhoz a saját identitása alapján férnek hozzá, a parancsfájl-ügyfélnek a következőre lenne szüksége:  

   - bizalmas/webes ügyfélalkalmazásként kell regisztrálni és jóváhagyást adni az Azure AD-nek
   - jelentkezzen be a szolgáltatásnév alatt az alkalmazás hitelesítő adataival (amelyek valószínűleg be vannak ágyazva a szkriptbe)

Az Azure-erőforrások felügyelt identitása esetében a szkript-ügyfélnek már egyiket sem kell megtennie, mivel be tud jelentkezni az Azure-erőforrások szolgáltatásnév felügyelt identitása alatt. 

## <a name="azure-cli"></a>Azure CLI

A következő szkript a következőket mutatja be:

1. Jelentkezzen be az Azure AD-be a vm felügyelt identitása alatt az Azure-erőforrások szolgáltatásnévvel  
2. Hívja Azure Resource Manager, és szerezze be a virtuális gép szolgáltatásnév-azonosítóját. A CLI automatikusan kezeli a jogkivonatok beszerzését és használatát. Mindenképpen helyettesítse be a virtuális gép nevét a `<VM-NAME>` nevére.  

   ```azurecli
   az login --identity
   
   spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
   echo The managed identity for Azure resources service principal ID is $spID
   ```

## <a name="azure-powershell"></a>Azure PowerShell

A következő szkript a következőket mutatja be:

1. Jelentkezzen be az Azure AD-be a vm felügyelt identitása alatt az Azure-erőforrások szolgáltatásnévvel  
2. A virtuális Azure Resource Manager lekért információkért hívja meg a Azure Resource Manager parancsmagot. A Jogkivonatok használatát a PowerShell automatikusan kezeli.  

   ```azurepowershell
   Add-AzAccount -identity

   # Call Azure Resource Manager to get the service principal ID for the VM's managed identity for Azure resources. 
   $vmInfoPs = Get-AzVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>
   $spID = $vmInfoPs.Identity.PrincipalId
   echo "The managed identity for Azure resources service principal ID is $spID"
   ```

## <a name="resource-ids-for-azure-services"></a>Azure-szolgáltatások erőforrás-nevei

Az [Azure AD-t támogató, az Azure-erőforrások](services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) felügyelt identitásával és azok erőforrás-azonosítóival tesztelt erőforrások listáját az Azure AD-hitelesítést támogató Azure-szolgáltatásokban talál.

## <a name="error-handling-guidance"></a>Hibakezelési útmutató 

Az alábbihoz hasonló válaszok azt jelezheti, hogy a virtuális gép Azure-erőforrásokhoz való felügyelt identitása nincs megfelelően konfigurálva:

- PowerShell: *Invoke-WebRequest: Nem lehet csatlakozni a távoli kiszolgálóhoz*
- CLI: MSI: Nem sikerült lekérni egy jogkivonatot a következő hibával: *`http://localhost:50342/oauth2/token` HTTPConnectionPool(host='localhost', port=50342)* 

Ha ilyen hibaüzenetet kap, térjen vissza az [](https://portal.azure.com) Azure-beli virtuális géphez  a Azure Portal, majd az **Identitás** lapra, és ellenőrizze, hogy a Rendszer által hozzárendelt beállítás "Igen" legyen.

## <a name="next-steps"></a>Következő lépések

- Az Azure-erőforrások felügyelt identitásának Azure-beli virtuális gépen való engedélyezéséhez lásd: Azure-beli virtuális gép felügyelt identitásának konfigurálása Egy Azure-beli virtuális gépen [a PowerShell](qs-configure-powershell-windows-vm.md)használatával, vagy Az Azure-erőforrások felügyelt identitásának konfigurálása Azure-beli virtuális gépen az [Azure CLI használatával](qs-configure-cli-windows-vm.md)
