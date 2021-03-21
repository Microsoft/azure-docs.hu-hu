---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 26c158145de6ce729d8a7060152b19fb14b63d58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95555261"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Felkészülés Azure Resource Manager kérelmek hitelesítésére
Az erőforrásokon végrehajtott összes műveletet a Azure Active Directory (AD) [Azure Resource Manager][lnk-authenticate-arm] használatával kell hitelesítenie. A konfigurálásának legegyszerűbb módja a PowerShell vagy az Azure CLI használata.

A folytatás előtt telepítse a [Azure PowerShell-parancsmagokat][lnk-powershell-install] .

A következő lépések bemutatják, hogyan állíthatja be a jelszó-hitelesítést egy AD-alkalmazáshoz a PowerShell használatával. Ezeket a parancsokat egy szabványos PowerShell-munkamenetben futtathatja.

1. Jelentkezzen be az Azure-előfizetésbe a következő parancs használatával:

    ```powershell
    Connect-AzAccount
    ```

1. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba való bejelentkezéssel elérheti a hitelesítő adataihoz társított összes Azure-előfizetést. Használja az alábbi parancsot a használni kívánt Azure-előfizetések listázásához:

    ```powershell
    Get-AzSubscription
    ```

    A következő parancs használatával válassza ki azt az előfizetést, amelyet az IoT hub kezelésére szolgáló parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Jegyezze fel a **TenantId** és a **SubscriptionId**. mert később szüksége lesz rájuk.
3. Hozzon létre egy új Azure Active Directory alkalmazást a következő parancs használatával, és cserélje le a hely birtokosait:
   
   * **{Display Name}:** az alkalmazás megjelenített neve, például **MySampleApp**
   * **{Kezdőlap URL-címe}:** az alkalmazás KEZDŐLAPJÁNAK URL-címe, például **http: \/ /mysampleapp/Home**. Az URL-címnek nem kell valódi alkalmazásra mutatnia.
   * **{Application Identifier}:** Egy egyedi azonosító, például **http: \/ /mysampleapp**. Az URL-címnek nem kell valódi alkalmazásra mutatnia.
   * **{Password}:** Az alkalmazással való hitelesítéshez használt jelszó.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Jegyezze fel a létrehozott alkalmazás **ApplicationId** . Erre később szüksége lesz.
5. Hozzon létre egy új egyszerű szolgáltatásnevet a következő paranccsal, és cserélje le a **{MyApplicationId}** kifejezést az előző lépésben szereplő **ApplicationId** :
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Hozzon létre egy szerepkör-hozzárendelést a következő parancs használatával, és cserélje le a **{MyApplicationId}** kifejezést a **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

Ezzel befejezte az Azure AD-alkalmazás létrehozását, amely lehetővé teszi az egyéni C#-alkalmazásból történő hitelesítést. Az oktatóanyag későbbi részében a következő értékeket kell megadnia:

* TenantId
* SubscriptionId
* ApplicationId
* Jelszó

[lnk-authenticate-arm]: /rest/api/
[lnk-powershell-install]: /powershell/azure/install-az-ps