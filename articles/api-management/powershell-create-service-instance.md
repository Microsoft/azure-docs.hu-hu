---
title: Rövid útmutató – Azure API Management-példány létrehozása a PowerShell használatával | Microsoft Docs
description: Hozzon létre egy új Azure API Management-példányt Azure PowerShell használatával.
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/14/2020
ms.author: apimpm
ms.openlocfilehash: eb2c42d26a85a07518a018ba5b8817f13d3cd17f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90707071"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-powershell"></a>Rövid útmutató: új Azure API Management Service-példány létrehozása a PowerShell használatával

Az Azure API Management (APIM) segít közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják használni az adataikban és szolgáltatásaikban rejlő lehetőségeket. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. Az APIM segítségével modern API-átjárókat hozhat létre meglévő háttérrendszerekhez, és az üzemeltetés helyétől függetlenül kezelheti azokat. További információ: [Áttekintés](api-management-key-concepts.md).

Ez a rövid útmutató ismerteti, hogyan hozhat létre új API Management-példányt Azure PowerShell-parancsmagok használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz a Azure PowerShell modul 1,0-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell, `Connect-AzAccount` hogy létrehozza az Azure-hoz való kapcsolódást.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő parancs létrehoz egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az USA nyugati régiójában:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location WestUS
```

## <a name="create-an-api-management-service"></a>API Management szolgáltatás létrehozása

Most, hogy már rendelkezik erőforráscsoporthoz, létrehozhat egy API Management Service-példányt. Hozzon létre egyet a [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) használatával, és adja meg a szolgáltatás nevét és a közzétevő részleteit. A szolgáltatás nevének egyedinek kell lennie az Azure-on belül.

A következő példában a *myapim* használja a szolgáltatás neve. Frissítse a nevet egy egyedi értékre. Az értesítések fogadásához frissítse az API-közzétevő és a rendszergazdai e-mail-cím szervezetének nevét is.

Alapértelmezés szerint a parancs létrehozza a példányt a fejlesztői szinten, amely egy gazdaságos megoldás az Azure API Management kiértékeléséhez. Ez a rétegek nem használhatók éles környezetben. További információt az API Management szintjeinek skálázásáról a [frissítés és skálázás](upgrade-and-scale.md) oldalon talál.

> [!NOTE]
> Ez egy hosszan futó művelet. Ezen a szinten 30 és 40 percet is igénybe vehet egy API Management szolgáltatás létrehozása és aktiválása.

```azurepowershell-interactive
New-AzApiManagement -Name "myapim" -ResourceGroupName "myResourceGroup" `
  -Location "West US" -Organization "Contoso" -AdminEmail "admin@contoso.com" 
```

Ha a parancs visszatér, futtassa a [Get-AzApiManagement](/powershell/module/az.apimanagement/get-azapimanagement) parancsot az Azure API Management szolgáltatás tulajdonságainak megtekintéséhez. Az aktiválás után a kiépítési állapot sikeres volt, és a szolgáltatási példány több társított URL-címmel rendelkezik. Például:

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

A API Management Service-példány üzembe helyezése után készen áll a használatára. Kezdje az Oktatóanyaggal az [első API importálásához és közzétételéhez](import-and-publish.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első API importálása és közzététele](import-and-publish.md)
