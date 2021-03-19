---
title: Azure Functions IP-címei
description: Megtudhatja, hogyan keresheti meg a beérkező és a kimenő IP-címeket a Function apps számára, és hogy mi okoz változást.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 2c248756899459e17082bcab863a4e857b594909
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608231"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Functions IP-címei

Ez a cikk a Function apps IP-címeivel kapcsolatos alábbi fogalmakat ismerteti:

* A Function app által jelenleg használt IP-címek megkeresése.
* Azok a feltételek, amelyek a Function app IP-címeinek módosítását okozzák.
* A Function alkalmazáshoz hozzáférő IP-címek korlátozása.
* Dedikált IP-címek definiálása egy Function alkalmazáshoz.

Az IP-címek a Function apps szolgáltatáshoz vannak társítva, nem az egyes függvényekhez. A bejövő HTTP-kérések nem használhatják a bejövő IP-címet az egyes függvények meghívásához. az alapértelmezett tartománynevet (functionappname.azurewebsites.net) vagy egy egyéni tartománynevet kell használniuk.

## <a name="function-app-inbound-ip-address"></a>Function alkalmazás bejövő IP-címe

Mindegyik Function alkalmazás egyetlen bejövő IP-címmel rendelkezik. Az IP-cím megkeresése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a Function alkalmazáshoz.
3. A **Beállítások** alatt válassza a **Tulajdonságok** elemet. A bejövő IP-cím a **virtuális IP-cím** területen jelenik meg.

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>Function alkalmazás kimenő IP-címei

Minden Function alkalmazáshoz elérhető kimenő IP-címek vannak megadva. A függvények kimenő kapcsolatai, például egy háttér-adatbázis esetében az elérhető kimenő IP-címek egyikét használják a forrás IP-címként. Nem tudja előre, hogy melyik IP-címet fogja használni az adott kapcsolatok. Emiatt a háttér-szolgáltatásnak meg kell nyitnia a tűzfalat a Function alkalmazás összes kimenő IP-címéhez.

A Function app számára elérhető kimenő IP-címek megkeresése:

