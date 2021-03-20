---
title: 'Oktatóanyag: a Microsoft társközi útvonal-szűrőinek konfigurálása – Azure CLI'
description: Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a Microsoft társközi útvonal-szűrőit az Azure CLI használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91975994"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>Oktatóanyag: útválasztási szűrők konfigurálása a Microsoft-partnerek számára: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Az útvonalszűrők lehetővé teszik a támogatott szolgáltatások egy részének felhasználását Microsoft-társviszony-létesítésen keresztül. Az ebben a cikkben ismertetett lépések segítséget nyújtanak a ExpressRoute-áramkörök útválasztási szűrőinek konfigurálásához és kezeléséhez.

Az olyan szolgáltatások, mint az Exchange Online, a SharePoint Online és a Skype vállalati verzió, a Microsoft-partneri kapcsolaton keresztül érhetők el Microsoft 365. Ha a Microsoft-társat egy ExpressRoute-áramkörben konfigurálja, az ezen szolgáltatásokhoz kapcsolódó összes előtag a létrehozott BGP-munkameneteken keresztül lesz meghirdetve. Minden előtaghoz egy BGP-közösségérték van csatolva, amely azonosítja az előtag keretében nyújtott szolgáltatást. A BGP közösségi értékek és a hozzájuk kapcsolódó szolgáltatások listáját itt tekintheti meg: [BGP-Közösségek](expressroute-routing.md#bgp).

Az összes Azure-és Microsoft 365-szolgáltatáshoz való kapcsolódás nagy számú előtagokat okoz a BGP-n keresztül. Az előtagok nagy száma jelentősen megnöveli a hálózaton belüli útválasztók által karbantartott útválasztási táblázatok méretét. Ha azt tervezi, hogy csak a Microsoft-partnerek által kínált szolgáltatások egy részhalmazát szeretné használni, az útválasztási táblázatok méretét kétféleképpen csökkentheti. A következőket teheti:

* Kiszűri a nemkívánatos előtagokat az útválasztási szűrők alkalmazásával a BGP-közösségeken. Az útvonal-szűrés szabványos hálózati gyakorlat, és általában számos hálózaton belül használatos.

* Adja meg az útvonal-szűrőket, és alkalmazza őket a ExpressRoute-áramkörre. Az útválasztási szűrő egy új erőforrás, amellyel kiválaszthatja a Microsoft-társon keresztül használni kívánt szolgáltatások listáját. A ExpressRoute-útválasztók csak az útválasztási szűrőben azonosított szolgáltatásokhoz tartozó előtagok listáját küldik el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - BGP-közösségi értékek beolvasása.
> - Hozzon létre egy útválasztási szűrőt és egy szűrési szabályt.
> - Útvonal-szűrő hozzárendelése egy ExpressRoute-áramkörhöz.

### <a name="about-route-filters"></a><a name="about"></a>Az útválasztási szűrők ismertetése

A Microsoft Edge-útválasztók a ExpressRoute-áramkörön konfigurált, a saját peremhálózati útválasztókkal rendelkező BGP-munkameneteket a kapcsolat szolgáltatóján keresztül hozzák létre. Nincsenek útvonalak meghirdetve a hálózatán. Ha engedélyezni szeretné az útvonalhirdetéseket a hálózaton, társítania kell egy útvonalszűrőt.

Az útvonalszűrőkkel azonosíthatja az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítésén keresztül használni kívánt szolgáltatásokat. Ez lényegében egy engedélyezett lista az összes BGP közösségi értékről. Ha egy ExpressRoute-áramkörhöz egy útvonal-szűrő erőforrás van meghatározva és csatolva van, akkor a BGP közösségi értékekre leképező összes előtag a hálózatra lesz hirdetve.

Ha Microsoft 365-szolgáltatásokkal szeretne útválasztási szűrőket csatolni, rendelkeznie kell a ExpressRoute-en keresztüli Microsoft 365-szolgáltatások használatára vonatkozó engedéllyel. Ha nincs engedélye arra, hogy a ExpressRoute-n keresztül használja Microsoft 365-szolgáltatásokat, az útválasztási szűrők csatlakoztatásának művelete meghiúsul. Az engedélyezési folyamattal kapcsolatos további információkért lásd: [Azure ExpressRoute for Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás előtagja lesz, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő.
> 

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Microsoft-társon keresztül sikeresen csatlakozhasson a szolgáltatásokhoz, a következő konfigurációs lépéseket kell végrehajtania:

* Rendelkeznie kell egy aktív ExpressRoute-áramkörrel, amelyhez Microsoft-társítás van kiépítve. A következő utasításokat követve hajthatja végre ezeket a feladatokat:
  * A folytatás előtt [hozzon létre egy ExpressRoute áramkört](howto-circuit-cli.md) , és engedélyezze az áramkört a kapcsolat szolgáltatója számára. A ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie.
  * [Hozzon létre Microsoft-társat](howto-routing-cli.md) , ha közvetlenül a BGP-munkamenetet kezeli. Vagy a kapcsolat szolgáltatója kiépíti a Microsoft-társat az áramkörhöz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését

A konfiguráció megkezdéséhez jelentkezzen be az Azure-fiókjába. Ha a "kipróbálás" lehetőséget használja, akkor automatikusan bejelentkezett, és kihagyhatja a bejelentkezési lépést. Az alábbi példák segítséget nyújtanak a kapcsolódáshoz:

```azurecli
az login
```

Keresse meg a fiókot az előfizetésekben.

```azurecli-interactive
az account list
```

Válassza ki azt az előfizetést, amelyhez ExpressRoute-áramkört kíván létrehozni.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Az előtagok és a BGP közösségi értékek listájának beolvasása

1. A következő parancsmaggal kérheti le a Microsoft-társon keresztül elérhető szolgáltatásokhoz kapcsolódó BGP közösségi értékek és előtagok listáját:

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. Készítse el az útválasztási szűrőben használni kívánt BGP-közösségi értékek listáját.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Útvonal-szűrő és egy szűrési szabály létrehozása

Egy útvonal-szűrőnek csak egy szabálya lehet, és a szabálynak "Allow" típusúnak kell lennie. Ez a szabály tartalmazhatja a BGP-közösségi értékek listáját. A parancs `az network route-filter create` csak egy útvonal-szűrő erőforrást hoz létre. Az erőforrás létrehozása után létre kell hoznia egy szabályt, és csatolni kell az útválasztási szűrő objektumhoz.

1. Útvonal-szűrő erőforrás létrehozásához futtassa a következő parancsot:

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. Útvonal-szűrő szabály létrehozásához futtassa a következő parancsot:
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Az útvonal-szűrő csatolása ExpressRoute-áramkörhöz

Futtassa az alábbi parancsot az útvonal-szűrő ExpressRoute-áramkörhöz való csatolásához:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Gyakori feladatok

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Egy útvonal szűrő tulajdonságainak beolvasása

Az útválasztási szűrők tulajdonságainak beszerzéséhez használja a következő parancsot:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Egy útvonal szűrő tulajdonságainak frissítése

Ha az útvonal szűrője már csatolva van egy áramkörhöz, a BGP-közösségi lista frissítései automatikusan propagálják az előtagok hirdetményének változásait a létrejött BGP-munkameneten keresztül. A következő paranccsal frissítheti az útválasztási szűrő BGP közösségi listáját:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Útvonal-szűrő leválasztása egy ExpressRoute-áramkörből

Ha egy útvonal-szűrőt leválasztanak a ExpressRoute áramkörről, a BGP-munkameneten belül nem történik meg az előtagok meghirdetése. A következő parancs használatával leválaszthat egy ExpressRoute áramkörből:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Csak akkor törölhet egy útvonal-szűrőt, ha nincs csatlakoztatva egyetlen áramkörhöz sem. A törlés megkísérlése előtt gondoskodjon arról, hogy az útválasztási szűrő ne legyen csatlakoztatva egyetlen áramkörhöz sem. Az útvonal-szűrőt a következő paranccsal törölheti:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Az útválasztó-konfigurációs mintákkal kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Útválasztó-konfigurációs minták az Útválasztás beállításához és kezeléséhez](expressroute-config-samples-routing.md)
