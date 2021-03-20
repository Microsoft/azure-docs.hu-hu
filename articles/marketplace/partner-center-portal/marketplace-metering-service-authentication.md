---
title: Marketplace-mérési szolgáltatás hitelesítési stratégiái | Azure piactér
description: Az Azure Marketplace-en támogatott mérési szolgáltatás-hitelesítési stratégiák.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b418a9cae6f6d58dbe82babcfe6fe1e1a5027d43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97657073"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Marketplace-mérési szolgáltatás hitelesítési stratégiái

A Marketplace-mérési szolgáltatás két hitelesítési stratégiát támogat:

* [Azure AD biztonsági jogkivonat](../../active-directory/develop/access-tokens.md)
* [Felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) 

Megmagyarázjuk, hogy mikor és hogyan használhatók a különböző hitelesítési stratégiák az egyéni mérőszámok biztonságos beküldéséhez a piactér-mérési szolgáltatás használatával.

## <a name="using-the-azure-ad-security-token"></a>Az Azure AD biztonsági jogkivonat használata

A kapcsolódó ajánlati típusok a felügyelhető SaaS-és Azure-alkalmazások, amelyek felügyelt alkalmazáscsomag-típussal rendelkeznek.  

Egyéni mérőszámok elküldése egy előre meghatározott rögzített Azure AD-alkalmazás-azonosító használatával a hitelesítéshez.

SaaS-ajánlatok esetén ez az egyetlen elérhető lehetőség. Az SaaS-ajánlatok közzétételének kötelező lépése az [SaaS-alkalmazás regisztrálása](./pc-saas-registration.md)című témakörben leírtak szerint.

A felügyelt alkalmazási csomaggal rendelkező Azure-alkalmazásokhoz a következő esetekben érdemes megfontolni a stratégia használatát:

* Már rendelkezik egy, a háttér-szolgáltatásokkal folytatott kommunikációhoz szükséges mechanizmussal, és szeretné kiterjeszteni ezt a mechanizmust egy központi szolgáltatásból származó egyéni mérőszámok kibocsátására.
* Összetett egyéni mérőszámokkal rendelkezik.  A felügyelt alkalmazás erőforrásai helyett egy központi helyen futtassa ezt a logikát.

Az alkalmazás regisztrálását követően programozott módon kérhet Azure AD biztonsági jogkivonatot. A közzétevőnek a tokent kell használnia, és el kell végeznie a feloldását.

További információ ezekről a jogkivonatokról: [Azure Active Directory hozzáférési tokenek](../../active-directory/develop/access-tokens.md).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Token beszerzése az Azure AD-alkalmazás alapján

#### <a name="http-method"></a>HTTP-metódus

**UTÁNI**

#### <a name="request-url"></a>*URL-cím kérése*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI-paraméter*

|  **Paraméter neve** |  **Kötelező**  |  **Leírás**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   Igaz         | A regisztrált Azure AD-alkalmazás bérlői azonosítója.   |
| | | |

#### <a name="request-header"></a>*Kérelem fejléce*

|  **Fejléc neve**    |  **Kötelező**  |  **Leírás**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   Igaz         | A kérelemhez társított tartalomtípus. Az alapértelmezett érték `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Kérelem törzse*

|  **Tulajdonság neve**  |  **Kötelező**  |  **Leírás**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   Igaz         | Adja meg a típust. Használja az `client_credentials` parancsot. |
|  `Client_id`        |   Igaz         | Az Azure AD-alkalmazáshoz társított ügyfél/alkalmazás-azonosító.|
|  `client_secret`    |   Igaz         | Az Azure AD-alkalmazáshoz társított titkos kulcs.  |
|  `Resource`         |   Igaz         | A célként megadott erőforrás, amelyre a tokent kérték. Használja az `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` parancsot. |
| | | |

#### <a name="response"></a>*Válasz*

|  **Név**    |  **Típus**  |  **Leírás**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | A kérelem sikeres volt.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Példa a válasz tokenre:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Az Azure által felügyelt identitások token használata

A megfelelő ajánlat típusa a felügyelt alkalmazáscsomag típusú Azure-alkalmazások.

Ezzel a módszerrel engedélyezheti, hogy az üzembe helyezett erőforrások identitása hitelesítse az egyéni mérőórák használati eseményeinek küldését.  Beágyazhatja azt a kódot, amely az üzemelő példány határain belül kibocsátja a használatot.

>[!Note]
>A közzétevőnek biztosítania kell, hogy a használatot kibocsátó erőforrások zárolva legyenek, így nem kerül illetéktelen módosításra.

A felügyelt alkalmazás a Virtual Machinestól a Azure Functionsig különböző típusú erőforrásokat is tartalmazhat.  A különböző szolgáltatások felügyelt identitások használatával történő hitelesítésével kapcsolatos további információkért lásd: [felügyelt identitások használata az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources).

Az alábbi lépéseket követve például Windows rendszerű virtuális gép használatával végezheti el a hitelesítést,

1. Győződjön meg arról, hogy a felügyelt identitás konfigurálva van a metódusok egyikének használatával:
    * [Azure Portal felhasználói felület](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [Parancssori felület](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Azure Resource Manager sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [Rest](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [Azure SDK-k](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Szerezzen be egy hozzáférési jogkivonatot a piactér-mérési szolgáltatásalkalmazás AZONOSÍTÓjának ( `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` ) a rendszeridentitás, az RDP és a virtuális gép között, nyissa meg a PowerShell-konzolt, és futtassa az alábbi parancsot

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. A felügyelt alkalmazás AZONOSÍTÓjának beolvasása az aktuális erőforráscsoportok "többé" tulajdonságával

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 

    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. A piactér-mérési szolgáltatásnak a (z `resourceID` ) és egy `resourceUsageId` felügyelt alkalmazás használatát kell jelentenie.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Használja a [piactér-mérési szolgáltatás API](./marketplace-metering-service-apis.md) -ját a használat kibocsátása érdekében.

## <a name="next-steps"></a>Következő lépések

* [Azure-alkalmazásajánlat létrehozása](../create-new-azure-apps-offer.md)
* [SaaS-ajánlat tervezése](../plan-saas-offer.md)
