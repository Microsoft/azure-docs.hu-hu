---
title: Bejövő/kimenő IP-címek
description: Megtudhatja, hogyan használja a rendszer a bejövő és kimenő IP-címeket Azure App Serviceban, Mikor változnak, és hogyan keresheti meg az alkalmazás címét.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 4237e51251a7ece05800aa7efa328a9c6cf65e76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104591367"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Bejövő és kimenő IP-címek Azure App Service

[Azure app Service](overview.md) egy több-bérlős szolgáltatás, a [app Service-környezetek](environment/intro.md)kivételével. Azok az alkalmazások, amelyek nem App Service-környezetben (az [elkülönített](https://azure.microsoft.com/pricing/details/app-service/)szinten nem) osztoznak a hálózati infrastruktúrában más alkalmazásokkal. Ennek eredményeképpen az alkalmazás bejövő és kimenő IP-címei eltérőek lehetnek, és bizonyos helyzetekben is változhatnak.

[App Service környezetek](environment/intro.md) dedikált hálózati infrastruktúrát használnak, így a app Service környezetben futó alkalmazások statikus, dedikált IP-címeket kapnak mind a bejövő, mind a kimenő kapcsolatokhoz.

## <a name="how-ip-addresses-work-in-app-service"></a>Az IP-címek működése App Service

Egy App Service alkalmazás egy App Service-csomagban fut, és a App Service csomagok üzembe helyezése az Azure-infrastruktúra egyik üzembe helyezési egységében történik (belsőleg a webtárhelyet nevezik). Minden központi telepítési egységhez hozzá van rendelve egy virtuális IP-cím, amely egy nyilvános bejövő IP-címet és egy [kimenő IP-címet](#find-outbound-ips)tartalmaz. Az azonos üzembe helyezési egységben és a bennük futó alkalmazások példányain lévő összes App Service-csomag ugyanazokat a virtuális IP-címeket használja. Egy App Service Environment (az [elszigetelt](https://azure.microsoft.com/pricing/details/app-service/)szinten lévő app Service-csomag) esetében a app Service terv maga a telepítési egység, így a virtuális IP-címek ennek eredményeképpen vannak kijelölve.

Mivel nem lehet áthelyezni egy App Service tervet a telepítési egységek között, az alkalmazáshoz hozzárendelt virtuális IP-címek általában változatlanok maradnak, de kivételek vannak.

## <a name="when-inbound-ip-changes"></a>Bejövő IP-változások esetén

A kibővített példányok számától függetlenül minden alkalmazás egyetlen bejövő IP-címmel rendelkezik. A bejövő IP-cím a következő műveletek valamelyikének végrehajtásakor változhat:

- Töröljön egy alkalmazást, és hozza létre újra egy másik erőforráscsoporthoz (a telepítési egység változhat).
- Törölje az erőforráscsoport _és_ a régió kombinációjában az utolsó alkalmazást, és hozza létre újból (a központi telepítési egység változhat).
- Töröljön egy meglévő IP-alapú TLS/SSL-kötést, például a tanúsítvány megújítása során (lásd: [tanúsítvány megújítása](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>A bejövő IP-cím keresése

Csak futtassa a következő parancsot egy helyi terminálon:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Statikus bejövő IP-cím beszerzése

Előfordulhat, hogy az alkalmazáshoz dedikált, statikus IP-címet szeretne használni. Statikus bejövő IP-cím beszerzéséhez [védenie kell egy egyéni tartományt](configure-ssl-bindings.md#secure-a-custom-domain). Ha az alkalmazás biztonságossá tételéhez nincs szükség TLS-funkciókra, akkor akár saját aláírású tanúsítványt is feltölthet ehhez a kötéshez. Az IP-alapú TLS-kötésekben a tanúsítvány magához az IP-címhez van kötve, ezért a App Service statikus IP-címet tesz elérhetővé. 

## <a name="when-outbound-ips-change"></a>A kimenő IP-címek változásakor

A kibővített példányok számától függetlenül az egyes alkalmazások megadott számú kimenő IP-címmel rendelkeznek. A App Service alkalmazásból, például egy háttér-adatbázisba irányuló kimenő kapcsolatok a kimenő IP-címek egyikét használják a forrás IP-címként. A használni kívánt IP-cím véletlenszerűen van kiválasztva futásidőben, ezért a háttér-szolgáltatásnak meg kell nyitnia a tűzfalat az alkalmazás összes kimenő IP-címéhez.

Az alkalmazás kimenő IP-címeinek készlete a következő műveletek valamelyikének végrehajtásakor változik:

- Töröljön egy alkalmazást, és hozza létre újra egy másik erőforráscsoporthoz (a telepítési egység változhat).
- Törölje az erőforráscsoport _és_ a régió kombinációjában az utolsó alkalmazást, és hozza létre újból (a központi telepítési egység változhat).
- Az alkalmazás méretezése az alsó szint (**Alapszintű**, **standard** és **prémium**) és a **prémium v2** -szint között (az IP-címek a készletből adhatók hozzá vagy vonhatók ki).

Az alkalmazás által használható kimenő IP-címek készletét az árképzési szintektől függetlenül, a `possibleOutboundIpAddresses` tulajdonság vagy a Azure Portal **Tulajdonságok** paneljének **további kimenő IP-címek** mezőjében találja. Lásd: [kimenő IP](#find-outbound-ips)-címek keresése.

## <a name="find-outbound-ips"></a>Kimenő IP-címek keresése

Az alkalmazás által az Azure Portalban jelenleg használt kimenő IP-címek megkereséséhez kattintson a **Tulajdonságok** elemre az alkalmazás bal oldali navigációs sávján. Ezek a **kimenő IP-címek** mezőben vannak felsorolva.

Ugyanezeket az információkat a következő parancs futtatásával is megtalálhatja a [Cloud Shellban](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Az alkalmazás _összes_ lehetséges kimenő IP-címének megkereséséhez, az árképzési szintektől függetlenül, kattintson a **Tulajdonságok** elemre az alkalmazás bal oldali navigációs sávján. Ezek a **további kimenő IP-címek** mezőben vannak felsorolva.

Ugyanezeket az információkat a következő parancs futtatásával is megtalálhatja a [Cloud Shellban](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan korlátozhatja a bejövő forgalmat forrás IP-címek alapján.

> [!div class="nextstepaction"]
> [Statikus IP-korlátozások](app-service-ip-restrictions.md)
