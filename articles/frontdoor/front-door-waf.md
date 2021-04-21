---
title: 'Oktatóanyag: Webalkalmazások méretezése és védelme a Azure Front Door és Azure Web Application Firewall (WAF) használatával'
description: Ez az oktatóanyag bemutatja, hogyan használhatja Azure Web Application Firewall a Azure Front Door szolgáltatással.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: d315fa5b588c6e5f2e4643ca18626e400e6ca01b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785648"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Oktatóanyag: Webalkalmazások gyors méretezése és védelme a Azure Front Door és Azure Web Application Firewall (WAF) használatával

Számos webalkalmazásban gyors növekedés történt az elmúlt hetekben a COVID-19 miatt. Ezek a webalkalmazások a rosszindulatú adatforgalom hirtelen megszavadását is tapasztalják, beleértve a szolgáltatásmegtagadásos támadásokat is. Hatékonyan felskálálhatja az alkalmazást a forgalom hirtelen meggyorsítása érdekében, és megvédheti magát a támadásoktól: az Azure Front Door-t az Azure WAF-et gyorsítási, gyorsítótárazási és biztonsági rétegként konfigurálhatja a webalkalmazás előtt. Ez a cikk útmutatást nyújt arról, hogyan Azure Front Door Azure WAF-et az Azure-on belül vagy azon kívül futó webalkalmazáshoz konfigurálva. 

Ebben az oktatóanyagban az Azure CLI használatával konfiguráljuk a WAF-et. Ugyanezt elvégezheti a következő használatával: Azure Portal, Azure PowerShell, Azure Resource Manager Azure REST API-k. 

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:
> [!div class="checklist"]
> - Hozzon létre egy Front Door.
> - Azure WAF-szabályzat létrehozása.
> - WAF-szabályzat szabálykészletének konfigurálása.
> - WAF-szabályzat társítása a Front Door.
> - Egyéni tartomány konfigurálása.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- Az oktatóanyagban található utasítások az Azure CLI-t használják. [Az Azure](/cli/azure/get-started-with-azure-cli) CLI használatának első lépésekhez tekintse meg ezt az útmutatót.

  > [!TIP] 
  > Az Azure CLI használatának egyszerű és gyors módja, ha a Basht használja a [Azure Cloud Shell.](../cloud-shell/quickstart.md)

- Győződjön meg arról, `front-door` hogy a bővítmény hozzá van adva az Azure CLI-hez:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Az oktatóanyagban használt parancsokkal kapcsolatos további információkért lásd: [Azure CLI-referencia a Front Door.](/cli/azure/ext/front-door)

## <a name="create-an-azure-front-door-resource"></a>Erőforrás Azure Front Door létrehozása

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: A védeni kívánt alkalmazás teljes tartományneve. Például: `myapplication.contoso.com`.

`--accepted-protocols`: Megadja azokat a protokollokat, Azure Front Door támogatni szeretné a webalkalmazást. Például: `--accepted-protocols Http Https`.

`--name`: A Azure Front Door neve.

`--resource-group`: Az erőforráscsoport, amelybe ezt Azure Front Door erőforrást. Az erőforráscsoportokkal kapcsolatos további információkért lásd: [Erőforráscsoportok kezelése az Azure-ban.](../azure-resource-manager/management/manage-resource-groups-portal.md)

A parancs futtatásakor adott válaszban keresse meg a `hostName` kulcsot. Erre az értékre egy későbbi lépésben lesz szüksége. A `hostName` a létrehozott Azure Front Door DNS-neve.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Azure WAF-profil létrehozása a Azure Front Door való használathoz

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: Az új Azure WAF-szabályzat neve.

`--resource-group`: Az erőforráscsoport, amelybe ezt a WAF-erőforrást szeretné helyezze. 

A fenti CLI-kód létrehoz egy engedélyezett, megelőzési módban elérhető WAF-szabályzatot. 

> [!NOTE] 
> Előfordulhat, hogy észlelési módban szeretné létrehozni a WAF-szabályzatot, és megfigyelni, hogyan észleli és naplózza a kártékony kéréseket (azok blokkolása nélkül), mielőtt a védelmi mód használata előtt dönt.

A parancs futtatásakor adott válaszban keresse meg a `ID` kulcsot. Erre az értékre egy későbbi lépésben lesz szüksége. 

A `ID` mezőnek a következő formátumban kell lennie:

