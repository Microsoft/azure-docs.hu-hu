---
title: Bejövő/kimenő IP-címek
description: Megtudhatja, hogyan használják a bejövő és kimenő IP-címeket a Azure App Service, mikor változnak, és hogyan találhatja meg az alkalmazás címeit.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 1dda487d23c9f955aea8e35d16e5a560a890a173
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834479"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Bejövő és kimenő IP-címek a Azure App Service

[Azure App Service](overview.md) egy több-bérlős szolgáltatás, kivéve a [App Service környezeteket.](environment/intro.md) Azok az alkalmazások, amelyek nem App Service környezetben (nem az Izolált [szinten)](https://azure.microsoft.com/pricing/details/app-service/)osztoznak a hálózati infrastruktúrán más alkalmazásokkal. Ennek eredményeképpen az alkalmazások bejövő és kimenő IP-címei eltérőek, és bizonyos helyzetekben akár változhatnak is.

[App Service környezetek](environment/intro.md) dedikált hálózati infrastruktúrát használnak, így az App Service környezetben futó alkalmazások statikus, dedikált IP-címeket kapnak a bejövő és kimenő kapcsolatokhoz is.

## <a name="how-ip-addresses-work-in-app-service"></a>Az IP-címek App Service

Az App Service-alkalmazások egy App Service-tervben futnak, és App Service-csomagokat az Azure-infrastruktúra (belsőleg webtér) egyik üzembe helyezési egységében telepítik. Minden üzembe helyezési egységhez hozzá van rendelve egy virtuális IP-címkészlet, amely egy nyilvános bejövő IP-címet és egy [kimenő IP-címkészletet tartalmaz.](#find-outbound-ips) Az App Service egységben lévő összes csomag és a bennük futó alkalmazáspéldányok ugyanazt a virtuális IP-címkészletet osztják meg. Az App Service Environment (App Service csomag az Izolált [](https://azure.microsoft.com/pricing/details/app-service/)szinten) esetében a App Service-terv maga az üzembe helyezési egység, ezért a virtuális IP-címek ennek eredményeképpen vannak hozzá dedikáltan kivatva.

Mivel nem lehet áthelyezni egy App Service-tervet az üzembe helyezési egységek között, az alkalmazáshoz rendelt virtuális IP-címek általában változatlanok maradnak, de vannak kivételek.

## <a name="when-inbound-ip-changes"></a>Ha a bejövő IP-cím megváltozik

A horizontálisan felskálált példányok számától függetlenül minden alkalmazás egyetlen bejövő IP-címmel rendelkezik. A bejövő IP-cím a következő műveletek valamelyikének végrehajtásakor változhat:

- Töröljön egy alkalmazást, és hozza létre újra egy másik erőforráscsoportban (az üzembe helyezési egység változhat).
- Törölje az erőforráscsoport és  a régió utolsó kombinációját, majd hozza létre újra (az üzembe helyezési egység változhat).
- Töröljön egy meglévő IP-alapú TLS-/SSL-kötést, például a tanúsítvány megújítása [során (lásd: Tanúsítvány megújítása).](configure-ssl-certificate.md#renew-certificate)

## <a name="find-the-inbound-ip"></a>A bejövő IP-cím megkeresi

Egyszerűen futtassa a következő parancsot egy helyi terminálon:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Statikus bejövő IP-cím lekérte

Előfordulhat, hogy dedikált, statikus IP-címet szeretne az alkalmazáshoz. Statikus bejövő IP-cím le rendeléséhez egy egyéni [tartományt kell biztonságossá lennie.](configure-ssl-bindings.md#secure-a-custom-domain) Ha ténylegesen nincs szüksége TLS-funkciókra az alkalmazás biztonságának biztosítása érdekében, akár önaírt tanúsítványt is feltölthet ehhez a kötéshez. Az IP-alapú TLS-kötések esetén a tanúsítvány maga az IP-címhez van kötve, ezért App Service statikus IP-címet hoz létre annak következtére. 

## <a name="when-outbound-ips-change"></a>Ha a kimenő IP-k megváltoznak

A horizontálisan felskálált példányok számától függetlenül minden alkalmazás adott időpontban meghatározott számú kimenő IP-címmel rendelkezik. Az alkalmazásból kimenő App Service, például egy háttéradatbázis felé irányuló kimenő kapcsolat a kimenő IP-címek egyikét használja forrás IP-címként. A használt IP-cím futásidőben véletlenszerűen van kiválasztva, így a háttérszolgáltatásnak meg kell nyitnia a tűzfalát az alkalmazás összes kimenő IP-címe számára.

Az alkalmazás kimenő IP-címeinek készlete a következő műveletek valamelyikének végrehajtásakor változik:

- Töröljön egy alkalmazást, és hozza létre újra egy másik erőforráscsoportban (az üzembe helyezési egység változhat).
- Törölje az erőforráscsoport és  a régió kombinációban utolsó alkalmazást, majd hozza létre újra (az üzembe helyezési egység változhat).
- Skálázhatja az alkalmazást az alacsonyabb szintek **(Alapszintű,** **Standard** és **Prémium)** és a **Prémium V2** szint között (az IP-címek hozzáadhatók a készlethez, vagy kivonhatók a készletből).

Az alkalmazás által a tarifacsomagtól függetlenül használható összes lehetséges kimenő IP-cím halmazát megkeresheti a tulajdonság megkeresével, vagy a Tulajdonságok panel További kimenő IP-címek mezőjében a `possibleOutboundIpAddresses` Azure Portal.   Lásd: [Kimenő IP-k megkeresve.](#find-outbound-ips)

## <a name="find-outbound-ips"></a>Kimenő IP-k megkeresi

Az alkalmazás által aktuálisan használt kimenő IP-címek Azure Portal kattintson  a Tulajdonságok elemre az alkalmazás bal oldali navigációs sávján. Ezek a Kimenő **IP-címek mezőben vannak felsorolva.**

Ugyanezeket az információkat a következő parancs futtatásával találhatja meg a [Cloud Shell.](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Az alkalmazás _összes_ lehetséges kimenő IP-címének megkereséhez a  tarifacsomagtól függetlenül kattintson a Tulajdonságok elemre az alkalmazás bal oldali navigációs sávján. Ezek az Additional Outbound IP Addresses (További **kimenő IP-címek) mezőben vannak felsorolva.**

Ugyanezeket az információkat a következő parancs futtatásával találhatja meg a [Cloud Shell.](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan korlátozhatja a bejövő forgalmat forrás IP-címek alapján.

> [!div class="nextstepaction"]
> [Statikus IP-korlátozások](app-service-ip-restrictions.md)
