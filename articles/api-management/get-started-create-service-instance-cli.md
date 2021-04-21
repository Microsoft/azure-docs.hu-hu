---
title: Rövid útmutató – Azure API Management-példány létrehozása a CLI használatával
description: Hozzon létre egy új Azure API Management-szolgáltatáspéldányt az Azure CLI használatával.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 19fc2e1629e7f67063e3cc3eec8cb3707b6dd2e4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775852"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Rövid útmutató: Új Azure API Management-szolgáltatáspéldány létrehozása az Azure CLI használatával

Az Azure API Management (APIM) segít közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják használni az adataikban és szolgáltatásaikban rejlő lehetőségeket. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. Az APIM segítségével modern API-átjárókat hozhat létre és kezelhet meglévő háttérszolgáltatásokhoz, amelyek bárhol üzemeltetve vannak. További információ: [Áttekintés](api-management-key-concepts.md).

Ez a rövid útmutató az új virtuális API Management [az apim parancsokkal](/cli/azure/apim) való létrehozásának lépéseit ismerteti az Azure CLI-n.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.11.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure API Management-példányokat, mint minden Azure-erőforrást, egy erőforráscsoportban kell üzembe helyezni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az USA középső helyén a következő [az group create paranccsal:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Új szolgáltatás létrehozása

Most, hogy már van egy erőforráscsoportja, létrehozhat egy API Management szolgáltatáspéldányt. Hozzon létre egyet az [az apim create paranccsal,](/cli/azure/apim#az_apim_create) és adja meg a szolgáltatás nevét és a közzétevő adatait. A szolgáltatás nevének egyedinek kell lennie az Azure-ban. 

A következő példában a *szolgáltatásnév a myapim* nevet használja. Frissítse a nevet egy egyedi értékre. Frissítse az API-közzétevő szervezetének nevét és e-mail-címét is az értesítések fogadásához. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Alapértelmezés szerint a parancs a Fejlesztői szinten hozza létre a példányt, amely egy gazdaságos lehetőség az Azure-beli API Management. Ez a szint nem használható éles környezetben. További információt az API Management szintjeinek skálázásáról a [frissítés és skálázás](upgrade-and-scale.md) oldalon talál. 

> [!TIP]
> Ezen a szinten 30–40 percet is igénybe API Management szolgáltatás létrehozása és aktiválása. Az előző parancs az kapcsolót használja, így a parancs azonnal visszatér `--no-wait` a szolgáltatás létrehozása közben.

Ellenőrizze az üzemelő példány állapotát az [az apim show parancs futtatásával:](/cli/azure/apim#az_apim_show)

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

A kimenet kezdetben az alábbihoz hasonló, és az állapotot `Activating` mutatja:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Az aktiválás után az állapot lesz, a szolgáltatáspéldány pedig egy átjárócímet és egy `Online` nyilvános IP-címet. Ezek a címek jelenleg nem fednek fel tartalmakat. Például:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Ha a API Management szolgáltatáspéldány online állapotban van, készen áll a használatára. Kezdje azzal az oktatóanyagmal, amely az első API importálását és [közzétételét teszi közzé.](import-and-publish.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolíthatja az erőforráscsoportot és a API Management szolgáltatáspéldányt.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első API importálása és közzététele](import-and-publish.md)