/subscriptions/**előfizetés azonosítója**/resourcegroups/**erőforráscsoport neve**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF-szabályzat neve**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Felügyelt szabálykészletek hozzáadása a WAF-szabályzathoz

Felügyelt szabálykészleteket adhat hozzá a WAF-szabályzathoz. A felügyelt szabálykészlet a Microsoft által épített és kezelt szabálykészlet, amely segít megvédeni a fenyegetések egy osztályát. Ebben a példában két szabálykészletet fogunk hozzáadni:
- Az alapértelmezett szabálykészlet segít megvédeni a gyakori webes fenyegetéseket. 
- A robotvédelmi szabálykészlet, amely segít megvédeni a rosszindulatú robotokat.

Adja hozzá az alapértelmezett szabálykészletet:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

Adja hozzá a robotvédelmi szabálykészletet:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: Az Azure WAF-erőforráshoz megadott név.

`--resource-group`: Az erőforráscsoport, amelybe a WAF-erőforrást helyezte.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>A WAF-szabályzat társítása a Azure Front Door erőforráshoz

Ebben a lépésben a létrehozott WAF-szabályzatot a webalkalmazás előtt Azure Front Door erőforráshoz társítjuk:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: Az erőforráshoz megadott Azure Front Door.

`--resource-group`: Az erőforráscsoport, amelybe Azure Front Door erőforrást.

`--set`: Itt frissíti a Azure Front Door erőforráshoz társított attribútumot `WebApplicationFirewallPolicyLink` `frontendEndpoint` az új WAF-szabályzathoz. Az oktatóanyag korábbi, WAF-profiljának létrehozásakor kapott válaszban meg kell lennie a WAF-szabályzat azonosítójának.

 > [!NOTE] 
> Az előző példa akkor alkalmazható, ha nem egyéni tartományt használ. Ha nem használ egyéni tartományokat a webalkalmazások eléréséhez, kihagyhatja a következő szakaszt. Ebben az esetben a saját erőforrás létrehozásakor kapott adatokat fogja Azure Front Door `hostName` számára. Ezt a `hostName` webalkalmazáshoz használják majd.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Az egyéni tartomány konfigurálása a webalkalmazáshoz

A webalkalmazás egyéni tartományneve az, amit az ügyfelek az alkalmazásra való hivatkozáshoz használhatnak. Például: www.contoso.com. Kezdetben ez az egyéni tartománynév arra a helyre mutatott, ahol a tartományt futtatta, mielőtt Azure Front Door. Miután hozzáadta a Azure Front Door WAF-et az alkalmazás előtt, az egyéni tartománynak megfelelő DNS-bejegyzésnek a Azure Front Door kell lennie. Ezt a változtatást úgy módosíthatja, ha újra leképezi a DNS-kiszolgáló bejegyzését a Azure Front Door erőforrás létrehozásakor feljegyzett `hostName` Azure Front Door elemre.

A DNS-rekordok frissítésének konkrét lépései a DNS-szolgáltatótól függenek. Ha a DNS Azure DNS név gazdagépeként használja, a [DNS-rekord](../dns/dns-operations-recordsets-cli.md) frissítésének lépéseit a dokumentációban találhatja meg, és `hostName` Azure Front Door. 

Fontos megjegyezni, ha arra van szüksége, hogy az ügyfelei a zóna csúcspontján (például a zóna csúcspontján) contoso.com. Ebben az esetben a DNS-név Azure DNS [](../dns/dns-alias.md) és aliasrekordtípusát kell használnia. 

Emellett frissítenie kell a Azure Front Door, [](./front-door-custom-domain.md) hogy az egyéni tartományt hozzá tudja adni, hogy értesülni fog erről a leképezésről.

Végül, ha egyéni tartományt használ a webalkalmazás eléréséhez, és engedélyezni szeretné a HTTPS protokollt. Az egyéni [tartomány tanúsítványait](./front-door-custom-domain-https.md)a következő Azure Front Door. 

## <a name="lock-down-your-web-application"></a>A webalkalmazás zárolása

Javasoljuk, hogy csak az Azure Front Door kommunikáljon a webalkalmazással. Ezzel biztosíthatja, hogy senki ne kerülje meg a Azure Front Door és közvetlenül hozzá tud férni az alkalmazáshoz. A zárolás elvégzéséhez tekintse meg a háttér Hogyan, hogy csak a [Azure Front Door?](./front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az oktatóanyagban használt erőforrásokra, az [az group delete](/cli/azure/group#az_group_delete) paranccsal távolítsa el az erőforráscsoportot, a Front Door és a WAF-szabályzatot:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: Az oktatóanyagban használt összes erőforrás erőforráscsoportja neve.

## <a name="next-steps"></a>Következő lépések

A hibaelhárítással kapcsolatos Front Door tekintse meg a hibaelhárítási útmutatókat:

> [!div class="nextstepaction"]
> [Gyakori útválasztási problémák elhárítása](front-door-troubleshoot-routing.md)