1. Jelentkezzen be a [Azure erőforrás-kezelőba](https://resources.azure.com).
2. Válassza **az előfizetések > {előfizetése} > providers > Microsoft. Web > helyek** lehetőséget.
3. A JSON panelen keresse meg a helyet egy olyan `id` tulajdonsággal, amely a Function alkalmazás nevében ér véget.
4. Lásd: `outboundIpAddresses` és `possibleOutboundIpAddresses` . 

A készlet `outboundIpAddresses` jelenleg elérhető a Function alkalmazás számára. A készlet olyan `possibleOutboundIpAddresses` IP-címeket tartalmaz, amelyek csak akkor lesznek elérhetők, ha a Function alkalmazás [más díjszabási szintekre van bontva](#outbound-ip-address-changes).

Az elérhető kimenő IP-címek keresésének másik módja a [Cloud Shell](../cloud-shell/quickstart.md)használata:

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

> [!NOTE]
> Ha a használati [csomagon](consumption-plan.md) vagy a [prémium csomagon](functions-premium-plan.md) futó Function alkalmazás skálázható, a kimenő IP-címek új tartománya is hozzárendelhető. Ha bármelyik csomagon fut, előfordulhat, hogy a teljes adatközpontot hozzá kell adnia egy engedélyezési listához.

## <a name="data-center-outbound-ip-addresses"></a>Az adatközpont kimenő IP-címei

Ha hozzá kell adnia a Function apps által használt kimenő IP-címeket egy engedélyezési, egy másik lehetőség, hogy hozzáadja a Function apps adatközpontját (Azure Region) egy engedélyezési. [Letöltheti az összes Azure-adatközpont IP-címeit felsoroló JSON-fájlt](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Ezután keresse meg azt a JSON-kódrészletet, amely arra a régióra vonatkozik, amelyen a Function alkalmazás fut.

A következő JSON-kódrészlet például a Nyugat-Európa engedélyezési hasonló lehet:

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 A fájl frissítésével és az IP-címek változásával kapcsolatos információkért bontsa ki a [Letöltőközpont oldal](https://www.microsoft.com/en-us/download/details.aspx?id=56519) **részletek** szakaszát.

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>Bejövő IP-címek változásai

A bejövő IP-cím a következő **esetekben** változhat:

- Törölje a Function alkalmazást, és hozza létre újra egy másik erőforráscsoporthoz.
- Törölje az utolsó függvény alkalmazást egy erőforráscsoport és egy régió kombinációjában, majd hozza létre újra.
- Törölje a TLS-kötést, például a [tanúsítvány megújítása](../app-service/configure-ssl-certificate.md#renew-certificate)során.

Ha a Function alkalmazás egy használati csomagban [vagy egy](consumption-plan.md) [prémium](functions-premium-plan.md)csomagban fut, akkor a bejövő IP-cím akkor is változhat, ha nem végeztek olyan műveleteket, mint például a [fent felsoroltak](#inbound-ip-address-changes).

## <a name="outbound-ip-address-changes"></a>Kimenő IP-címek változásai

A Function app számára elérhető kimenő IP-címek készlete a következő esetekben változhat:

* Hajtson végre bármilyen műveletet, amely módosíthatja a bejövő IP-címet.
* Változtassa meg a App Service csomag díjszabási szintjét. Az alkalmazás által használható összes lehetséges kimenő IP-cím listája a `possibleOutboundIPAddresses` tulajdonságban található. Lásd: [kimenő IP](#find-outbound-ip-addresses)-címek keresése.

Ha a Function alkalmazás egy használati [tervben](consumption-plan.md) vagy egy [prémium](functions-premium-plan.md)csomagban fut, a kimenő IP-cím akkor is változhat, ha nem végeztek olyan műveleteket, mint például a [fent felsoroltak](#inbound-ip-address-changes).

A kimenő IP-címek változásának szándékos kényszerítéséhez kövesse az alábbi eljárást:

1. A standard és a prémium v2 díjszabási szintjeinek méretezése App Service.

2. Várjon 10 percet.

3. Méretezés vissza, ahol elindította.

## <a name="ip-address-restrictions"></a>IP-címekre vonatkozó korlátozások

Konfigurálhatja azon IP-címek listáját, amelyeknek engedélyezni vagy megtagadni kívánja a hozzáférést egy függvény alkalmazásához. További információ: [Azure app Service statikus IP-korlátozások](../app-service/app-service-ip-restrictions.md).

## <a name="dedicated-ip-addresses"></a>Dedikált IP-címek

Ha a Function alkalmazás statikus, dedikált IP-címeket igényel, több stratégia is megvizsgálható. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>Virtual Network NAT Gateway a kimenő statikus IP-címekhez

A függvények kimenő forgalmának IP-címét egy virtuális hálózati NAT-átjáró használatával irányíthatja a statikus nyilvános IP-címeken keresztüli közvetlen forgalomhoz. Ezt a topológiát [prémium](functions-premium-plan.md)csomagban futtatva használhatja. További információ: [oktatóanyag: Azure functions kimenő IP-cím szabályozása Azure Virtual Network NAT-átjáróval](functions-how-to-use-nat-gateway.md).

### <a name="app-service-environments"></a>App Service-környezetek

A bejövő és kimenő IP-címek teljes vezérléséhez a [app Service-környezetek](../app-service/environment/intro.md) (App Service-csomagok [elkülönített szintje](https://azure.microsoft.com/pricing/details/app-service/) ) használatát javasoljuk. További információ: [app Service Environment IP-címek](../app-service/environment/network-info.md#ase-ip-addresses) és a [Bejövő forgalom vezérlése egy app Service Environment](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).

Annak megállapítása, hogy a függvény alkalmazás fut-e egy App Service Environmentban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Navigáljon a Function alkalmazáshoz.
3. Válassza az **Áttekintés** lapot.
4. A App Service csomag szintje a **app Service terv/árképzési** szinten jelenik meg. A App Service Environment díjszabási szintje **elkülönített**.
 
Másik lehetőségként használhatja a [Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

A App Service Environment `sku` `Isolated` .

## <a name="next-steps"></a>Következő lépések

Az IP-változások gyakori oka az alkalmazások méretezésének változásai. [További információ a Function app skálázásról](functions-scale.md).
