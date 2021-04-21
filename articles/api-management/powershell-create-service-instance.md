---
title: Rövid útmutató – Azure API Management-példány létrehozása PowerShell-| Microsoft Docs
description: Hozzon létre egy új Azure API Management-példányt a Azure PowerShell.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: 641f262cf95753bd4c364fa889051a2b8f9d111a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814261"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>Rövid útmutató: Új Azure API Management-szolgáltatáspéldány létrehozása a PowerShell használatával

Az Azure API Management (APIM) segít közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják használni az adataikban és szolgáltatásaikban rejlő lehetőségeket. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. Az APIM segítségével modern API-átjárókat hozhat létre meglévő háttérrendszerekhez, és az üzemeltetés helyétől függetlenül kezelheti azokat. További információ: [Áttekintés](api-management-key-concepts.md).

Ez a rövid útmutató ismerteti az új API Management parancsmagok használatával Azure PowerShell lépéseit.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata között dönt, az oktatóanyaghoz az Azure PowerShell 1.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor a parancsot is futtatnia kell az Azure-ral `Connect-AzAccount` való kapcsolat létrehozásához.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup használatával.](/powershell/module/az.resources/new-azresourcegroup) Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő parancs létrehoz egy *myResourceGroup* nevű erőforráscsoportot az USA nyugati régiója helyen:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>API Management szolgáltatás létrehozása

Most, hogy már van egy erőforráscsoportja, létrehozhat egy API Management szolgáltatáspéldányt. Hozzon létre egyet a [New-AzApiManagement használatával,](/powershell/module/az.apimanagement/new-azapimanagement) és adja meg a szolgáltatás nevét és közzétevői adatait. A szolgáltatás nevének egyedinek kell lennie az Azure-ban.

A következő példában a *szolgáltatásnév a myapim* lesz. Frissítse a nevet egy egyedi értékre. Az értesítések fogadásához frissítse az API-közzétevő szervezetnevét és a rendszergazdai e-mail-címet is.

Alapértelmezés szerint a parancs a Fejlesztői szinten hozza létre a példányt, amely egy gazdaságos lehetőség az Azure-beli API Management. Ez a szint nem használható éles környezetben. További információt az API Management szintjeinek skálázásáról a [frissítés és skálázás](upgrade-and-scale.md) oldalon talál.

> [!NOTE]
> Ez egy hosszú ideig futó művelet. Ezen a szinten 30–40 percet is igénybe API Management szolgáltatás létrehozása és aktiválása.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

Amikor a parancs visszatér, futtassa a [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) parancsot az Azure API Management tulajdonságainak megtekintéséhez. Az aktiválás után a kiépítési állapot Succeeded (Sikeres) lesz, és a szolgáltatáspéldányhoz számos társított URL-cím tartozik. Például:

```azurepowershell-interactive
Get-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" 
```

Példa a kimenetre:

```console
PublicIPAddresses                     : {203.0.113.1}
PrivateIPAddresses                    :
Id                                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.ApiManagement/service/myapim
Name                                  : myapim
Location                              : West US
Sku                                   : Developer
Capacity                              : 1
CreatedTimeUtc                        : 9/9/2020 9:07:43 PM
ProvisioningState                     : Succeeded
RuntimeUrl                            : https://myapim.azure-api.net
RuntimeRegionalUrl                    : https://myapi-westus-01.regional.azure-api.net
PortalUrl                             : https://myapim.portal.azure-api.net
DeveloperPortalUrl                    : https://myapim.developer.azure-api.net
ManagementApiUrl                      : https://myapim.management.azure-api.net
ScmUrl                                : https://myapim.scm.azure-api.net
PublisherEmail                        : admin@contoso.com
OrganizationName                      : Contoso
NotificationSenderEmail               : apimgmt-noreply@mail.windowsazure.com
VirtualNetwork                        :
VpnType                               : None
PortalCustomHostnameConfiguration     :
ProxyCustomHostnameConfiguration      : {myapim.azure-api.net}
ManagementCustomHostnameConfiguration :
ScmCustomHostnameConfiguration        :
DeveloperPortalHostnameConfiguration  :
SystemCertificates                    :
Tags                                  : {}
AdditionalRegions                     : {}
SslSetting                            : Microsoft.Azure.Commands.ApiManagement.Models.PsApiManagementSslSetting
Identity                              :
EnableClientCertificate               :
ResourceGroupName                     : myResourceGroup

```

A API Management szolgáltatáspéldány üzembe helyezése után készen áll a használatára. Kezdje azzal az oktatóanyagmal, amely az első API importálását és [közzétételét teszi közzé.](import-and-publish.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első API importálása és közzététele](import-and-publish.md)
