---
title: Rövid útmutató – Azure API Management-példány létrehozása a parancssori felület használatával
description: Hozzon létre egy új Azure API Management Service-példányt az Azure CLI használatával.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 72d1faac02a21f23d46eb992af1d501bca89e71f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688076"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli"></a>Rövid útmutató: új Azure API Management Service-példány létrehozása az Azure CLI használatával

Az Azure API Management (APIM) segít közzétenni az API-kat a külső, a partner- és a belső fejlesztők számára, hogy ki tudják használni az adataikban és szolgáltatásaikban rejlő lehetőségeket. Az API Management a fejlesztők bevonásán, az üzleti elemzéseken, a biztonságon és a védelmen keresztül biztosítja az alapvető kompetenciákat az API-program sikeressé tételéhez. A APIM lehetővé teszi modern API Gateway-átjárók létrehozását és felügyeletét a bárhol üzemeltetett meglévő háttér-szolgáltatásokhoz. További információ: [Áttekintés](api-management-key-concepts.md).

Ez a rövid útmutató ismerteti, hogyan hozhat létre új API Management példányt az az [APIM](/cli/azure/apim) parancsok használatával az Azure CLI-ben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.11.1 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure API Management példányokat, például az összes Azure-erőforrást, egy erőforráscsoporthoz kell telepíteni. Az erőforráscsoportok lehetővé teszik az egymáshoz kapcsolódó Azure-erőforrások rendszerezését és kezelését.

Először hozzon létre egy *myResourceGroup* nevű ERŐFORRÁSCSOPORTOT az USA középső régiójában a következő az [Group Create](/cli/azure/group#az-group-create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Új szolgáltatás létrehozása

Most, hogy már rendelkezik erőforráscsoporthoz, létrehozhat egy API Management Service-példányt. Hozzon létre egyet az az [APIM Create](/cli/azure/apim#az-apim-create) paranccsal, és adja meg a szolgáltatás nevét és a közzétevő részleteit. A szolgáltatás nevének egyedinek kell lennie az Azure-on belül. 

A következő példában a *myapim* használja a szolgáltatás neve. Frissítse a nevet egy egyedi értékre. Az értesítések fogadásához frissítse az API-közzétevő szervezetének nevét és az e-mail-címet is. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Alapértelmezés szerint a parancs létrehozza a példányt a fejlesztői szinten, amely egy gazdaságos megoldás az Azure API Management kiértékeléséhez. Ez a rétegek nem használhatók éles környezetben. További információt az API Management szintjeinek skálázásáról a [frissítés és skálázás](upgrade-and-scale.md) oldalon talál. 

> [!TIP]
> Ezen a szinten 30 és 40 percet is igénybe vehet egy API Management szolgáltatás létrehozása és aktiválása. Az előző parancs azt a `--no-wait` lehetőséget használja, hogy a parancs a szolgáltatás létrehozásakor azonnal visszaadja.

A központi telepítés állapotának ellenőrzéséhez futtassa az az [APIM show](/cli/azure/apim#az-apim-show) parancsot:

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Kezdetben a kimenet a következőhöz hasonló, az `Activating` állapotot mutatja:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Az aktiválás után a szolgáltatás állapota `Online` és a szolgáltatási példány átjáró címe és nyilvános IP-címe. Egyelőre ezek a címek semmilyen tartalmat nem tesznek elérhetővé. Például:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

Ha a API Management Service-példány online állapotban van, készen áll a használatára. Kezdje az Oktatóanyaggal az [első API importálásához és közzétételéhez](import-and-publish.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, az az [Group delete](/cli/azure/group#az-group-delete) paranccsal eltávolíthatja az erőforráscsoportot és a API Management Service-példányt.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az első API importálása és közzététele](import-and-publish.md)
